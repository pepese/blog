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

```bash
$ pip install --upgrade pip
$ pip list --format=columns
```

## Virtualenv のインストールと使い方

```bash
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

```bash
$ pip freeze > requirements.txt
$ cat requirements.txt
aniso8601==1.3.0
appnope==0.1.0
bleach==1.5.0
certifi==2017.7.27.1
# (省略)
```

残された `requirements.txt` を使って以下のようにインストールする。

```bash
$  pip install -r requieremnts.txt
```

その他実践的な方法は以下を参照。  
[Python アプリ依存パッケージ管理 ベストプラクティス集](https://qiita.com/zakuro9715/items/68c8d8c6b1b05f91fa2e)

# おすすめ書籍

<!-- ad link - amazon/rakuten books - python -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"みんなのPython 第4版 [ 柴田 淳 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/9463\/9784797389463.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DMswgV","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMCjfu","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"rQIGU","s":"s"});
</script>
<div id="msmaflink-rQIGU">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>