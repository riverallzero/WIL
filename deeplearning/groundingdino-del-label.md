# GroundingDINO에서 라벨 박스를 제거해 바운딩 박스만 남기기

![](https://github.com/riverallzero/WIL/assets/93754504/1e2bd15e-6592-47fe-8c38-f23292ff1fd8) | ![](https://github.com/riverallzero/WIL/assets/93754504/a3f1ba63-a29b-4b37-b166-d1195f3c8d39) |
---| ---|

## GroundingDINO/util/inference.py
여러 요소를 검출할때 라벨 박스에 가리기도하고 조금 지저분하다고 느껴져 바운딩 박스만 남기기로 하였다. 
추론 부분에서 pharse, logit을 출력하지 않고, 박스를 추출한 부분에서 바로 rectangle을 그렸다. 
라벨 박스를 제거한 덕분에 소요 시간도 살짝 줄었다.

### before
```python
def annotate(image_source: np.ndarray, boxes: torch.Tensor, logits: torch.Tensor, phrases: List[str]) -> np.ndarray:
    h, w, _ = image_source.shape
    boxes = boxes * torch.Tensor([w, h, w, h])
    xyxy = box_convert(boxes=boxes, in_fmt="cxcywh", out_fmt="xyxy").numpy()
    detections = sv.Detections(xyxy=xyxy)

    labels = [
        f"{phrase} {logit:.2f}"
        for phrase, logit
        in zip(phrases, logits)
    ]

    box_annotator = sv.BoxAnnotator()
    annotated_frame = cv2.cvtColor(image_source, cv2.COLOR_RGB2BGR)
    annotated_frame = box_annotator.annotate(scene=annotated_frame, detections=detections, labels=labels)
    return annotated_frame
```

```python
 annotated_frame = annotate(image_source=image_source, boxes=boxes, logits=logits, phrases=phrases)
```

### after
```python
def annotate(image_source: np.ndarray, boxes: torch.Tensor) -> np.ndarray:
    h, w, _ = image_source.shape
    boxes = boxes * torch.Tensor([w, h, w, h])
    xyxy = box_convert(boxes=boxes, in_fmt="cxcywh", out_fmt="xyxy").numpy()

    # Return the annotated image with only bounding boxes
    annotated_frame = cv2.cvtColor(image_source, cv2.COLOR_RGB2BGR)
    for box in xyxy:
        box = box.astype(int)
        cv2.rectangle(annotated_frame, (box[0], box[1]), (box[2], box[3]), (0, 255, 0), 2)

    return annotated_frame
```

```python
 annotated_frame = annotate(image_source=image_source, boxes=boxes)
```
