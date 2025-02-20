---
title:       "マルチランタイムバージョン管理ツールasdf"
URL:         "asdf-basics"
subtitle:    ""
description: "asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツールです。この記事では、インストールから使い方の概要までをご紹介します。"
keyword:     "asdf, プログラミング言語, CLI, バージョン管理, ランタイム, ツール"
date:        2025-02-07
author:      "ぺーぺーSE"
image:       ""
tags:
- asdf
categories:
- tech
---

asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツールです。  
この記事では、インストールから使い方の概要までをご紹介します。

なお、2025年1月30日にリリースされた v0.16.0 にて asdf は bash から golang に実装が変わり、仕様が変わっています。  
本記事では、 v0.16.0 以降の情報で記載します。

- [Getting Started (v0.15.xまで)](https://asdf-vm.com/guide/upgrading-to-v0-16.html)
- [Getting Started (v0.16.0以降)](https://asdf-vm.com/guide/getting-started.html)
- [Upgrading to 0.16.0](https://asdf-vm.com/guide/upgrading-to-v0-16.html)

<!--more-->

# 環境構築

## インストール

本記事では Homebrew を利用してインストールします。

```bash
$ brew install asdf
```

`~/.zshrc` に以下を追記します。

```bash
export PATH="${ASDF_DATA_DIR:-$HOME/.asdf}/shims:$PATH"
```

## アップデート

```bash
$ brew upgrade asdf
```

Homebrew 以外の場合は [こちら](https://asdf-vm.com/manage/core.html#update) を参照してください。  
また、 Homebrew で asdf をアップデートした際に `no such file or directory` といったエラーが発生する場合、 環境変数 `ASDF_DIR` に旧バージョンの情報が含まれる可能性があるため、以下のコマンドを試してみてください。

```bash
$ export ASDF_DIR=
$ exec $SHELL -l
```

## アンインストール

```bash
$ brew uninstall asdf --force
$ rm -rf $HOME/.tool-versions $HOME/.asdf
```

Homebrew 以外の場合は [こちら](https://asdf-vm.com/manage/core.html#uninstall) を参照してください。

# 使い方

## プラグイン・ツールのインストール

1. 使いたいプログラミング言語・ツールに対応したプラグインを探します
    - `asdf plugin list all`
2. 使いたいプログラミング言語・ツールに対応したプラグインを追加します
    - `asdf plugin add <name>`
    - `asdf plugin add <name> <git-url>` # asdf で管理されていないプラグインの場合
3. プラグインが追加されたか確認します
    - `asdf plugin list`
4. 使いたいプログラミング言語・ツールのバージョンを探します
    - `asdf list all <name>`
5. 使いたいプログラミング言語・ツールのバージョンを指定してインストールします
    - `asdf install <name> <version>`
6. プログラミング言語・ツールのバージョンを設定します
    - `asdf set <name> <version>`
7. 現在のバージョンを確認します
    - `asdf current`
    - `asdf current <name>`

例として golang で一連の流れを紹介します。

```bash
$ asdf plugin list all | grep golang
golang                                      *https://github.com/asdf-community/asdf-golang.git
golangci-lint                               https://github.com/hypnoglow/asdf-golangci-lint.git

$ asdf plugin add golang

$ asdf plugin list
golang

$ asdf list all golang
1.23.6

$ asdf install golang 1.23.6

$ asdf set golang 1.23.6

$ asdf current golang
Name            Version         Source                      Installed
golang          1.23.6          /Users/xxxxx/.tool-versions true

$ go version
go version go1.23.6 darwin/amd64
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

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>