# 특정 폴더에 있는 모든 파일과 폴더 & 파일 & 폴더 출력하기

```python
import os

folder_path = 'FOLDER'
folder_all = os.listdir(folder_path)
folder_files = [f for f in os.listdir(folder_path) if os.path.isfile(os.path.join(folder_path, f))]
folder_dirs = [f for f in os.listdir(folder_path) if os.path.isdir(os.path.join(folder_path, f))]

print('폴더에 있는 모든 폴더와 파일', folder_all)
print('폴더에 있는 모든 파일',folder_files)
print('폴더에 있는 모든 폴더',folder_dirs)
```

## 결과

- FOLDER의 구조
    ```
    📁FOLDER
    ├── README.md
    ├── main.py
    ├── requirements.txt
    └── data
        ├── 2023
           ├── 10.csv
           ├── 11.csv
           └── 12.csv
    ```
  
- folder_all: ```['README.md', 'main.py', 'requirements.txt', 'data']```
- folder_files: ```['README.md', 'main.py', 'requirements.txt']```
- folder_dirs:  ```['data']```