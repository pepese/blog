---
title:       "Node.js・Expres・requestでHTTP(S)リクエストを発行する"
URL:         "nodejs-express-http-client"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-28
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- yarn
- express
- javascript
categories:
- tech
---

Node.js/ExpressアプリケーションからHTTP(S)を発行してみる。

<!--more-->

使用するツール・ライブラリは以下。

- HTTP(S)クライアント
    - [request](https://github.com/request/request)

以下の記事を読んだ前提で書く。

- [Express入門](https://blog.pepese.com/nodejs-express-basics/)

ちなみに HTTP(S)クライアントとしては `request` 以外にも以下のようなものがある。

- [http](https://nodejs.org/dist/latest-v6.x/docs/api/http.html#http_http_methods) / [https](https://nodejs.org/dist/latest-v6.x/docs/api/https.html#https_https_get_options_callback)
    - Node.js のネイティブライブラリ
- [Unirest](http://unirest.io/nodejs.html)
- [axios](https://github.com/mzabriskie/axios)
    - `request` が嫌ならこれがいいかな？

# 環境設定

先の記事で紹介したプロジェクトにて以下を実行する。

```bash
$ yarn add request
```

# 実装

- `app/controllers/get_ip.js`
    - 自分のグローバルIPを取得するAPIにリクエストを発行するサンプル
    - `http://[FQDN]/ip` で動確

## `app/controllers/get_ip.js`

```javascript
const request = require('request');

const url = 'https://httpbin.org/ip';
const options = {
  url: url,
  method: 'GET',
  headers: {
    'Accept': 'application/json'
  }
};

const get_ip = (req, res, next) => {
  request(options, (error, response, body) => {
    if (!error) {
      console.log('response status: ' + response.statusCode);
      console.log('response headers content-type: ' + response.headers['content-type']);
      res.send(body);
    }
    else {
      console.log('error: ' + error.message);
      res.status(500);
      res.end('Internal Server Error'); // これがないとレスポンスが返らない
    }
  });
};

module.exports = get_ip;
```

# request の基本的な使い方

- `request(options, callback)`
    - コールバックで `options` に基づいたリクエストを発行する
    - サンプルはこれを使用
    - [ドキュメント](https://github.com/request/request#requestoptions-callback)
    - [optiong の設定](https://github.com/request/request#requestoptions-callback)
- `request.defaults(options)`
    - HTTP(S)クライアントのデフォルトの設定を行う
    - [ドキュメント](https://github.com/request/request#requestdefaultsoptions)
- `request.METHOD()`
    - HTTPメソッドを関数で表現する形式
    - [ドキュメント](https://github.com/request/request#requestmethod)

# Promise

コールバック地獄を避けたい人は [request-promise](https://github.com/request/request-promise) を使うと簡単に非同期 bluebird 版 Promise を実現できる。

## 導入

```bash
$ yarn add request-promise
```

## 実装 `app/controllers/get_ip_promise.js`

```javascript
const request = require('request-promise');

const url = 'https://httpbin.org/ip';
const options = {
  url: url,
  method: 'GET',
  headers: {
    'Accept': 'application/json'
  },
  resolveWithFullResponse: true, // <--- これを入れないと responseオブジェクトが取得できない！！
  simple: true
};

const get_ip_promise = (req, res, next) => {
  request(options)
  .then((response) => {
    console.log('response status: ' + response.statusCode);
    console.log('response headers content-type: ' + response.headers['content-type']);
    res.send(response.body);
  })
  .catch((error) => {
    console.log('error: ' + error.message);
    res.status(500);
    res.end('Internal Server Error'); // これがないとレスポンスが返らない
  });
};

module.exports = get_ip_promise;
```

# おすすめ書籍

<!-- ad link - amazon/rakuten books - node.js -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Node.js超入門 第3版 [ 掌田津耶乃 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/2433\/9784798062433.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16353060\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3Jf72uu","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/haXJAD","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16353060\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"vtzlh","s":"s"});
</script>
<div id="msmaflink-vtzlh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>