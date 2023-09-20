# commit 취소, 리셋하기

## rest
- commit을 취소하고 해당 파일들은 staged 상태로 워킹 디렉터리에 보존: ```git reset --soft HEAD^```
- commit을 취소하고 해당 파일들은 unstaged 상태로 워킹 디렉터리에서 삭제: ```git reset --hard HEAD^```

## reset-log
- ```git log```: 방금 전에 커밋한 내용 기록 
- ```git reset a6f30a --hard```: 노란 줄의 커밋번호의 앞 6자리(a6f30a)를 통해 분기

## revert
- ```git revert a6f30a```
