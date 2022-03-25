---
title:       "Node.js・Express・Multerでファイルアップロード・ダウンロード"
URL:         "nodejs-express-multer"
subtitle:    ""
description: ""
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

```sh
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

<!-- amazon affiliate kindle node.js --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08HRMTXHB&linkId=f02e6af82c7864b6df5fd31c0639d4bf"></iframe>