---
title:       "Heroku入門"
URL:         "heroku-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-12-07
author:      "ぺーぺーSE"
image:       ""
tags:
- heroku
categories:
- tech
---

Heroku は salesforce が運営する PaaS 。  
Free プランではクレカ登録無しで月に 550 dyno hours （約 23 日分、 1 ヶ月分無い）、クレカ登録すると月に 1000 dyno hours を無料で利用できる。  
Free プランの落とし穴は、 30 分アクセスが無いとインスタンス（ dyno ）が Sleep してしまうところ。　　
以下、 Free プランでアカウントを取得している前提で記載する。

<!--more-->

# Heroku CLI

Heroku の Web ページで各種設定できるが、ここでは CLI ベースの方法を記載する。

## インストール

```bash
$ brew install heroku/brew/heroku
```

[Trouble Shooting](https://devcenter.heroku.com/articles/heroku-cli#troubleshooting)  
[Uninstalling](https://devcenter.heroku.com/articles/heroku-cli#uninstalling-the-heroku-cli)

## 初期起動

以下の手順で Heroku へアプリケーションをデプロイ・起動する。  
なお、 `/path/to/myApp` は Heroku が対応している言語で実装されたローカルのアプリケーションプロジェクトをさす。  
また、 Heroku へのアプリケーションのデプロイは Git 経由で行われる。

```bash
$ heroku login # Heroku にログイン
$ cd /path/to/myApp
$ heroku create # Heroku 上に新しいアプリケーションを作成
Creating app... done, ⬢ shielded-scrubland-xxxxx
https://shielded-scrubland-xxxxx.herokuapp.com/ | https://git.heroku.com/shielded-scrubland-xxxxx.git
$ git init # アプリケーションプロジェクトの Git 初期化
$ git remote add heroku https://git.heroku.com/shielded-scrubland-xxxxx.git
$ git add --all
$ git commit -m "first commit"
$ git push heroku master # Heroku へアプリケーションをデプロイ
$ heroku open # ブラウザを立ち上げてページを表示
$ heroku help # USAGE
$ heroku apps help # apps の USAGE
```

## アドオン

アドオンを CLI から適用する方法は以下。

```bash
$ heroku addons:create xxxx
```

## プロセスの確認、停止

```bash
$ heroku apps:info # アプリケーションの情報を見る
$ heroku ps # プロセスを見る
$ heroku logs # ログを見る
```

```bash
$ heroku ps:scale web=0 # 停止
$ heroku ps:scale web=1 # 起動
```

Webプロセスのスケール（Dyno、インスタンス台数）を 0 に指定して実質プロセスを停止している。

# 小ネタめも

## node.js アプリケーション

`$ heroku open` でエラーが発生した場合は以下に対応。

- npm script に **start** を追加する
    - `"start": "node app/app.js"`

Heroku ではアプリケーションの起動方法に各プログラミング言語毎にルールがあるので注意。

## １つのアプリケーションを複数のHeroku環境へデプロイ

```bash
$ heroku create アプリ名 --remote 新環境名
$ git push 新環境名 master
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>