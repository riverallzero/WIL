# 코드로 깃허브 이슈 등록하기

![](../.asset/github-code-issue.png)

```pip install pygithub```

```python
from github import Github

GITHUB_TOKEN = 'github_token'
REPO_NAME = 'github_repository_name'
repo = Github(GITHUB_TOKEN).get_user().get_repo(REPO_NAME)

title = ''
body = ''

repo.create_issue(title=title, body=body)
```
