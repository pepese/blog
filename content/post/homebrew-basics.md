---
title:       "Homebrew入門"
URL:         "mac-homebrew-basics"
subtitle:    ""
description: "HomebrewはMacで利用できるソフトウェアパッケージマネージャ。"
keyword:     "Mac, Homebrew, brew"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- mac
- homebrew
categories:
- tech
---

[Homebrew](http://brew.sh/index_ja.html)についてまとめる。  
HomebrewはMac用のaptやyumのようなパッケージマネージャ。ソフトウェアを簡単にインストールできる。  
インストールから使い方までまとめてみる。

<!--more-->

# 環境設定

## インストール

```bash
$ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
==> This script will install:
/usr/local/bin/brew
/usr/local/share/doc/homebrew
/usr/local/share/man/man1/brew.1
/usr/local/share/zsh/site-functions/_brew
/usr/local/etc/bash_completion.d/brew
/usr/local/Homebrew
==> The following new directories will be created:
/usr/local/Cellar
/usr/local/Homebrew
/usr/local/Frameworks
/usr/local/bin
/usr/local/include
/usr/local/lib
/usr/local/opt
/usr/local/sbin
/usr/local/share/zsh
/usr/local/share/zsh/site-functions
```

上記を実行後、 `brew doctor` で問題がないことを確認。  
もしWarningが出たらメッセージに従って対処する。  
筆者の場合はXcodeをインストールしてCommand Line Toolsをアップデートしたら解決した。

- `/usr/local/Cellar/`
    - Homebrewでインストールしたソフトウェアはここに配備される
- `/usr/local/bin/`
    - インストールしたソフトウェアのコマンドのシンボリックリンクはここに配備される

## Homebrew のアップデート

```bash
$ brew update
```

## ソフトウェアのインストール

```bash
$ brew install <software>
```

## ソフトウェアの更新

```bash
# 特定のソフトウェア
$ brew upgrade <software>
# Homebrew で導入した全てのソフトウェア
$ brew upgrade
```

## キャッシュされている古いソフトウェアの削除

```bash
$ brew cleanup
```

# brew tap

`brew tap` は、公式以外の formula を追加することのできる Homebrew のサブコマンド。  
Homebrewをインストールした際に標準で組み込まれている。

```bash
$ brew tap <userName>/<repository>
```

上記コマンドを実行すると、GitHub 公開リポジトリ (https://github.com/<userName>/homebrew-<repository>) が参照され、 `/usr/local/Library/Taps` 以下にインストールされる。  
`brew tap` コマンドでこれまでにインストールされたリポジトリの一覧が表示される。  
また、 `brew tap <url>` コマンドでGitHub以外のソフトウェアもインストール可能。

# brew cask

Homebrew 本体に統合され、インストール不要になった。 `--cask` オプションで `brew cask` コマンドは代替可能になっている。  
`brew` や `brew tap` でソフトウェアをインストールする際、目的のソフトウェアを動かすには依存するその他のソフトウェアをインストールする必要がある場合がある。  
そんなとき Cask を使用すると目的のソフトウェアをインストールするだけで依存するソフトウェアもインストールしてくれる。  
さらにGoogle ChromeやAtomといったソフトのインストールも可能となる。

- `/usr/local/Caskroom/`
    - Cask でインストールしたソフトウェアはここに配備される
- `/usr/local/bin/`
    - インストールしたソフトウェアのコマンドのシンボリックリンクはここに配備される

# おすすめのソフトウェア

- `brew install iterm2 --cask`
- `brew install visual-studio-code --cask`
- `brew install google-chrome --cask`
- `brew install zoom`
- `brew install microsoft-teams --cask`
- `brew install mattermost --cask`
- `brew install hugo`