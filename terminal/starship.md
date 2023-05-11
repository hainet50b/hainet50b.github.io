# Starship

## Starshipのインストール

### Mac
```shell
brew install starship

echo 'eval "$(starship init zsh)"' >> ~/.zshrc

# Nerd Fontをインストールする。ターミナルのフォント設定も変更すること。
brew tap homebrew/cask-fonts
brew install font-hack-nerd-font
```

### Windows
```powershell
scoop install starship

echo 'Invoke-Expression (&starship init powershell)' >> $PROFILE

# Nerd Fontをインストールする。ターミナルのフォント設定も変更すること。
scoop bucket add nerd-fonts
scoop isntall Hack-NF
```
