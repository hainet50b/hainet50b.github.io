# Rustのバージョン管理

```shell
# rustcバージョンを確認
rustc --version
rustc 1.69.0 (84c898d65 2023-04-16)

# インストール済みのRustを確認
rustup toolchain list
stable-aarch64-apple-darwin (default)

# 任意のRustをインストール
rustup toolchain install beta # ベータ版
rustup toolchain install 1.69.0 # 任意のバージョンを指定可能

rustup toolchain list
stable-aarch64-apple-darwin (default)
beta-aarch64-apple-darwin
1.69.0-aarch64-apple-darwin

# Rustを切り替える
rustup default beta
rustc --version
rustc 1.70.0-beta.5 (16ec1c0a8 2023-05-20)

rustup default 1.69.0
rustc --version
rustc 1.69.0 (84c898d65 2023-04-16)
```
