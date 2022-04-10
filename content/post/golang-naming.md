---
title:       "Go言語の命名規則"
URL:         "golang-naming"
subtitle:    ""
description: "Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。この記事ではGoの命名規則をご紹介します。リーダブルコードを目指しましょう。"
keyword:     "Go, 命名規則, Go言語, golang"
date:        2022-04-10
author:      "ぺーぺーSE"
image:       ""
tags:
- golang
categories:
- tech
---

Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。  
コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。  
この記事ではGoの命名規則をご紹介します。リーダブルコードを目指しましょう。

<!--more-->

Go 言語の入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/golang-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Go言語入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。この記事ではGo開発環境の設定から使い方までを記載します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# パッケージ名、ディレクトリ名

- 完結に、全て小文字かつ 1 単語で
  - 例外としては `_test` サフィックス
- utility パッケージにしない
  - base、util、common、lib、misc など
- パッケージ名を繰り返さない
  - `repository.UserRepository` とか NG
  - `list.NewList` でなく `list.New`
- どうしても難しい場合、ディレクトリ名はケバブケースは可
  - `xxx-xxx`

# ファイル名

- スネークケースで
  - `xxx_xxx`

# 関数、type、構造体

- キャメルケースで
  - 公開する場合はアッパーキャメルケース `XxxYyy`
  - 公開しない場合はローワーキャメルケース `xxxYyy`
- Getter/Setter
  - Go では所謂 Getter には `Get` プレフィックスを付けません
    - 例えば `User` を返したいなら、メソッド名は `GetUser` ではなく `User`
  - Setter の場合は SetUser のように `Set` を付けましょう
    -  (Go で Setter を作るケースはあまりありませんが)

# レシーバ名

Go には class は無いが、 **型に対してメソッドを定義** できます。  
構造体だけにメソッド定義できるのではなく、型に対して定義できることに注意しましょう。  
Go ではメソッドを定義する型を **レシーバ** と呼びます。

- 英語 1 文字か 2 文字でなるべく短く命名
  - 型が `Client` であれば `c` 、 `cl` 等
- レシーバ名は必ず統一
  - 場所によって `c` が使われていたり `cl` が使われていたりは NG
- 修飾語を利用しない
  - 例えば `httpClient` ならレシーバ名は `c` 、 `DBCreator` なら `c`
- map 等の存在チェックを行う場合は `ok`
  - `id, ok := users[userID]`
- 命名は基本的にキャメルケースだが、元々略語として浸透している単語は一貫した大文字と小文字を使用
  - `url` ではなく `URL` とか、`http` ではなく `HTTP` を使うとか
  - `ACL`、`API`、`ASCII`、`CPU`、`CSS`、`DNS`、`EOF`、`GUID`、`HTML`、`HTTP`、`HTTPS`、`ID`、`IP`、`JSON`、`LHS`、`QPS`、`RAM`、`RHS`、`RPC`、`SLA`、`SMTP`、`SQL`、`SSH`、`TCP`、`TLS`、`TTL`、`UDP`、`UI`、`UID`、`UUID`、`URI`、`URL`、`UTF8`、`VM`、`XML`、`XMPP`、`XSRF`、`XSS`

# 変数・引数名

- 基本的にレシーバ名と同じ
- ただし、スコープに応じて使い分けましょう
  - `Config` の変数なら、`c` 、 `conf` 、 `cfg` などで使い分けましょう

# error変数名

- 基本的に `err`
- 公開する際など複数定義が必要な場合は `Err` プレフィックスを付けて宣言

# インターフェース名

- メソッドが 1 つの場合
  - 原則として「メソッド名 + `er/or`」
    - 例えば `Write` メソッドを持つインターフェースであれば `Writer`
  - 目的語がある場合は「目的語 + 動詞 + `er/or`」
    - 例えば `PrintObject` メソッドを持つインターフェースであれば `ObjectPrinter`
- メソッドが 2 つ以上の場合
  - 原則として慣習がある場合それに従い、慣習が無い場合いい感じの名前を考える
    - `io` パッケージの `ReadCloser` のように、メソッドを連ねて前述の `er` を付けるパターン
    - `Codec` のように、特別なメソッドの組み合わせのときに使われるパターン
      - `Codec` は `EncodeDecoder` だったり他の命名にすることも

# おすすめ書籍

<!-- ad link - amazon/rakuten books - golang -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"改訂2版 みんなのGo言語 [ 松木雅幸、mattn、藤原俊一郎、中島大一、上田拓也、牧 大輔、鈴木 健太 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/7277\/9784297107277.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/15956516\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DHsTJX","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hwxl6y","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/15956516\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"nAQxh","s":"s"});
</script>
<div id="msmaflink-nAQxh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>