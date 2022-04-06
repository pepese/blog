---
title:       "Node.jsでDynamoDBへアクセスする"
URL:         "nodejs-dynamodb"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-29
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- yarn
- aws
- dynamodb
categories:
- tech
---

Node.js から DynamoDB にアクセスしてみる。

<!--more-->

以下の記事を読んだ前提で書く。

- [Node.js/npm入門](https://blog.pepese.com/nodejs-npm-basics/)
- [DynamoDB入門](https://blog.pepese.com/aws-dynamodb-basics/)

# 環境構築

```bash
$ mkdir dynamodb-js
$ cd dynamodb-js
$ yarn init
$ yarn add aws-sdk
$ touch .gitignore
$ aws dynamodb create-table \
  --attribute-definitions '[{"AttributeName":"test_hash","AttributeType":"S"},{"AttributeName":"test_range","AttributeType":"S"}]' \
  --table-name 'test_table' \
  --key-schema '[{"AttributeName":"test_hash","KeyType":"HASH"},{"AttributeName":"test_range","KeyType":"RANGE"}]' \
  --provisioned-throughput '{"ReadCapacityUnits":5,"WriteCapacityUnits":5}'
```

`.gitignore` を下記のように編集しておく。

```
node_modules
.DS_Store
./**/.DS_Store
```

# 実装

[API Document](http://docs.aws.amazon.com/AWSJavaScriptSDK/latest/AWS/DynamoDB.html)を参考のこと。  
以下を作成する。

- app/dynamodb.js
- app/putItem.js
- app/getItem.js
- app/deleteItem.js

[DynamoDB入門](https://blog.pepese.com/aws-dynamodb-basics/) で作成したテーブルにアクセスする前提。

## app/dynamodb.js

```javascript
const aws = require("aws-sdk");
const dynamodb = new aws.DynamoDB({region: 'ap-northeast-1'});

module.exports = dynamodb;
```

## app/putItem.js

```javascript
const path = require('path');
const dynamodb = require(path.join(__dirname, 'dynamodb'));

const params = {
  TableName: "test_table",
  Item:{
    "test_hash": {
      S: "xxxxx3"
    },
    "test_range": {
      S: "yyyyy3"
    },
    "test_value": {
      S: "zzzzz3"
    }
  }
};

dynamodb.putItem(params, (err, data) => {
  if (err) {
    console.error("Unable to add item. Error JSON:", JSON.stringify(err, null, 2));
  } else {
    console.log("Added item:", JSON.stringify(data, null, 2));
  }
});
```

## app/getItem.js

```javascript
const path = require('path');
const dynamodb = require(path.join(__dirname, 'dynamodb'));

const params = {
  TableName: "test_table",
  Key:{
    "test_hash": {
      S: "xxxxx"
    },
    "test_range": {
      S: "yyyyy"
    }
  },
  AttributesToGet: [
    "test_value"
  ]
};

dynamodb.getItem(params, (err, data) => {
  if (err) {
    console.error("Unable to add item. Error JSON:", JSON.stringify(err, null, 2));
  } else {
    console.log("Added item:", JSON.stringify(data, null, 2));
  }
});
```

## app/deleteItem.js

```javascript
const path = require('path');
const dynamodb = require(path.join(__dirname, 'dynamodb'));

const params = {
  TableName: "test_table",
  Key:{
    "test_hash": {
      S: "xxxxx"
    },
    "test_range": {
      S: "yyyyy"
    }
  }
};

dynamodb.deleteItem(params, (err, data) => {
  if (err) {
    console.error("Unable to add item. Error JSON:", JSON.stringify(err, null, 2));
  } else {
    console.log("Added item:", JSON.stringify(data, null, 2));
  }
});
```

# 実行

```bash
$ node app/putItem.js
$ node app/getItem.js
$ node app/deleteItem.js
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