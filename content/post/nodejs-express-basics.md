---
title:       "Express入門"
URL:         "nodejs-express-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-14
author:      "ぺーぺーSE"
image:       ""
tags:
- express
- node.js
- npm
- javascript
categories:
- tech
---

Node.jsのWebフレームワーク `Express` 触ってみた。

<!--more-->

下記の記事を読んだテイで書く。

- [Node.js・npm入門](https://blog.pepese.com/nodejs-basics/)

# 構築

## Expressプロジェクトの作成

プロジェクト作成は以下。

```sh
$ mkdir express-sample          // プロジェクトディレクトリの作成
$ cd express-sample
$ npm init
```

Angular CLI 等で既にプロジェクトを作成している場合は、上記は行わず以下から実行。

```sh
$ npm i -S express@5.0.0-alpha.6 body-parser cookie-parser debug morgan pug serve-favicon request fs file-stream-rotator node-sass-middleware
$ touch .gitignore
$ mkdir app                     // サーバサイドExpressアプリ用のソースディレクトリ作成
$ touch app/app.js              // Expressアプリケーション起動ポイントの作成
$ mkdir app/models              // Mongoose（MongoDB）用のモデルディレクトリ作成
$ touch app/models/.gitkeep
$ mkdir app/repositories        // DAO/Repository用のディレクトリ作成
$ touch app/repositories/.gitkeep
$ mkdir app/config              // 設定ファイル用ディレクトリ作成
$ touch app/config/config.json  // 環境差分ファイル作成
$ mkdir app/log                 // ログ出力用ディレクトリ作成
$ touch app/log/.gitkeep
$ mkdir app/spec                // テストスクリプト用のディレクトリ作成
$ touch app/spec/.gitkeep
```

Expressアプリケーションのソースディレクトリは `app/` だけで完結するようにする。  
また、 forever を導入しておく。

```sh
$ npm install -g forever
$ ndenv rehash
```

さらに `.gitignore` に以下を追加しておく。

```
# Logs
/app/log
*.log

# dependencies
/node_modules

# System Files
.DS_Store
Thumbs.db
```

### フロントエンドアプリケーションをプロジェクトに同梱する場合、REST APIアプリを作成する場合

この場合、以下のようにディレクトリを作成する。

```sh
$ mkdir app/api           // REST API用のコントローラディレクトリ作成
$ touch app/api/.gitkeep
```

フロントエンドアプリケーションのトランスパイル結果は `express-sample/dist` に出力されるものとし、サーバサイドアプリケーション（ `express-sample/app` ）は `express-sample/dist` をExpressの公開ディレクトリとする。  
フロントエンドアプリケーションは `app/api` に実装したAPIにアクセスする。

### フロントエンドアプリケーションをプロジェクトに同梱せず、ExpressからテンプレートエンジンVIEWを使用する場合

この場合、以下のようにディレクトリを作成する。  
なお、 SCSS を使用する。

```sh
$ mkdir app/controllers                  // VIEW用のコントローラディレクトリ作成
$ touch app/controllers/.gitkeep
$ mkdir app/views                        // 画面・テンプレート（Pugなど）用のディレクトリ作成
$ touch app/views/.gitkeep
$ mkdir app/public                       // 静的コンテンツ用のディレクトリ作成
$ mkdir app/public/javascripts           // JS用のディレクトリ作成
$ touch app/public/javascripts/.gitkeep
$ mkdir app/public/stylesheets           // SCSS用のディレクトリ作成
$ touch app/public/stylesheets/style.scss
$ mkdir app/public/images                // 画像用のディレクトリ作成
$ touch app/public/images/.gitkeep
```

## ソースコードの作成

 「フロントエンドアプリケーションをプロジェクトに同梱する場合」は以下を参照。

- [所謂MEANスタックを作る](https://blog.pepese.com/mean-stack-basics/)

ここでは、「フロントエンドアプリケーションをプロジェクトに同梱せず、Expressからテンプレートエンジンを使用する場合」で、最低限動くViewアプリを構築するために以下のスクリプトを実装する。

- `app/app.js`
    - Expressアプリケーションのミドルウェアの設定と起動処理
- `app/controllers/router.js`
    - Viewを処理するモジュールへのルーティングを行う
- `app/controllers/get_index.js`
    - Index画面を表示する
- `app/controllers/get_users.js`
    - Users画面を表示する
- `app/public/stylesheets/style.scss`
- `app/views/layout.pug`
- `app/views/index.pug`
- `app/views/error.pug`
- `app/config/config.json`
    - 環境差分設定ファイル

### app/app.js

```javascript
const express = require('express');
const http = require('http');
const path = require('path');
const favicon = require('serve-favicon');
const logger = require('morgan');
const cookieParser = require('cookie-parser');
const bodyParser = require('body-parser');
const sassMiddleware = require('node-sass-middleware');
const fs = require('fs');
const FileStreamRotator = require('file-stream-rotator');
const util = require('util');

// 環境差分ファイル
const env = process.env.NODE_ENV || "development";
const config = require(path.join(__dirname, 'config', 'config.json'))[env];

/**
 * APIルーティング先のロード
 */
//const api_router = require('./api/router');

/**
 * Viewのルーティング先のロード
 */
const controllers_router = require('./controllers/router');

/**
 * express本体の作成
 */
const app = express();

/**
 * ログの設定
 */
// ログディレクトリの定義
const logDirectory = __dirname + '/log';
// ディレクトリがなければ作成する
fs.existsSync(logDirectory) || fs.mkdirSync(logDirectory);
// dailyローテーションの設定でアクセスログのwrite streamを作成
const accessLogStream = FileStreamRotator.getStream({
  filename: logDirectory + '/access-%DATE%.log',
  frequency: 'daily',
  verbose: false,
  date_format: "YYYY-MM-DD"
});
// dailyローテーションの設定でコンソールログのwrite streamを作成
const consoleLogStream = FileStreamRotator.getStream({
  filename: logDirectory + '/console-%DATE%.log',
  frequency: 'daily',
  verbose: false,
  date_format: "YYYY-MM-DD"
});
// console.logの出力をconsoleLogStreamへ流す
console.log = (d) => {
  consoleLogStream.write(util.format(d) + '\n');
}

/**
 * Viewエンジンの設定
 */
app.set('views', path.join(__dirname, 'views'));
app.set('view engine', 'pug');

/**
 * サードパーティミドルウェア
 */
// 「/public」 に favicon を置いてからコメント解除
// app.use(favicon(path.join(__dirname, 'public', 'favicon.ico')));
// JSONボディパーサ
// app.use(bodyParser.json());
// ボディのURLエンコード
app.use(bodyParser.urlencoded({ extended: false }));
// ロガー（アクセスログ）、上記「ログの設定」と関係
app.use(logger('combined', {stream: accessLogStream}));
// cookieパーサ
app.use(cookieParser());
// SASSトランスパイル
app.use(sassMiddleware({
  src: path.join(__dirname, 'public'),
  dest: path.join(__dirname, 'public'),
  indentedSyntax: false, // true = .sass and false = .scss
  sourceMap: true
}));

/**
 * ビルドインミドルウェア
 */
// 静的コンテンツのパスを指定
app.use(express.static(path.join(__dirname, 'public')));
// app.use(express.static(path.join(__dirname, '../dist')));

/**
 * APIルーティング
 */
// app.use('/api', api_router);
// 上記のルーティングにマッチしなかった場合はindex.htmlを返す
// app.get('*', (req, res) => {
//   res.sendFile(path.join(__dirname, '..dist/index.html'));
// });

/**
 * VIEWルーティング
 */
app.use('/', controllers_router);
// 上記のルーティングにマッチしなかった場合はエラー処理ミドルウェアに処理を流す
app.use((req, res, next) => {
  var err = new Error('Not Found');
  err.status = 404;
  next(err);
});

/**
 * エラー処理ミドルウェア
 */
// Viewの場合のエラー処理
app.use((err, req, res, next) => {
  res.locals.message = err.message;
  res.locals.error = req.app.get('env') === 'development' ? err : {};
  res.status(err.status || 500);
  res.render('error');
});

/**
 * ポートの設定
 */
const port = process.env.PORT || config.port || '3000';
app.set('port', port);

module.exports = app;

/**
 * HTTP server作成
 */
if(!module.parent){
  const server = http.createServer(app);
  // listenポートを指定してHTTP serverを起動
  server.listen(port, () => console.log(`API running on localhost:${port}`));
}
```

### app/controllers/index.js

```javascript
const express = require('express');
const router = express.Router();

router.get('/', require('./get_index'));
router.get('/users', require('./get_users'));
// 以下、DB接続用サンプル
router.get('/clients', require('./get_clients'));
// 以下、HTTP(S)リクエスト用サンプル
router.get('/ip', require('./get_ip'));
// 以下、非同期HTTP(S)リクエスト用サンプル
router.get('/ip_promise', require('./get_ip_promise'));

module.exports = router;
```

### app/controllers/get_index.js

```javascript
const get_index = (req, res, next) => {
  res.render('index', { title: 'Express' });
};

module.exports = get_index;
```

### app/controllers/get_users.js

```javascript
const get_users = (req, res, next) => {
  res.send('respond with a resource');
};

module.exports = get_users;
```

### app/public/stylesheets/style.scss

```css
$my-color: #be003f;

body {
  color: $my-color;
}
```

### app/views/layout.pug

```pug
doctype html
html
  head
    title= title
    link(rel='stylesheet', href='/stylesheets/style.css')
  body
    block content
```

### app/views/index.pug

```pug
extends layout

block content
  h1= title
  p Welcome to #{title}
```

### app/views/error.pug

```pug
extends layout

block content
  h1= message
  h2= error.status
  pre #{error.stack}
```

### app/config/config.json

```javascript
{
  "development": {
    "port": "3000"
  },
  "test": {
    "port": "3000"
  },
  "production": {
    "port": "3000"
  }
}
```

### 起動

```
$ NODE_ENV=production forever start app/app.js
```

`NODE_ENV` で環境を指定することができる。デフォルトは `development` 。


# ちょっとExpressの機能解説

## Expressアプリケーション

Expressアプリケーションの最小単位は以下のように作成する。

```javascript
const express = require('express');
const app = express();
```

## ミドルウェア関数

Expressは、単独では最小限の機能を備えたルーティングとミドルウェアのWebフレームワーク。  
Expressアプリケーションは様々な **ミドルウェア関数** 呼び出しを行うことで様々な機能を実現する。  
ミドルウェア関数はコールバック関数で以下の種類がある。

- アプリケーション・レベルのミドルウェア
- ルーター・レベルのミドルウェア
- エラー処理ミドルウェア
- 標準装備のミドルウェア
- サード・パーティー・ミドルウェア

### アプリケーション・レベルのミドルウェア

以下のように記述する。

```javascript
app.use([path,] callback [, callback...])
```

`path` を記載しない場合は **全てのリクエスト** に対してミドルウェア関数（ `callback` :コールバック関数）が実行される。  
コールバック関数には `(req, res, next)` の3つを渡すことができる。
`next()` を実行すると次のミドルウェア関数に処理が移る。（ **`next()` を実行しないと処理は終わる** ）

```javascript
app.use( (req, res, next) => {
  console.log('Time:', Date.now());
  next();
});
```

`path` を記載すると **指定したパスへのリクエスト** に対してミドルウェア関数が実行される。  

```javascript
app.use('/user/:id', (req, res, next) => {
  console.log('Request Type:', req.method);
  next();
});
```

```javascript
app.get('/user/:id', (req, res, next) => {
  res.send('USER');
});
```

**`app.use` は `app.[all|get|post|put|delete|etc]` よりも全て前に記述する必要がある** 。

### ルーター・レベルのミドルウェア

ルーター・レベルのミドルウェアは、express.Router() のインスタンスにバインドされる点を除き、アプリケーション・レベルのミドルウェアと同じ。

```javascript
const express = require('express');
const router = express.Router();
```

正直、 **アプリケーション・レベルとルーター・レベルの違いはよくわからない** 。  
以下の方針で使い分けることにする。

- アプリケーション・レベル
    - **アプリ全体** 、 **全てのHTTPメソッド** へ影響する処理
    - `app.use()` を使用する
- ルーター・レベル
    - **特定のパス** 、 **特定のHTTPメソッド** へのルーティング
    - `router.[all|get|post|put|delete](path, callback [, callback...])` を使用する

### エラー処理ミドルウェア

引数は ` (err、req、res、next)` となり `err` が追加される。

```javascript
app.use( (err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send('Something broke!');
});
```

### 標準装備のミドルウェア

Expressの標準装備のミドルウェア関数は `express.static` のみ。  
過去はもったあったらしいが、個別モジュール化されたらしい。

```javascript
express.static(root, [options])
```

静的コンテツを提供する機能。  
`root` 引数は、静的コンテンツを提供するルートディレクトリを指定する。  
`options` オブジェクトにはプロパティを指定することができる。

```javascript
var options = {
  dotfiles: 'ignore',
  etag: false,
  extensions: ['htm', 'html'],
  index: false,
  maxAge: '1d',
  redirect: false,
  setHeaders: function (res, path, stat) {
    res.set('x-timestamp', Date.now());
  }
}

app.use(express.static('public', options));
```

### サード・パーティー・ミドルウェア

`npm` でモジュールをインストールして、アプリケーション・レベルまたはルーター・レベルでロードして使用する。

```javascript
const express = require('express');
const bodyParser = require('body-parser'); // サード・パーティー・ミドルウェア

const app = express();

// Parsers for POST data
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: false }));
```

## モジュール呼び出し

`module.exports` と `require` を使用すると、モジュールの外部参照が可能になる。  
以下のような `index.js` があったとすると

```javascript
router.post('/', (req, res) => {
  ...
}
module.exports = router; // 外部モジュールからrequireできるようになる
```

`app.js` から以下のように `index.js` の機能を呼び出すことができる。

```javascript
const index = require('./index');
```

## Expressアプリケーションの設定

Expressでは、以下の記載で「key-value」形式でアプリケーションに対して様々な設定ができる。

```javascript
app.set(name, value);
app.get(name);
```

代表的な設定項目としては以下（[詳細](http://expressjs.com/ja/api.html#app.settings.table)）があるが、単にインメモリキーバリューストア（好きな「key-value」を設定できる）としても使用できる。

|Property|Type|Description|Default|
|:---:|:---:|:---|:---:|
|case sensitive routing|Boolean|ルーティング時、URLの大文字・小文字を区別する。|N/A (undefined)|
|env|String|環境名。productionとかdevelopmentとか。|process.env.NODE_ENV or “development”|
|etag|Varied| `Etag` レスポンスヘッダの設定を行う。|weak|
|jsonp callback name|String|JSONP callback名を設定する。|“callback”|
|json replacer|Varied|`JSON.stringify` で利用される引数 `replacer` の設定。JSONの各メンバの値の変換の設定ができる。|N/A (undefined)|
|json spaces|Varied|`JSON.stringify` で利用される引数 `space` の設定。JSONパース時に見やすいようにインデントのスペースを設定できる。|N/A (undefined)|
|query parser|Varied|URLクエリパラメータのパースの設定ができる。 `simple` 、 `extended` 、カスタマイズから選択できる。 `simple` はNodeJSが内包するNativeのクエリパーサ。 `extended` は サードパーティモジュール `qs` 。カスタマイズは関数を設定し、その関数はクエリパラメータの文字列を受け取り、キー・バリューのオブジェクトを返却するよう実装する。|"extended"|
|strict routing|Boolean|ルーティングにおいてURLの `/foo` と `/foo/` を区別するかどうか設定する。|N/A (undefined)|
|subdomain offset|Number|サブドメインにアクセスするために削除するホストのドット区切り部分の数。|2|
|trust proxy|Varied|信頼するWebプロキシの設定を行う。詳細は、[ここ](http://expressjs.com/ja/guide/behind-proxies.html)を参照。|false (disabled)|
|views|String or Array|Viewを配置するディレクトリを文字列もしくは配列で設定する。|process.cwd() + '/views'|
|view cache|Boolean|Viewテンプレートコンパイルキャッシュを有効にする。|true in production, otherwise undefined.|
|view engine|String|Viewエンジンの設定を行う。|N/A (undefined)|
|x-powered-by|Boolean|"X-Powered-By: Express" HTTPヘッダを有効にする。|true|

## モジュール間で変数をやりとりする方法

- global変数を使う
    - `const global.hoge = 'hogehoge';` みたいに、頭にglobalを付けるとglobalスコープで扱われる。
    - ただし、 `require('./global.js');` のようにグローバル変数を定義したモジュールを使用するモジュールでロードする必要がある。
    - グローバル領域を汚染するので注意が必要
- 共有変数用のモジュールを作成する
    - `module.exports = {};` とだけ書いた `common.js` を作成する
    - 他モジュールからは `require('./common').hoge = hoge;` で変数を代入でき、 `const hoge = require('./common').hoge` で変数を参照できる
    - どんな変数を作ったからわからなくなるので、 `common.js` にはコメントくらい残しておく必要がある

# おすすめ書籍

<!-- amazon affiliate kindle node.js --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08HRMTXHB&linkId=f02e6af82c7864b6df5fd31c0639d4bf"></iframe>