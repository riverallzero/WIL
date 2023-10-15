# 폴더 확인 후 생성하기

- 기존: 코드를 실행하면 파일 저장할 때 폴더를 직접 입력해줘야한다.
- 코드: 폴더명을 입력해 존재하지 않는다면 경로를 만드는 간단한 효율적인 코드이다.

```python
import os

output_dir = 'output'
if not os.path.exists(output_dir):
    os.makedirs(output_dir)
```

만약 이 폴더에 파일을 저장하고 싶다면 아래와 같이 실행하면 된다.(예: csv파일)

```python
 df.to_csv(os.path.join(output_dir, 'output.csv'), index=False)
```
