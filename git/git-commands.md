# よく使うGitコマンド

## 直近のコミットのコメントを変更する
```shell
git commit --amend
```

## 過去のコミットのユーザー名とメールアドレスを変更する
誤って業務環境でDotfilesを更新してしまった時などに使う。
```shell
user_name=hainet50b
user_email=20400616+hainet50b@users.noreply.github.com

git filter-branch -f --env-filter " \
  GIT_AUTHOR_NAME=$user_name; \
  GIT_AUTHOR_EMAIL=$user_email; \
  GIT_COMMITTER_NAME=$user_name; \
  GIT_COMMITTER_EMAIL=$user_email; \
" HEAD
```
