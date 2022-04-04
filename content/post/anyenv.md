---
title:       "すべての**envを管理するanyenv"
URL:         "anyenv"
subtitle:    ""
description: "anyenvはスクリプト言語のバージョン管理を行うツール。"
keyword:     "anyenv,プログラミング言語,バージョン管理"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- anyenv
- ndenv
- node.js
- pyenv
- python
- rbenv
- ruby
categories:
- tech
---

[anyenv](https://github.com/riywo/anyenv)は、renvやpyenvなどスクリプト言語のバージョン管理を行うツール類（所謂、 \*\*env）を管理するツール。  
「すべて」は言い過ぎかも。  
インストールから簡単な使い方まで記載する。

<!--more-->

# 環境設定

## Homebrew でインストール

Homebrew にも対応した模様。

```bash
$ brew install anyenv
$ anyenv init
$ echo 'eval "$(anyenv init -)"' >> ~/.bash_profile
```

## マニュアルインストール

```bash
$ git clone https://github.com/riywo/anyenv ~/.anyenv
$ echo 'export PATH="$HOME/.anyenv/bin:$PATH"' >> ~/.bash_profile
$ echo 'eval "$(anyenv init -)"' >> ~/.bash_profile
$ exec $SHELL -l
$ anyenv init
```

anyenv自身のアップデートは上記の通りGithubからcloneしているだけなので、 `git pull origin master` すればいいだけだと思う。  
アンインストールは `rm -fR ~/.anyenv` 。ただし、プロファイルに設定したanyenvへのパス削除を忘れずに。

# プラグインのインストール

ここでは以下をインストールする。

- [anyenv-update](https://github.com/znz/anyenv-update)
    - インストールされている \*\*env をアップデートするためのプラグイン
    - `anyenv update` コマンドが使用できるようになる
- [anyenv-git](https://github.com/znz/anyenv-git)
    - anyenv で git のコマンドを実行できるようにするためのプラグイン
    - `anyenv git` コマンドが使用できるようになる

## anyenv-updateのインストール

```bash
$ mkdir -p $(anyenv root)/plugins
$ git clone https://github.com/znz/anyenv-update.git $(anyenv root)/plugins/anyenv-update
```

`anyenv commands` で `update` が確認されればインストールされている。

## anyenv-gitのインストール

```bash
$ git clone https://github.com/znz/anyenv-git.git $(anyenv root)/plugins/anyenv-git
```

`anyenv commands` で `git` が確認されればインストールされている。

# \*\*envの管理

## \*\*envのインストール

```bash
$ anyenv install rbenv
$ anyenv install plenv
$ anyenv install pyenv
$ anyenv install phpenv
$ anyenv install ndenv
$ anyenv install denv
$ anyenv install jenv
$ anyenv install luaenv
$ anyenv install goenv
$ exec $SHELL -l
```

## \*\*envのアップデート

以下でインストールしてある \*\*env がすべてアップデートされる。

```bash
$ anyenv update
```

## 各 \*\*env の現在のバージョンを確認

```bash
$ anyenv global
ndenv: v7.6.0
plenv: 5.25.7
pyenv: 3.5.2
rbenv: 2.3.0

$ anyenv version
ndenv: v7.6.0 (set by /Users/xxxx/.anyenv/envs/ndenv/version)
plenv: 5.25.7 (set by /Users/xxxx/.anyenv/envs/plenv/version)
pyenv: 3.5.2 (set by /Users/xxxx/.anyenv/envs/pyenv/version)
rbenv: 2.3.0 (set by /Users/xxxx/.anyenv/envs/rbenv/version)
```

# \*\*envの使い方

## ndenv

### 現在のバージョンを確認

```bash
$ ndenv version
```

### インストール済みのバージョン一覧確認

```bash
$ ndenv versions
```

### インストール可能なバージョン一覧の確認

```bash
$ ndenv install --list
```

### インストール

```bash
$ ndenv install v8.1.3
```

### バージョンの切り替え

```bash
$ ndenv global v8.1.3
$ node -v
v8.1.3
```

### npm をアップデート

```bash
$ npm update -g npm
```

## pyenv

### 現在のバージョンを確認

```bash
$ pyenv version
```

### インストール済みのバージョン一覧確認

```bash
$ pyenv versions
```

### インストール可能なバージョン一覧の確認

```bash
$ pyenv install --list
```

### インストール

```bash
$ pyenv install 3.6.1
```

### バージョンの切り替え

```bash
$ pyenv global 3.6.1
$ python -V
Python 3.6.1
```

### pipをアップデート

```bash
$ pip install -U pip
```

### pipでいれたパッケージを一括アップデートする pip-review

#### インストール

```bash
$ pip install pip-review
```

#### 更新があるパッケージを表示

```bash
$ pip-review
```

#### 一括アップデート

```bash
$ pip-review --auto
```

## rbenv

### 現在のバージョンを確認

```bash
$ rbenv version
```

### インストール済みのバージョン一覧確認

```bash
$ rbenv versions
```

### インストール可能なバージョン一覧の確認

```bash
$ rbenv install --list
```

### インストール

```bash
$ rbenv install 2.4.1
```

### バージョンの切り替え

```bash
$ rbenv global 2.4.1
$ rbenv rehashr
$ ruby -v
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-darwin16]
```

### gemの確認とbundlerの導入

```bash
$ which gem
/Users/xxx/.anyenv/envs/rbenv/shims/gem
$ gem install bunlder
$ which bundle
/Users/xxx/.anyenv/envs/rbenv/shims/bundle
```

### rehash を不要にする rbenv-gem-rehash

「rbenv-gem-rehash」を導入することで「rbenv rehash」（~/.rbenv/versions/2.x.y/bin/ 以下に置いてあるコマンド群を ~/.rbenv/shims/以下に置いて使えるようにする）を実行する必要が無くなる。

```bash
$ git clone https://github.com/sstephenson/rbenv-gem-rehash.git ~/.anyenv/envs/rbenv/plugins/rbenv-gem-rehash
$ exec $SHELL -l
$ rbenv install 2.4.1
$ rbenv global 2.4.1
$ ruby -v
ruby 2.4.1p111 (2017-03-22 revision 58053) [x86_64-darwin16]
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>