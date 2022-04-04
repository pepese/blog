---
title:       "Jupyter Notebook入門"
URL:         "jupyter-notebook-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2018-02-20
author:      "ぺーぺーSE"
image:       ""
tags:
- jupyter
- python
categories:
- tech
---

Jupyter Notebook 、ついでに JupyterHub について軽くまとめる。

<!--more-->

- [公式](http://jupyter.org/)
- [公式ドキュメント](http://jupyter.org/documentation)

# Jupyter Notebook

## インストール

```bash
$ pip install jupyter
```

## 起動

```bash
$ jupyter notebook
```

### 起動オプション

|オプション|説明|
|:---|:---|
| `--no-browser` |自動でブラウザを起動しない|
| `--port xxxx` |ポートの指定|
| `--help` |ヘルプ|

## ショートカットまとめ

自分がよく使うショートカットのみまとめる。

### コマンドモード（ `ESC` 押したとき）

|コマンド|説明|
|:---|:---|
| `Enter` |編集モード|
| `H` |ショートカット一覧|
| `DD` |セルの削除|
| `K` |上のセルへ移動|
| `J` |下のセルへ移動|
| `A` |セルを上に追加|
| `B` |セルを下に追加|
| `Y` |コードモード|
| `M` |マークダウンモード|
| `1` 〜 `6` |見出し１〜見出し６で書き出す|
| `00` |カーネルをリスタート|
| `S` or `Command + S` |保存|

### 編集モード（ `Enter` 押したとき）

|コマンド|説明|
|:---|:---|
| `ESC` or `Ctrl + M` |コマンドモード|
| `Ctrl + Enter` |セルの実行|
| `Command + S` |保存|

## その他小技

|コマンド|説明|
|:---|:---|
|実行結果のセルをダブルクリック|実行結果の最小化（閉じる）|

# JupyterHub

JupyterHub を導入することで以下が可能になる。

- マルチユーザ
- ユーザ管理・認証
- 高スペックサーバでリモートから利用
- などなど

<img src="https://jupyterhub.readthedocs.io/en/stable/_images/jhub-parts.png" />

## インストール

```bash
$ pip install jupyterhub notebook
$ npm install -g configurable-http-proxy
# test
$ jupyterhub -h
$ configurable-http-proxy -h
```

なお、 Docker 版も提供されている。

## 起動

```bash
$ jupyterhub
```

`https://localhost:8000` で起動する。

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>