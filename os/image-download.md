# dataframe 열에 위치한 경로로 이미지 다운받기

- ```img_url = https://www.bigdatahub.go.kr/images/disease/f1_37(2).jpg```
- 주소에서 이미지 이름 추출: ```os.path.basename(img_url)```
- ```iter_content```로  요청/응답 데이터를 반복하여 대용량 응답을 위해 콘텐츠를 한 번에 메모리로 읽는 것을 방지
  
```python
import pandas as pd
import requests
import os

df = pd.read_csv('data.csv')

def download_image(row):
    img_url = row['Img_Path']
    img_name = os.path.basename(img_url)
    img_path = os.path.join('data', img_name)

    response = requests.get(img_url, stream=True, verify=False)

    if response.status_code == 200:
        block_size = 1024

        with open(img_path, 'wb') as file:
            for data in response.iter_content(block_size):
                file.write(data)

        print(f"이미지 다운로드 완료: {img_path}")
    else:
        print(f"이미지 다운로드 실패: {img_url}")

if not os.path.exists('data'):
    os.makedirs('data')

df.apply(download_image, axis=1)
```
