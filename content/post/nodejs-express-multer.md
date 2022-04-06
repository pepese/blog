---
title:       "Node.js・Express・Multerでファイルアップロード・ダウンロード"
URL:         "nodejs-express-multer"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-09-21
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- yarn
- express
- javascript
- multer
categories:
- tech
---

Node.js/Expressアプリケーションへファイルアップロードしてみる。  
使用するツール・ライブラリは以下。

- [Multer](https://github.com/expressjs/multer)

<!--more-->

以下の記事を読んだ前提で書く。

- [Express入門](https://blog.pepese.com/nodejs-express-basics/)

# 環境設定

```bash
$ yarn add multer
```

# 実装

controller 層へ以下のような実装を行う。

## ファイルアップロード

```javascript
const multer  = require('multer');

// アップロードされたファイルをストレージへ保存する場合
const storage = multer.diskStorage({
  // ファイルの保存先を指定
  destination: (req, file, cb) => {
    cb(null, '/tmp');
  },
  // ファイル名を指定(オリジナルのファイル名を指定)
  filename: (req, file, cb) => {
    cb(null, file.originalname);
  }
});

// アップロードされたファイルを一時的にメモリへ保存する場合
const storage = multer.memoryStorage();

// 1 つのファイルをアップロードする場合
const upload_ = multer({ storage: storage }).single('qafile');

const upload = (req, res, next) => {
  upload_(req, res, (err) => {
    if (err) {
      next(err);
    }
    const csvData = req.file.buffer;
    const mimetype = req.file.mimetype;
    let message = '';
    let qaData = [];
    let errorData = [];
    if(mimetype == 'text/csv' || mimetype == 'application/vnd.ms-excel') {
      // 何らかのファイルの処理ロジック
      // テキストファイルであれば `csvData.toString()` してから処理する
    }
  }
}
```

## ファイルダウンロード

ダウンロード処理には特別ライブラリは必要ない。  
CSVなどのテキストデータの場合は以下。

```javascript
const download = (req, res, next) => {
  let str = 'hoge, hoge, hoge';
  res.setHeader('Content-disposition', 'attachment; filename=' + 'hoge.csv');
  res.setHeader('Content-type', 'text/csv; charset=UTF-8');
  res.write(str);
  res.end();
};
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