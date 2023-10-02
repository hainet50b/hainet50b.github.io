# Homebrewで過去のパッケージをインストール
ライセンスの更新で問題が発生したParallels Desktopをサンプルとする。

## Tapリポジトリを作成
```shell
brew tap-new $USER/old-casks
cd $(brew --repo $USER/old-casks)
git remote add upstream https://github.com/Homebrew/homebrew-cask.git
git fetch upstream
```

## 過去のパッケージのコミットハッシュを取得
```shell
git log master Casks/parallels.rb
```

## コミットハッシュから任意のバージョンを復元
```shell
git checkout f2e4f58 Casks/parallels.rb
```

## 復元したパッケージをインストール
```shell
brew install Casks/parallels.rb
```
