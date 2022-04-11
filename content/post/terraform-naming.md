---
title:       "Terraformの命名規則"
URL:         "terraform-naming"
subtitle:    ""
description: "Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、筆者が利用しているTerraformの命名規則をご紹介します。"
keyword:     "Terraform, 命名, 命名規則"
date:        2022-04-11
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
categories:
- tech
---

Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。  
この記事では、筆者が利用しているTerraformの命名規則をご紹介します。

<!--more-->

Terraform の入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/terraform-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Terraform入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、Terraformのインストールから使い方までをご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# 基本

|対象|命名方法|
|:---|:---|
|ファイル名|ケバブケース|
|ディレクトリ名|ケバブケース|
|リソース ID （ `resource` や `data` ）|スネークケース|
|リソース名 （ `name` 属性や `Name` タグ）|ケバブケース|
|変数名|スネークケース|

# リソースタグ

`tags` 属性のある `resource` には以下のタグを必ずつける。

- `System` ：システムを識別可能な文字列
- `Service`  : システム内のコンポーネント・マイクロサービスなどを識別するサービス名
- `Env` ：環境名（dev, tst, stg, prd）
- `Terraform` ："true"、 Terraform で作成した目印
- `Name` ：命名規則で定めたリソース名称
  - ケバブケース で記載し、『[ `System` ]-[ `Env` ]-[ `Service` ]-[ `リソースに合わせた任意名称` ]』の形式
  - `resource` の `name` 属性は、 `Name tag` で名称の名称と矛盾がないように命名
    - なお `aws_db_instance` のように `identifier` を使用しなければならないケースは注意
  - [ `リソースに合わせた任意名称` ] は リソースの付随関係により `-` で繋げて多段に命名

# リソース ID

- 命名は『[ `Service` ]_[ `リソースに合わせた任意名称` ]』
- 特定のコンポーネント・マイクロサービスに所属しない `System` で唯一のリソースの場合、 [ `Service` ] は `common` などを利用
- `Service` で唯一のリソースの場合、 [ `リソースに合わせた任意名称` ] は省略可
- [ `リソースに合わせた任意名称` ] はリソースの付随関係により `_` で繋げて多段にする
- 例
  - EC2 : `[Service]_web`
  - EC2 : `[Service]_batch`
  - RDS : `[Service]_rds`
  - Security Group : `[Service]_web_sg`
  - Security Group : `[Service]_rds_sg`

# おすすめ書籍

<!-- ad link - amazon/rakuten books - terraform aws -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"【POD】実践Terraform　AWSにおけるシステム設計とベストプラクティス AWSにおけるシステム設計とベストプラクティス （技術の泉シリーズ（NextPublishing）） [ 野村 友規 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/8136\/9784844378136.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16058180\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37mOivB","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hawlg8","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16058180\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"kSp0Z","s":"s"});
</script>
<div id="msmaflink-kSp0Z">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>