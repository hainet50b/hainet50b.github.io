# Node.jsのセットアップ（Mac）
{:.no_toc}

* toc
{:toc}

Windows：[Node.jsのセットアップ（Windows）](https://hainet50b.github.io/nodejs/nodejs-setup-windows)

## nvm（Node Version Manager）のインストール
```shell
brew install nvm

mkdir ~/.mvn
echo 'export NVM_DIR="$HOME/.nvm"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/nvm.sh" ] && \. "/opt/homebrew/opt/nvm/nvm.sh"' >> ~/.zshrc
echo '[ -s "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm" ] && \. "/opt/homebrew/opt/nvm/etc/bash_completion.d/nvm"' >> ~/.zshrc

nvm --version
0.39.3
```

## node / npmのインストール
```shell
nvm ls-remote --lts
v18.16.1   (Latest LTS: Hydrogen)

nvm install --lts

nvm ls
-> v18.16.1
```

## nodeのバージョンを切り替える
```shell
nvm use --lts
Now using node v18.16.1 (npm v9.5.1)

node -v
v18.16.1
npm -v
9.5.1
npx -v
9.5.1
```
