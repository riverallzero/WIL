# ResNet50 backbone을 EfficientNet-b4로 바꾸기

## 분석
SeqDeepFake의 베이스라인 모델은 backbone이 resnet50으로 구성되어있다. 
정확도가 48% 밖에 나오지 않아 모델을 수정해보고자 했다.
아직 자유자재로 모델을 튜닝할 실력이 되지 않았고, backbone을 다른 모델로 바꿔보면 어떨까라는 생각이 들었다.
딥러닝 네트워크 아키텍처 중에서 해커톤 대회에서 사용해보았던 efficientNet-b4를 이용해보고자 한다.
단순하게 pretained 모델만 가져와 바꾸면 되지 않을까라고 생각했지만, 생각보다 어려웠다.
일단 ResNet과 EfficientNet의 차이를 분석해야한다.

**ResNet**은 잔차 연결을 통해 깊은 네트워크를 학습하는데 도움을 주는 아키텍처로, 'skip connection' 또는 'shortcut connection'이라고도 불리는 이 잔차 연결은 이전 레이어의 출력을 후속 레이어의 입력에 직접 추가하는 방식으로 작동한다.
이러한 연결은 그래디언트가 잘 흐르도록 하여 더 깊은 네트워크에서도 그래디언트 소실 문제를 줄여준다.
따라서 ResNet에서는 이러한 잔차 연결들을 더해주는 레이어, 즉 return layer가 사용된다.
또한 일반적인 Batch Normalization을 사용하면 학습 동안의 배치별 통계 변화로 인해 불안정한 학습이나 그래디언트 소실 문제가 발생할 수 있다.
따라서 이 코드에서는 일반적인 Batch Normalization 대신에 고정된 통계값으로 사용하기 위해 FrozenBatchNorm2d라는 클래스를 따로 구현했다.

한편, **EfficientNet**은 네트워크 깊이, 너비 및 해상도를 조절하여 여러 가지 크기의 모델을 생성하는데 중점을 둔 최적화된 아키텍처이다.
'compound scaling'이라는 개념을 도입하여 네트워크의 규모를 증가시키는 동시에 네트워크 깊이, 너비 및 해상도를 균형있게 조절한다. 
이렇게 함으로써 보다 효율적으로 모델의 성능을 향상시키는 데 도움이 된다. 
EfficientNet은 ResNet과 다르게 각 레이어가 그 자체로 유용한 표현을 학습하므로, ResNet에서처럼 명시적인 return layer가 필요하지 않은 것이다.

따라서 FrozenBatchNorm2d 클래스와 return layer를 없애고, num_channels 또한 알맞게 변경해 모델을 수정하였다. 그 결과 test 48%에서 52%로 정확도가 상승하였다.

## Resnet
```python
class FrozenBatchNorm2d(torch.nn.Module):
    def __init__(self, n):
        super(FrozenBatchNorm2d, self).__init__()
        self.register_buffer("weight", torch.ones(n))
        self.register_buffer("bias", torch.zeros(n))
        self.register_buffer("running_mean", torch.zeros(n))
        self.register_buffer("running_var", torch.ones(n))

    def _load_from_state_dict(self, state_dict, prefix, local_metadata, strict,
                              missing_keys, unexpected_keys, error_msgs):
        num_batches_tracked_key = prefix + 'num_batches_tracked'
        if num_batches_tracked_key in state_dict:
            del state_dict[num_batches_tracked_key]

        super(FrozenBatchNorm2d, self)._load_from_state_dict(
            state_dict, prefix, local_metadata, strict,
            missing_keys, unexpected_keys, error_msgs)

    def forward(self, x):
        # move reshapes to the beginning
        # to make it fuser-friendly
        w = self.weight.reshape(1, -1, 1, 1)
        b = self.bias.reshape(1, -1, 1, 1)
        rv = self.running_var.reshape(1, -1, 1, 1)
        rm = self.running_mean.reshape(1, -1, 1, 1)
        eps = 1e-5
        scale = w * (rv + eps).rsqrt()
        bias = b - rm * scale
        return x * scale + bias
    
    
class BackboneBase(nn.Module):
    def __init__(self, backbone: nn.Module, train_backbone: bool, num_channels: int, return_interm_layers: bool):
        super().__init__()
        for name, parameter in backbone.named_parameters():
            if not train_backbone or 'layer2' not in name and 'layer3' not in name and 'layer4' not in name:
                parameter.requires_grad_(False)
        if return_interm_layers:
            return_layers = {"layer1": "0", "layer2": "1", "layer3": "2", "layer4": "3"}
        else:
            return_layers = {'layer4': "0"}
        self.body = IntermediateLayerGetter(backbone, return_layers=return_layers)
        self.num_channels = num_channels

    def forward(self, tensor_list: NestedTensor):
        xs = self.body(tensor_list.tensors)
        out: Dict[str, NestedTensor] = {}
        for name, x in xs.items():
            m = tensor_list.mask
            assert m is not None
            mask = F.interpolate(m[None].float(), size=x.shape[-2:]).to(torch.bool)[0]
            out[name] = NestedTensor(x, mask)
        return out

    
class Backbone(BackboneBase):
    def __init__(self, name: str,
                 train_backbone: bool,
                 return_interm_layers: bool,
                 dilation: bool,
                 Frozen_BatchNorm2d: bool):
        if Frozen_BatchNorm2d:
            backbone = getattr(torchvision.models, name)(
                replace_stride_with_dilation=[False, False, dilation],
                pretrained=is_main_process(), norm_layer=FrozenBatchNorm2d)
        else:
            backbone = getattr(torchvision.models, name)(
                replace_stride_with_dilation=[False, False, dilation],
                pretrained=is_main_process(), norm_layer=nn.BatchNorm2d)
        num_channels = 2048
        super().__init__(backbone, train_backbone, num_channels, return_interm_layers)


```

## EfficientNet
```python
class BackboneBase(nn.Module):
    def __init__(self, backbone: nn.Module, train_backbone: bool, num_channels: int):
        super().__init__()
        if not train_backbone:
            for parameter in self.backbone.parameters():
                parameter.requires_grad_(False)
        self.backbone = backbone
        self.num_channels = num_channels

    def forward(self, tensor_list: NestedTensor):
        xs = {'0': self.backbone.extract_features(tensor_list.tensors)}
        out: Dict[str, NestedTensor] = {}
        for name, x in xs.items():
            m = tensor_list.mask
            assert m is not None
            mask = F.interpolate(m[None].float(), size=x.shape[-2:]).to(torch.bool)[0]
            out[name] = NestedTensor(x, mask)
        return out

    
class Backbone(BackboneBase):
    def __init__(self, train_backbone: bool):
        backbone = EfficientNet.from_pretrained('efficientnet-b4')
        num_channels = 1792

        super().__init__(backbone, train_backbone, num_channels)
```
