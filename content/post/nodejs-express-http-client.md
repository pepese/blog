---
title:       "Node.js・Expres・requestでHTTP(S)リクエストを発行する"
URL:         "nodejs-express-http-client"
subtitle:    ""
description: ""
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

```sh
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

```sh
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

<!-- amazon affiliate kindle node.js --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08HRMTXHB&linkId=f02e6af82c7864b6df5fd31c0639d4bf"></iframe>