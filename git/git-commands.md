# よく使うGitコマンド
{:.no_toc}

* toc
{:toc}

## 直近のコミットのコメントを変更する
```shell
git commit --amend
```

## 複数のコミットを一つにまとめる
サンプルとして以下の4つのコミットを作成する。

```shell
git init
touch init.txt; git add -A; git commit -m "Add init"
touch foo.txt; git add -A; git commit -m "Add foo"
touch bar.txt; git add -A; git commit -m "Add bar"
touch baz.txt; git add -A; git commit -m "Add baz"

git log --oneline

17074f5 (HEAD -> main) Add baz
832b157 Add bar
07e14de Add foo
44ce705 Add init
```

fooとbarをまとめて第３のコミットとすることを目指す。

```shell
git rebase -i HEAD~3

pick 07e14de Add foo
# pick から squash へと変更する。このコミットは一つ前のコミット（=foo）と統合される。
squash 832b157 Add bar
pick 17074f5 Add baz
```

コミットメッセージの編集画面に遷移する。任意のメッセージに書き換える。

```
# 編集前
# This is a combination of 2 commits.
# This is the 1st commit message:

Add foo

# This is the commit message #2:

Add bar

---

# 編集後
Rebased commit
```

まとめたコミット以降のコミットハッシュは一新される。

```shell
git log --oneline

e013765 (HEAD -> main) Add baz
2a96634 Rebased commit
44ce705 Add init
```

## ファイルをGit管理下から取り除く
取り除くファイルを`.gitignore`に追加する。
```
target
```

Gitのキャッシュから該当のファイルを削除する。
```shell
git rm --cache target

# ディレクトリの場合は-rオプションを付与する。
git rm --cache -r target
```

その後、変更をコミットする。
```shell
git commit -m "Remove unwanted files."
```

## 複数のリポジトリを合流させたリポジトリを作成する
以下のディレクトリ構成のときに、fooとbarを合流させたリポジトリを作成する。

```shell
tree
.
├── bar
│   └── bar
└── foo
    └── foo
```

```shell
mkdir programacho-repo
cd programacho-repo
git init

git subtree add --prefix foo ../foo main
git subtree add --prefix bar ../bar main
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