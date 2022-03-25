---
title:       "Virtualenv入門"
URL:         "python-virtualenv-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-12-07
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- virtualenv
categories:
- tech
---

[Virtualenv](https://virtualenv.pypa.io/en/stable/) についてかるくまとめる。

<!--more-->

以下の記事を読んで pyenv を導入済みである前提で記載する。

- [すべての**envを管理するanyenv](https://blog.pepese.com/anyenv/)

# はじめに

ややこしい他ツールがあるので少しまとめる。

- pyenv-virtualenv
    - pyenv で導入した各 Python の site-packages を管理するツール
    - Virtualenv とは全く別物
- Virtualenv
    - Python で作成する各プロジェクト毎のモジュール・ライブラリのバージョンを管理するツール
        - 厳密に言うと、プロジェクト毎というか、環境のみをそれぞれ作成・管理できる感じ
    - 今回のターゲット
- venv
    - Virtualenv とほぼ同じ機能
    - Virtualenv の方がデファクトの模様
    - Virtualenv が Python 2 系 3 系両方に対応しているのにたいして venv は 3 系対象
- virtualenvwapper
    - Virtualenv のラッパー
    - 今回は使わない

# 環境設定

## pip の更新と確認

```sh
$ pip install --upgrade pip
$ pip list --format=columns
```

## Virtualenv のインストールと使い方

```sh
$ pip install virtualenv
$ cd /path/to/project
$ virtualenv .virtualenv # .virtualenv という名前でディレクトリができる
$ source .virtualenv/bin/activate # これで .virtualenv の環境になる
(.virtualenv) $ # いつも通り pip で欲しいモジュールを導入・削除・更新する
(.virtualenv) $ deactivate # 環境をぬける
```

Windows の場合は `.virtualenv\Scripts\activate` が環境実行用のスクリプト。  
`.virtualenv` ディレクトリを削除するば、環境が削除される。  
gemfile とか package.json みたいなファイルベースではなく、環境を切り替える使い方。

# Virtualenv を使わない環境共有方法

Virtualenv など Python エコシステムを利用するとバージョン管理システムなどでプロジェクトを共有する際、モジュール・ライブラリのバージョンを共有しにくい印象。

## requirements.txt

`requirements.txt` というファイル（名前は任意）に現在の `pip` でのインストール状況を出力する方法。

```sh
$ pip freeze > requirements.txt
$ cat requirements.txt
aniso8601==1.3.0
appnope==0.1.0
bleach==1.5.0
certifi==2017.7.27.1
# (省略)
```

残された `requirements.txt` を使って以下のようにインストールする。

```sh
$  pip install -r requieremnts.txt
```

その他実践的な方法は以下を参照。  
[Python アプリ依存パッケージ管理 ベストプラクティス集](https://qiita.com/zakuro9715/items/68c8d8c6b1b05f91fa2e)

# おすすめ書籍

<!-- amazon affiliate kindle python --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=479738946X&linkId=6a85a5dd8a1e65d5de970b2542faceb0"></iframe>