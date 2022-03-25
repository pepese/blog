---
title:       "マルチランタイムバージョン管理ツールasdf"
URL:         "asdf-basics"
subtitle:    ""
description: "asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。"
keyword:     "asdf,プログラミング言語,ツール,バージョン管理,ランタイム"
date:        2022-03-25
author:      "ぺーぺーSE"
image:       ""
tags:
- asdf
categories:
- tech
---

[asdf](https://asdf-vm.com) を使えば、複数のプログラミング言語ランタイムおよびそのバージョンを `asdf` コマンドだけで管理することができる。  
ここでは、Mac に環境構築する方法について記載する。

<!--more-->

# 環境構築

## インストール

```bash
$ brew install coreutils curl git
$ brew install asdf
$ echo -e "\n. $(brew --prefix asdf)/libexec/asdf.sh" >> ~/.bash_profile
$ exec $SHELL -l
```

上記は `.bash_profile` に設定を追記しているが、 環境・好みに応じて `.zshenv` などへの追記でもよい。  
なお、環境により差分があるので、基本は `brew install asdf` 実行後の Homebrew の指示に従う。  
Homebrew 以外の場合は [こちら](https://asdf-vm.com/guide/getting-started.html#_3-install-asdf) を参照。

## アップデート

```bash
$ brew upgrade asdf
```

Homebrew 以外の場合は [こちら](https://asdf-vm.com/manage/core.html#update) を参照。

## アンインストール

`.bash_profile` などインストール時に設定したファイルから以下の行を削除。

```bash
. $(brew --prefix asdf)/libexec/asdf.sh
. $(brew --prefix asdf)/etc/bash_completion.d/asdf.bash
```

上記実施後に以下のコマンドを実行。

```bash
$ brew uninstall asdf --force
$ rm -rf $HOME/.tool-versions $HOME/.asdf
```

Homebrew 以外の場合は [こちら](https://asdf-vm.com/manage/core.html#uninstall) を参照。

# 使い方

## プラグイン・ツールのインストール

1. 使いたいプログラミング言語・ツールに対応したプラグインを探す
    - `asdf plugin list all`
2. 使いたいプログラミング言語・ツールに対応したプラグインを追加する
    - `asdf plugin add <name>`
    - `asdf plugin add <name> <git-url>` # asdf で管理されていないプラグインの場合
3. プラグインが追加されたか確認する
    - `asdf plugin list`
4. 使いたいプログラミング言語・ツールのバージョンを探す
    - `asdf list-all <name>`
5. 使いたいプログラミング言語・ツールのバージョンを指定してインストールする
    - `asdf install <name> <version>`
6. global/local/shell のいずれかでプログラミング言語・ツールのバージョンを設定する
    - `asdf global <name> <version>`
7. 現在のバージョンを確認する
    - `asdf current`
    - `asdf current <name>`

helm で一連の流れをやってみる。

```bash
$ asdf plugin list all | grep helm
helm                          https://github.com/Antiarchitect/asdf-helm.git
helm-cr                       https://github.com/Antiarchitect/asdf-helm-cr.git
helm-ct                       https://github.com/tablexi/asdf-helm-ct.git
helm-docs                     https://github.com/sudermanjr/asdf-helm-docs.git
helmfile                      https://github.com/feniix/asdf-helmfile.git

$ asdf plugin add helm

$ asdf plugin list
helm

$ asdf list-all helm
3.4.1

$ asdf install helm 3.4.1

$ asdf global helm 3.4.1

$ asdf current helm
helm            3.4.1           /Users/xxxx/.tool-versions

$ helm version
version.BuildInfo{Version:"v3.4.1", GitCommit:"c4e74854886b2efe3321e185578e6db9be0a6e29", GitTreeState:"clean", GoVersion:"go1.14.11"}
```

## プラグインのアップデート

```bash
$ asdf plugin update --all
```

## ツールのアンインストール

```bash
$ asdf uninstall <name> <version>
```

## プラグインのアンインストール

```bash
$ asdf plugin remove <name>
```

## 困ったときは

```bash
$ asdf help
```