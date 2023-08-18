# Public, Private 저장소별 커밋 및 브랜치 사용하기

## Public기
- 저장소 클론: ```git clone https://github.com/<username>/<repository>.git```

## Private
- 저장소 클론: ```git clone https://<username>:<token>@github.com/<username>/<repository>.git```
- GitHub token 발급
  - Settings > Developer Settings > Personal access tokens > Generate new token
  - https://github.com/settings/tokens

## Branch 사용
- 브랜치 확인: ```git branch```
- 새 브랜치 생성: ```git checkout -b <branch-name>```
- 변경사항 스테이징: ```git add .```
- 커밋 생성: ```git commit -m 'contents'```
- 새 브랜치에 변경사항 푸시: ```git push origin <branch-name>```
- 메인에 푸쉬: ```git push origin main```
