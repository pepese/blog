---
title:       "Node.js・Expressでユニットテスト"
URL:         "nodejs-express-test"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-16
author:      "ぺーぺーSE"
image:       ""
tags:
- node.js
- npm
- express
- javascript
- mocha
- chai
- sinon.js
- istanbul
- nyc
categories:
- tech
---

Node.js/Expressアプリケーションのテストをしてみる。

<!--more-->
使用するツール・ライブラリは以下。

- テスティングフレームワーク
    - [mocha](https://mochajs.org/)
        - デフォルトでは `./test/*.js` 、 `./test/*.coffee` をテストスクリプトとして認識
        - `mocha.opts` というファイルにオプションを設定できる模様（[参考](https://mochajs.org/#mochaopts)）
- アサート
    - [Chai](http://chaijs.com/)
- モック
    - [sinon.js](http://sinonjs.org/)
- カバレッジ
    - [Istanbul](https://istanbul.js.org/)
        - 公式の案内にもある通り、以下の `nyc` 経由で `istanbul` を利用する
    - [nyc](https://github.com/istanbuljs/nyc)
        - tap、mocha、AVA といったJSテスティングフレームワークと Istanbul をうまく連携させるコマンドラインツール
        - [mocha用のチュートリアル](https://istanbul.js.org/docs/tutorials/mocha/)

なお、タスクランナーは使用せず、npmスクリプトを使用する。

以下の記事を読んだ前提で書く。

- [Express入門](https://blog.pepese.github.com/nodejs-express-basics/)

# 環境設定

## グローバルインストール

```bash
$ yarn global add nyc
$ ndenv rehash
```

## ローカルインストール

[Express入門](https://blog.pepese.com/nodejs-express-basics/)で作成したプロジェクトにて以下を導入する。

```bash
$ yarn add mocha chai sinon nyc rimraf --dev
```

## ディレクトリ作成

先に紹介した記事の通り、Expressアプリケーションのソースディレクトリは `app/` だけで完結するようにする。  
以下のようにテストスクリプト用のディレクトリを作成する。

```bash
$ mkdir app/spec
$ mkdir app/spec/controllers
```

# ユニットテストの実装

先に紹介した記事のスクリプトをテストする。

## packege.json

以下を加筆する。

```
{
  // 上記省略
  "nyc": {
    "check-coverage": true,
    "per-file": true,
    "lines": 90,
    "statements": 90,
    "functions": 90,
    "branches": 90,
    "include": [
      "app/"
    ],
    "exclude": [
      "app/spec/**/*.spec.js",
      "app/app.js",
      "app/controllers/router.js",
      "app/config",
      "app/coverage",
      "app/log",
      "app/public"
    ],
    "reporter": [
      "html",
      "text"
    ],
    "require": [],
    "extension": [
      ".js"
    ],
    "cache": true,
    "all": true,
    "report-dir": "app/coverage"
  },
  "scripts": {
    "clean": "rimraf .nyc_output ./app/coverage",
    "test": "mocha app/spec/*.spec.js app/spec/**/*.spec.js"
  },
  // 下記省略
}
```

## テストスクリプト `app/spec/controllers/get_index.spec.js`

`app/controllers/get_index.js` を対象としたテストスクリプトを以下のように作成する。

```javascript
const sinon = require('sinon');
const chai = require('chai');
const expect = chai.expect;

const get_index = require('../../controllers/get_index');

describe('/', () => {
  it('Index画面が1回レンダリングされること', (done) => {
    let req,res,spy;
    req = res = {};
    spy = res.render = sinon.spy();

    get_index(req, res);
    expect(spy.calledOnce).to.equal(true);

    done();
  });
});
```

## テストスクリプト `app/spec/controllers/get_users.spec.js`

`app/controllers/get_users.js` を対象としたテストスクリプトを以下のように作成する。

```javascript
const sinon = require('sinon');
const chai = require('chai');
const expect = chai.expect;

const get_index = require('../../controllers/get_users');

describe('/users', () => {
  it('Users画面が1回レンダリングされること', (done) => {
    let req,res,spy;
    req = res = {};
    spy = res.send = sinon.spy();

    get_index(req, res);
    expect(spy.calledOnce).to.equal(true);

    done();
  });
});
```

## テストの実行

npmスクリプトで以下のようにテストを実行する。

```bash
$ npm test

  /
    ✓ Index画面が1回レンダリングされること

  /users
    ✓ Users画面が1回レンダリングされること


  2 passing (8ms)
```

カバレッジレポートは `nyc` を用いて以下のように出力する。

```bash
$ nyc npm test

  /
    ✓ Index画面が1回レンダリングされること


  /users
    ✓ Users画面が1回レンダリングされること


  2 passing (10ms)

--------------|----------|----------|----------|----------|----------------|
File          |  % Stmts | % Branch |  % Funcs |  % Lines |Uncovered Lines |
--------------|----------|----------|----------|----------|----------------|
All files     |      100 |      100 |      100 |      100 |                |
 get_index.js |      100 |      100 |      100 |      100 |                |
 get_users.js |      100 |      100 |      100 |      100 |                |
--------------|----------|----------|----------|----------|----------------|
```

`package.json` の `nyc` の設定の通りだが、HTML形式のレポートが `app/coverage` 配下に出力される。  
なお、 `$ npm run-script clean` で `nyc` が出力する `.nyc_output` 、 `app/coverage` を削除する。

# ライブラリ・ツールの概念を解説

個々のライブラリを軽く解説する。  
なお、各ライブラリを連携するプラグインライブラリに以下のようなものがある。

- mocha-sinon
    - [Github](https://github.com/elliotf/mocha-sinon)
- [sinon-chai](http://chaijs.com/plugins/sinon-chai/)
    - [Github](https://github.com/domenic/sinon-chai)
    - chai-sinon というのもあるが、違いがわからん。。。

## mocha

mocha では以下のような関数を使用してテストを記述していく。

|関数|概要|
|:---|:---|
|describe|ネスト管理可能な階層。テスト対象を宣言する。|
|before|describeの前に実行される。前提条件を記述する。|
|after|describeの後に実行される。後処理を記述する。|
|beforeEach|各describeの前に実行される。前提条件を記述する。|
|afterEach|各describeの後に実行される。後処理を記述する。|
|it|検証内容を記述する。|

## Chai

Chai の代表的な機能には以下がある。

- Should
- Expect
- Assert

Should と Expect は基本同じインターフェースだが、Sholudはオブジェクト自身を拡張してsholudメソッドを追加して実装されているのに対して、expectは関数で引数に評価する値を渡すことでオブジェクトの検査を行う。

```
// hogeオブジェクトとfuge文字列が等しいかどうか検査

// Should
hoge.should.equal('fuge');

// Expcet
expect(hoge).to.equal('fuge');
```

## sinon.js

sinon.js の代表的な機能には以下がある。

- スパイ
    - 関数がどのように呼び出されたかを記録する
- スタブ
    - 関数の戻り値をあらかじめ設定し、その結果でテストを行う
- モック
    - 実行前に関数の実行回数など期待する結果を指定しておく
- フェイク
    - 問い合わせるDBやサーバ処理などを単純な実装に置き換える

[API documentation - Sinon.JS - v3.2.0](http://sinonjs.org/releases/v3.2.0/)

### スパイ

スパイオブジェクトの作成方法は以下。

|呼び出し方法|概要|
|:---|:---|
|let spy = sinon.spy();|空のスパイオブジェクトを生成。主にコールバックで渡す無名関数などに対して使用する。|
|let spy = sinon.spy(myFunc);|特定の関数に対してスパイオブジェクトを作成する。|
|let spy = sinon.spy(object, "method");|オブジェクト内のメソッドに対してスパイオブジェクトを生成する。|

スパイオブジェクトの主な関数は以下。

|呼び出し方法|概要|
|:---|:---|
|spy.calledWith(arg1, arg2, ...);|指定した引数で関数が呼び出されたかを確認する。|
|spy.callCount|呼び出された回数を返す。|
|spy.called|呼び出された場合にtrueを返す。|
|spy.calledOn(obj);|指定したオブジェクトで関数が実行された場合trueを返す。|
|spy.calledOnce|1回呼び出された場合trueを返す。|
|spy.calledTwice|2回呼び出された場合trueを返す。|
|spy.exceptions|発生したexceptionを返す。|
|spy.returnValues|実行後の戻り値を返す。|
|spy.withArgs(arg1, arg2, ...);|指定した引数で関数が呼び出された場合にのみ、スパイオブジェクトを返す。|

### スタブ

スタブオブジェクトの作成方法は以下。

<table>
<thead>
<tr class="header">
<th align="left">呼び出し方法</th>
<th align="left">概要</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">let stub = sinon.stub();</td>
<td align="left">空のスタブオブジェクトを生成。主にコールバックで渡す無名関数などに対して使用する。</td>
</tr>
<tr class="even">
<td align="left">let stub = sinon.stub(object, “method”);</td>
<td align="left">特定の関数に対してスタブオブジェクトを作成する。</td>
</tr>
<tr class="odd">
<td align="left">let stub = sinon.stub(object, “method”, func);</td>
<td align="left">特定の関数に対してスタブオブジェクトを作成し、指定した関数で処理を上書きします。</td>
</tr>
<tr class="even">
<td align="left">let stub = sinon.stub(object);</td>
<td align="left">対象オブジェクトのメソッドをすべてスタブにする。</td>
</tr>
</tbody>
</table>

スタブオブジェクトの主な関数は以下。

|呼び出し方法|概要|
|:---|:---|
|stub.returns(obj);|呼び出し時のリターン値を指定する。|
|stub.throws(obj);|呼び出し時に指定したExceptionを発生させる。|
|stub.onCall(n);|n回目のスタブオブジェクトを返す。|
|stub.onFirstCall();|1回目のスタブオブジェクトを返す。|
|stub.onSecondCall();|2回目のスタブオブジェクトを返す。|

### モック

モックオブジェクトの作成方法は以下。

<table>
<thead>
<tr class="header">
<th align="left">呼び出し方法</th>
<th align="left">概要</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">let mock = sinon.mock(object);</td>
<td align="left">オブジェクトのモックを作成する。</td>
</tr>
<tr class="even">
<td align="left">let expectation = mock.expects(“method”);</td>
<td align="left"><code>object.method</code> をオーバーライドしてモック関数を作成する。</td>
</tr>
<tr class="odd">
<td align="left">let expectation = sinon.expectation.create(“method”);</td>
<td align="left">モックオブジェクト無しでモック関数を作成する。</td>
</tr>
</tbody>
</table>

スタブオブジェクトの主な関数は以下。

|呼び出し方法|概要|
|:---|:---|
|mock.restore();|全てのモック関数をリストアする。|
|mock.verify();|全てのモック関数の expectation をベリファイする。|
|expectation.atLeast(number);|モック関数が少なくとも呼び出される回数を指定する。|
|expectation.atLeast(number);|モック関数が多くとも呼び出される回数を指定する。|
|expectation.never();|モック関数が決して呼び出されないことを指定する。|
|expectation.once();|モック関数が1回呼び出されることを指定する。|
|expectation.twice();|モック関数が2回呼び出されることを指定する。|
|expectation.exactly(number);|モック関数が呼び出される回数を指定する。|
|expectation.withArgs(arg1, arg2, ...);|モック関数呼び出し時に含まれる引数を指定する。|
|expectation.withExactArgs(arg1, arg2, ...);|モック関数呼び出し時の引数を指定する。|
|expectation.on(object);|モック関数が object から呼び出されることを指定する。|
|expectation.verify();|モック関数が上記の指定にマッチしていたか確認する。|

## フェイク

フェイクには、時間・XMLHttpRequest・サーバーオブジェクトの置き換えができる。  
ここでは省略する。

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