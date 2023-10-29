# Package 설치할때 모든 질문에 Y 입력하기

패키지 설치하는 관리자 권한이 필요한 작업을 진행할 때 sudo 명령어를 이용해서 관리자 권한을 획득한다.
Jupyter Notebook환경에서 ```!sudo apt install <package_name>``` 실행 시 ```Do you want to continue? [Y/n]```이라는 문구가 뜨게 되는데, 이를 입력할 수가 없다.
따라서 아래와 같이 echo를 사용해 모든 질문에 'Y'를 자동으로 입력해 설치하도록한다. 
하지만 사용할때는 질문을 확인하며 주의해야한다.

```!echo 'Y' | sudo apt install <package_name>```
