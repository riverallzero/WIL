# RuntimeError(CUDA error: no kernel image is available for execution on the device) 해결하기 

먼저 [nvidia](https://developer.nvidia.com/cuda-gpus)에서 GPU의 compute capability 확인 후 TORCH_CUDA_ARCH_LIST에 입력한다.
기존 pytorch를 제거한 후 다시 install 해준다.

- ```export TORCH_CUDA_ARCH_LIST=8.6```
- conda: ```conda uninstall pytorch```
- linux: ```pip uninstall torch```
- ```conda install pytorch torchvision torchaudio cudatoolkit=11.1 -c pytorch -c nvidia```
