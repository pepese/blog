---
title:       "Amazon CloudFront入門"
URL:         "aws-cloudfront-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2025-03-05
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- cloudfront
categories:
- tech
---

Amazon CloudFront は、 エッジサービスを提供するサービスです。  
この記事では、Amazon CloudFront をざっくり理解できる概要と使い方を記載します。

<!--more-->

# Amazon CloudFront の概要

Amazon CloudFront は、ユーザへの静的および動的なウェブコンテンツ (.html、.css、.js、イメージファイルなど) の配信を高速化するウェブサービスで、エッジロケーションと呼ぶデータセンターの世界的ネットワークを経由してコンテンツを配信します。  
エッジロケーションには以下の種類があります。

# 用語

- **Viewer（ビューワー）** ：クライアント/Webブラウザ
- **Distribution（ディストリビューション）** ：コンテンツ配信の設定単位で、CloudFrontドメイン名、代替ドメイン名毎に作成
- **Origin（オリジン）** ：コンテンツ提供元のWebサーバ毎に作成
  - カスタムオリジン：Amazon VPCやオンプレミスのWebサーバ
  - S3オリジン：静的コンテンツを提供する S3 バケット
- **Behavior（ビヘイビア）** ：キャッシュの動作設定で、URLパスパターン毎に作成

# Distribution

- Distribution毎にOrigin、Behaviorの設定を行う
- HTTP/1.0、HTTP/1.1、HTTP/2、WebSocketに対応
  - HTTP/2 使用時はクライアントが TLS 1.2以降および SNI（Servier Name Identification）に対応している必要がある
- TLS 1.3 クライアントに対応しており、デフォルトで有効
- IPv6に対応
- `[ランダム文字列].cloudfront.net` がドメイン名として割り当てられる
- CNAME エイリアスを利用して代替ドメイン名を指定可能（Route53やACMと連携）
- AWS WAFと連携可能
- アクセスログを S3 に保存可能

# Origin

オリジンはカスタムオリジンとS3 オリジンを指定可能です。

- カスタムオリジン：クラウド、オンプレに関わらない任意のWebサーバ
- S3オリジン：S3バケット
- VPCオリジン：VPCのプライベートサブネットにホストされているWebサーバ

S3をオリジンとする場合、OAI（Origin Access Identity）とOAC（Origin Access Control）の 2 種類から選択可能ですが、 OAI はレガシーで OAC を使用することを推奨します。  
カスタムオリジンの指定の際は OAI や OAC は使用せず、オリジンの Web サーバのドメイン名を直接指定することになります。（ALB であれば ALB のドメイン名）  
また、VPCオリジンを使用することで、パブリックに公開されていない VPC プライベートサブネットからのコンテンツも配信可能になります。プライベートサブネットの Application Load Balancer (ALB)、Network Load Balancer (NLB)、EC2 インスタンスを VPC オリジンとして使用できるということです。

# Behavior

以下のような設定が可能です。

- キャッシュコントロールに関する設定
- 許可するプロトコル（HTTP/HTTPS）やリダイレクト、HTTPメソッドなどの指定
- Lambda@Edge関数に関する設定
- など

# おすすめ書籍

<!-- ad link - amazon/rakuten books - aws lambda -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"AWS Lambda実践ガイド 第2版 （impress top gear） [ 大澤 文孝 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/3303","p":["\/9784295013303_1_4.jpg","\/9784295013303_2.jpg","\/9784295013303_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16987486\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3J83xpr","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hM1BhW","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16987486\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"Dlcy4","s":"s"});
</script>
<div id="msmaflink-Dlcy4">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>