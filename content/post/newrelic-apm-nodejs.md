---
title:       "New Relic APMをNode.jsに導入する"
URL:         "newrelic-apm-nodejs"
subtitle:    ""
description: "Node.js アプリケーションに New Relic APM を導入したのでメモとして記事に残します。"
keyword:     ""
date:        2022-05-20
author:      "ぺーぺーSE"
image:       ""
tags:
- newrelic
- node.js
categories:
- tech
---

Node.js アプリケーションに New Relic APM を導入したのでメモとして記事に残します。

<!--more-->

# 設定

Express.js と Winston を利用した簡易なアプリケーションを例にします。

```bash
$ npm init
$ npm install newrelic winston @newrelic/winston-enricher express --save
```

# 各種バージョン情報

```bash
$ node -v  
v16.15.0
$ npm -v
8.5.5
$ npm info newrelic version
8.11.1
$ npm info winston version
3.7.2
$ npm info @newrelic/winston-enricher version
3.1.1
$ npm info express version
4.18.1
```

なお、 Node.js の New Relic モジュールは [8.11.0以降](https://docs.newrelic.com/jp/docs/release-notes/agent-release-notes/nodejs-release-notes/node-agent-8-11-0) にログ転送機能がサポートされます。

# コード

コードは以下の 2 種類を準備します。  
各ファイルはプロジェクトのルートディレクトリにフラットに配置するものとします。

- newrelic.js
- sample.js

## newrelic.js

`/node_modules/newrelic/newrelic.js` からコピーしてきたものを編集して利用します。  
`app_name` と `license_key` が必須設定です。

```javascript
'use strict'

exports.config = {
  app_name: ['sample-nodejs'], // 必須設定
  license_key: 'license key',  // 必須設定
  logging: {
    level: 'info'
  },
  allow_all_headers: true,
  application_logging: {
    forwarding: {
      enabled: true
    }
  },
  attributes: {
    exclude: [
      'request.headers.cookie',
      'request.headers.authorization',
      'request.headers.proxyAuthorization',
      'request.headers.setCookie*',
      'request.headers.x*',
      'response.headers.cookie',
      'response.headers.authorization',
      'response.headers.proxyAuthorization',
      'response.headers.setCookie*',
      'response.headers.x*'
    ]
  }
}
```

## sample.js

Express.js と Winston を利用した簡易なアプリケーションを例にします。  
先に例にあげた `newrelic.js` をロードできるように `require('./newrelic')` を記載しておくのがポイントです。

```javascript
require('./newrelic') // newrelic.js のロード

// ロガーの設定
const winston = require('winston');
const newrelicFormatter = require('@newrelic/winston-enricher')(winston)
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    newrelicFormatter(),
    winston.format.json()
  ),
  transports: new winston.transports.Console()
});

// Express.js の設定
const express = require('express')
const app = express()
const port = 3000

app.get('/', (req, res) => {
  logger.info('You access / .'); // Winston によるログ出力
  res.send('Hello World!')
})

app.get('/health', (req, res) => {
  logger.info('You access /health .'); // Winston によるログ出力
  res.send('healty')
})

app.listen(port, () => {
  logger.info(`Example app listening on port ${port}`); // Winston によるログ出力
})
```

実行方法は以下の通りです。

```bash
$ node sample.js
```

## おすすめ書籍

<!-- ad link - amazon/rakuten books - newrelic -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"New Relic実践入門 監視からオブザーバビリティへの変革 [ 松本 大樹 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/6599\/9784798166599_1_24.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16655212\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"id":1,"u_tx":"Amazonで見る","u_bc":"#ff9900","u_url":"https:\/\/amzn.to\/3ac5kOH","a_id":3351917,"p_id":170,"pl_id":27060,"pc_id":185,"s_n":"amazon","u_so":0},{"u_tx":"楽天ブックスで見る","u_bc":"#bf0000","u_url":"https:\/\/a.r10.to\/hNK5GW","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":2,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16655212\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"njES6","s":"s"});
</script>
<div id="msmaflink-njES6">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>