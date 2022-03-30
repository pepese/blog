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

<!-- amazon affiliate kindle google cloud basics --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B09DP1BR35&linkId=cfdf74ae820a1204fd3aae3a2d07d272"></iframe>

<!-- amazon affiliate kindle aws basics --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08QTQBJKZ&linkId=b2aea688fd22ddb6076a65af2f9c3738"></iframe>