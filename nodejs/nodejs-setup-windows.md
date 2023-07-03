# Node.jsのセットアップ（Windows）
{:.no_toc}

* toc
{:toc}

Mac：[Node.jsのセットアップ（Mac）](https://hainet50b.github.io/nodejs/nodejs-setup-mac){:target="_blank"}

## nvm（Node Version Manager）のインストール
```powershell
scoop install nvm

nvm version
1.1.11
```

## node / npmのインストール
```powershell
nvm list available
|   CURRENT    |     LTS      |  OLD STABLE  | OLD UNSTABLE |
|--------------|--------------|--------------|--------------|
|    20.3.1    |   18.16.1    |   0.12.18    |   0.11.16    |
...

nvm install lts

nvm list installed
* 18.16.1 (Currently using 64-bit executable)
```

## nodeのバージョンを切り替える
```powershell
nvm use lts
Now using node v18.16.1 (64-bit)

node -v
v18.16.1
npm -v
9.5.1
npx -v
9.5.1
```
