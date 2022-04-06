---
title:       "Google Cloud と AWS の比較"
URL:         "googlecloud-aws-comparison"
subtitle:    ""
description: "Google Cloud と AWS それぞれの特徴と比較について整理する。"
keyword:     "Google Cloud, GCP, AWS, 比較"
date:        2022-03-29
author:      "ぺーぺーSE"
image:       ""
tags:
- "google cloud"
- aws
categories:
- tech
---

Google Cloud と AWS それぞれの特徴と比較について記載する。

<!--more-->

# Google Cloud と AWS の特徴

## Google Cloud の特徴

- Google 製サービスとの連携が容易
  - 例えば、 Google Analytics のデータを BigQuery にエキスポート出来たりする
- グローバルなサービス展開に強い
  - 例えば、VPC などのネットワーク系サービスが全リージョン横断で構築出来たりする
- OSS ベースのマネージドサービスが豊富
  - 代表的なところでいうと Kubernetes の GKE 、Apache Beam の Cloud Dataflow 、 Apache Spark/Hadoop の Cloud Dataproc など
- ビッグデータ分析・AI系のサービスが強い

## AWS の特徴

- とにかくサービスの種類が多く、エンハンスも早い
  - スクラッチの作り込みを避けたい場合、使えるものが多い
- パートナー制度（ APN ）により多様なサードパーティ製品と連携可能
- クラウドエンジニアが多い
  - パブリッククラウドシェア 1 位で、コミュニティも多い
- 準拠法・裁判所は 日本法・東京地方裁判所を指定可能
  - Google Cloud はいずれも米国

# Google Cloud と AWS の比較

Google Cloud 視点で AWS と比較すると以下のようなものが挙げられる。（網羅性は無いので注意）

- IAM
    - Cloud IAMはGoogleアカウントに対してプロジェクト毎にロールを割り当てる。（AWSのように管理者が独自のIAMアカウントを作って管理するわけではない）
    - 階層になっているIAMだが、階層の下に対してポリシーを継承できるものの下でより強い権限にてポリシーを上書きすることができる。
- プロジェクトの概念
    - AWSでは契約ごとに払い出されるアカウント払い出されるが、GCPではプロジェクトという単位になっており、いくつでも自由に作れる。
    - プロジェクト単位で課金やIAMを分けられる。
- ネットワーク
    - GCPではVPCを作ると初めからマルチリージョンで作られる / ゾーン(AZ相当)を跨ったサブネットが作れる。
    - 外部IPアドレス（EIP相当）は、テンポラリーIPを昇格させることができる。また、グローバルに負荷分散できるIPも取得できる。
- データベース
    - CloudSQL（RDS相当）にOracleはない。（Bare Metal Solution にある） 
- API
    - 各サービスを操作するAPIがあるのだが初期で有効になっているものはほとんどなく、ユーザが意図的に有効化しないとつかえない。
- ストレージ
    - AWSのGlacierはデータを読み出す際に数時間かかるが、GCPのクラウドストレージのオプションであるColdLineやNearLineは同じようなサービスでありながら、通常のストレージと同じ速度で読み出しができる。
- LoadBalancer
    - グローバル向けに HTTPロードバランサとSSLロードバランサとTCPロードバランサ、リージョン向けにネットワークロードバランサ（TCP/UDP/SSL) がある。
    - 内部向けにはインターナルロードバランサがある。（HTTP(S)/TCP/UDP）
    - グローバル向けの方はリバースプロキシサーバで、リージョン向け/内部向けはロードバランサ。
    - GCPでグローバルIPを取得し、LBを前段におくとマルチリージョンでの負荷分散が行われる。
- 監視
    - オペレーションスイート（旧Stackdriver）の有料のプレミアム階層では、AWSのリソースも同じダッシュボード上で監視ができる。
    - オペレーションスイート（旧Stackdriver）アカウントに複数のGCPアカウントやAWSアカウントを紐付けることができる。
    - リソースを個々のインスタンスではなく、「Webサーバグループ」などとまとめて監視することもできる。

# Google Cloud と AWS のシェア

[Synergy Research の 2022 年 2 月 3 日の発表](https://www.srgresearch.com/articles/as-quarterly-cloud-spending-jumps-to-over-50b-microsoft-looms-larger-in-amazons-rear-mirror) によると、AWS がシェア 33％ で 1 位、Google Cloud で 9％ で 3 位という結果だった。（ちなみに 2 位は Azure）

# おすすめ書籍

<!-- ad link - amazon/rakuten books - google cloud basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"図解即戦力　Google Cloudのしくみと技術がこれ1冊でしっかりわかる教科書 [ 株式会社grasys ／ Google Cloud 西岡典生、田丸司 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3017\/9784297123017.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37iaebm","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMkNFF","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"ju6Y9","s":"s"});
</script>
<div id="msmaflink-ju6Y9">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

<!-- ad link - amazon/rakuten books - aws basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"AWSではじめるインフラ構築入門 安全で堅牢な本番環境のつくり方 [ 中垣 健志 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3437\/9784798163437.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3j7fBNl","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/h6WEYh","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"e0IUt","s":"s"});
</script>
<div id="msmaflink-e0IUt">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>