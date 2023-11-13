# Gradio 임시 파일저장소 설정하기

gradio로 만든 웹을 실행하고 이미지를 input으로 넣어주었을 때 아래와 같이 /tmp/gradio 폴더에 접근이 제한되었다.

```PermissionError: [Errno 13] Permission denied: '/tmp/gradio/1431b381eb884e1b70a58761238008cfb84fee59'```

접근 권한을 줄수도 있지만, 관리자로 포함되어있지 않을때는 임시 저장소를 다른 경로로 설정해주면 된다.
os.environ에 "GRADIO_TEMP_DIR"을 직접 명시해준다.

```python
import os

os.environ["GRADIO_TEMP_DIR"] = "./gradio"
```
