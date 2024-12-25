---
title:       "Node.js・npm入門"
URL:         "nodejs-npm-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2024-12-25
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- npm
- javascript
categories:
- tech
---

Node.js（npm）についてまとめる。

<!--more-->

npm（Node Packege Manager）は、Node.js用のパッケージ管理コマンド。  
JavaScriptエコシステムツール・ライブラリはnpmで導入できるものが多い。  
npmはNode.jsと同時にインストールされるので、Node.jsを入れておけばいい。  
また、Node.jsはJavaScriptの実行エンジン。

ちなみに[ここ](http://node.green)でNode.jsのES6対応状況がわかる。  
v8.3.0で **99%** カバーしている。

<!--more-->

# インストール

ここでは、Node.jsのインストール方法は以下の「asdf」を利用する。

- [マルチランタイムバージョン管理ツールasdf](https://blog.pepese.com/asdf-basics/)

```bash
$ asdf plugin add helm       # Node.jsプラグインを追加
$ asdf list-all nodejs       # インストールできるNode.jsのバージョンを確認
$ asdf install nodejs 23.5.0 # バージョンを指定してインストール
$ asdf global nodejs 23.5.0  # バージョンの切り替え
$ node -v                    # Node.js のバージョン確認
v23.5.0
$ npm install -g npm         # npm のバージョンアップ
$ npm -v                     # npm のバージョン確認
11.0.0
```

# npmの使い方

公式ドキュメントは[ここ](https://docs.npmjs.com)。  
プロキシの設定が必要な場合は以下。

```bash
$ npm config set proxy http://<username>:<password>@<proxy-host>:<proxy-port>
$ npm config set https-proxy http://<username>:<password>@<proxy-host>:<proxy-port>
```

## プロジェクトの作成

任意のディレクトリを作成して `npm init` を実行すると `package.json` が作成され、プロジェクトとなる。  
いろいろ聞かれるが、全部Enterで問題無い。あとで変更可能。

```bash
$ mkdir nodejs-sample
$ cd nodejs-sample
$ npm init
```

`package.json` はMavenでいう `pom.xml` みたいなもの。（Javaの人向けでごめん）

## パッケージのインストール

### グローバルインストール

`npm install -g <package_name>@<version>` でパッケージのインストールが可能。  
`-g` オプションがついてるのがポイント。  
アンインストールは `npm uninstall -g <package_name>` 。  
インストールパッケージ一覧の確認は `npm list -g` 。  

インストール先は以下で確認できる。

```
# グローバルインストール先のディレクトリ
$ npm root -g

# グローバルインストール先の、コマンドディレクトリ
$ npm bin -g
```

### ローカルインストール

`npm install <option> <package_name>@<version>` でパッケージのインストールが可能。  
インストールしたパッケージは `package.json` に追記され、 `node_modules/` 配下に配備される。  
オプションは以下の通り。

- `--save` 、 `-S`
    - `package.json` の `dependencies` に追記される
    - `npm install  --production` した時に `mode_modules/` に配備される
    - 開発したソフトウェアを動かすのに必要な依存ライブラリ
- `--save-dev` 、 `-D`
    - `package.json` の `devDependencies` に追記される
    - `npm install --dev` した時に `mode_modules/` に配備される
    - ソフトウェアを開発する際に必要になるツール・ライブラリ
- `--save-optional` 、 `-O`
    - `package.json` の `optionalDependencies` に追記される
    - `npm install` した時に `mode_modules/` に配備される（失敗してもスルーされる）

デフォルト設定の場合、 `npm install` （オプション無）すると、 `dependencies` 、 `devDependencies` 、 `optionalDependencies` の全てがインストールされる。  
アンインストールは `npm uninstall <package_name>` 。  
インストールパッケージ一覧の確認は `npm list` 。

### パッケージの使い方

npmで導入したJavaScriptライブラリ（ `node_modules/` 配下にある）は、JavaScriptのソースコードからほぼ以下の形式で呼び出すことができる。

```javascript
var req = require('request')
```

上記は `request` モジュールを呼び出して `req` 変数へ代入している例。

## npmスクリプト

`package.json` の `scripts` にスクリプトを登録することにより簡易に実行することができる。  
例えば以下。

```javascript
"scripts": {
  "test": "mocha --require test/support/env --reporter spec --bail --check-leaks test/ test/acceptance/",
  "test-ci": "istanbul cover node_modules/mocha/bin/_mocha --report lcovonly -- --require test/support/env --reporter spec --check-leaks test/ test/acceptance/",
  "test-cov": "istanbul cover node_modules/mocha/bin/_mocha -- --require test/support/env --reporter dot --check-leaks test/ test/acceptance/",
  "test-tap": "mocha --require test/support/env --reporter tap --check-leaks test/ test/acceptance/"
  }
```

上記のようにスクリプトを定義することにより `npm test` 、 `npm test-ci` といった具合で登録したスクリプトを実行することができる。

### `package.json` の更新

手動で `package.json` 内のライブラリバージョンを変更してもよいが、 `npm-check-updates` を利用すると楽。

```bash
$ npm install -g npm-check-updates
$ which ncu
/usr/local/bin/ncu
```

`ncu` コマンドで `package.json` のライブラリの最新バージョンを確認できる。

```bash
$ ncu
Checking /path/to/project/package.json
[====================] 17/17 100%

 hexo-deployer-git       ^0.3.1  →  ^1.0.0 
 hexo-generator-archive  ^0.1.4  →  ^0.1.5 
 hexo-generator-index    ^0.2.0  →  ^0.2.1 
 hexo-renderer-ejs       ^0.3.0  →  ^0.3.1 
 hexo-renderer-marked    ^0.3.2  →  ^1.0.1 
 hexo-renderer-pandoc    ^0.2.6  →  ^0.3.0 
 hexo-renderer-stylus    ^0.3.1  →  ^0.3.3 
 hexo-server             ^0.2.0  →  ^0.3.3 

Run ncu -u to upgrade package.json
```

`ncu -u` で `package.json` が書き換えられる。

# Node.jsの使い方

Node.jsのAPIリファレンスは[ここ](https://nodejs.org/api/)。

## シンプルに動かしてみる

まず、Node.jsをJavaScriptエンジンとしてシンプルに動かしてみる。

■hello.js

```javascript
exports.sayHello = function(){ return "Hello World !" }
```

■main.js

```javascript
var hello = require('./hello.js');
console.log(hello.sayHello());
```

上記JSファイルを作成して下記を実行。

```bash
$ node main.js
Hello World !
```

`node <JSファイル>` コマンドで実行できる。

## HTTPサーバ

Node.jsでHTTPサービスを立ち上げてみる。

■app.js

```javascript
var http = require('http');
http.createServer(function (req, res) {
  res.writeHead(200, {'Content-Type': 'text/plain'});
  res.end('Hello World !\n');
}).listen(1337, "127.0.0.1");
console.log('Server running at http://127.0.0.1:1337/');
```

下記を実行してブラウザで `http://127.0.0.1:1337/` へアクセスすると「Hello World !」と表示される。

```bash
$ node app.js
```

`Ctr+C` でサービスを停止できる。  
この方法でHTTPサービスを起動した場合、ロジックでエラーが発生するとプロセスが終了する。  
エラーが発生してもプロセスが終了しないようにする方法の１つとして **forever** の導入がある。

■foreverのインストール、起動、停止

```bash
$ npm install -g forever
$ forever start app.js
$ forever stop app.js
```

## Node.jsに関する話題

- [Node.jsでのJavaScriptメモリリークを発見するための簡単ガイド](http://postd.cc/simple-guide-to-finding-a-javascript-memory-leak-in-node-js/)
    - JavaScriptは **ガベージコレクション** を行う言語。

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