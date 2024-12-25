---
title:       "Vite入門"
URL:         "nodejs-vite-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2024-12-25
author:      "ぺーぺーSE"
image:       ""
tags:
- typescript
- javascript
- node.js
categories:
- tech
---

JavaScript モジュールバンドラの [Vite](https://ja.vite.dev/) についてまとめる。

<!--more-->

# Vite とは？

まず **バンドル** とは、複数のファイルを１つのファイルにまとめて出力することです。  
ブラウザで実行される JavaScript は通常、複数の .js ファイルを 1 つのファイルにまとめて minify されています。  
これを実現するツールを **モジュールバンドラ** と呼びます。  
既存のモジュールバンドラには Webpack などが挙げられますが、 Vite には以下の特徴があります。

- 高速ビルド
  - ブラウザの Native ES Modulesを使用して、より高速なビルドを実現
- 高速ホットリロード
  - HMR(Hot Module Replacement)のメカニズムをNative ES Modules上で実行して実現

ちなみに Vite（ヴィート）と発音するようです。  
フロントエンド界隈でデファクトとなっている React の初期プロジェクトを作成する「create-react-app」は 2023 年春頃からメンテナンスが止まっています。  
Vite には「create-react-app」の代替となる機能を有しており、その事もあって「Vite + React」の組み合わせが人気となっています。  

私見ですが、 Vite は React 以外にも他のフロントエンド開発にも対応していますので、本記事執筆時点では、JavaScript/TypeScript プロジェクトを作成する場合、使いたいフレームワーク・ライブラリのテンプレート（後述）があれば、とりあえず Vite を使えばいいのではないかと思います。（ Next.js は Vite と競合しますので例外とします。）

# 使い方

## プロジェクト作成

Vite では以下のコマンドでプロジェクトを作成します。

```bash
$ npm create vite@latest [プロジェクト名] -- --template [テンプレート]
```

[公式テンプレート](https://github.com/vitejs/vite/tree/main/packages/create-vite) には以下のようなものがあります。  
（ `-ts` とつくと TypeScript のプロジェクトになります。）

- vanilla / vanilla-ts
  - Vanilla JS のプロジェクト
- vue / vue-ts
  - Vue.js のプロジェクト
- react /react-ty
  - React のプロジェクト

話はずれますが、 Vanilla とは素の JavaScript を意味します。「バニラ」はアイスクリームのフレーバーの 1 つでもあり、「余分なものや装飾品のない、シンプルなもの」転じて退屈で地味な物事、を意味するようです。そのことから Vanilla は素の JavaScript というわけです。  
Vanilla JS は、純粋な JavaScript またはプレーンな JavaScript と同義で、ライブラリやフレームワークなどの外部ツールを使用しません。  
ここでは Vanilla JS を用いてプロジェクトを作成してみます。

```bash
$ npm create vite@latest vanilla-ts-sample -- --template vanilla-ts
$ cd vanilla-ts-sample
$ npm install
$ npm run dev
```

また、 Vite には上記の公式テンプレートの他に [コミュニティが管理するテンプレート](https://github.com/vitejs/awesome-vite#templates) も存在します。

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