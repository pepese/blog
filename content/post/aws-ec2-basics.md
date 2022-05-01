---
title:       "Amazon EC2入門"
URL:         "aws-ec2-basics"
subtitle:    ""
description: "Amazon EC2 は、AWS が提供する最もシンプルな仮想サーバです。この記事では、 EC2 をざっくり理解できる概要を記載します。"
keyword:     "AWS, EC2, 仮想サーバ"
date:        2022-05-01
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- ec2
categories:
- tech
---

Amazon EC2 は、AWS が提供する最もシンプルな仮想サーバです。  
この記事では、 EC2 をざっくり理解できる概要を記載します。

<!--more-->

# 概要

[EC2](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/concepts.html) を作成する際は以下の設定を行います。

- AMI の選択
  - Amazon Machine Image 、インスタンス用に事前に設定されたテンプレート
- インスタンスタイプの選択
- インスタンスの設定
  - ネットワーク
  - プレイスメントグループ
- ストレージの設定
- タグの設定
- セキュリティグループの設定
- など

以降、設定に必要な知識について記載します。  
ただし、 本記事では VPC 関連については省略しますので適宜以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/aws-vpc-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.jpg" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Amazon VPC入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Amazon VPC は、 AWS が提供する仮想ネットワークサービスです。この記事では、Amazon VPC をざっくり理解できる概要と作成例について記載します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# AMI の選択

AMI は以下の項目から設定を選択します。

- アーキテクチャ
    - x86 / Arm
- ビット数
    - 32bit / 64bit
- 仮想化方式
    - 準仮想化（ Paravirtual : PV ）※古いので利用は推奨されません
    - 完全仮想化（ Hardware-assisted VM : HVM ）
- **ルートボリューム** （ブートストレージ / OSがインストールされるボリューム）
    - EBS Backed
        - ルートボリュームが EBS スナップショットから作成された EBS で起動が早いです
    - Instance Store-Backed
        - ルートボリュームがインスタンスストアで S3 に保存されたテンプレートから作成されるため起動が遅いです
        - ただし、起動後のデータアクセスが早いのでキャッシュ利用には向きます

推奨は、 64bit HVM EBS-Backed です。

[参考](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/AMIs.html)

## 物理ホストの専有

物理ホストの専有には 2 つの方法があり、以下のようになります。

- 2 つの方法
    - ハードウェア専有インスタンス（Dedicated Instance）[参考](https://aws.amazon.com/jp/ec2/pricing/dedicated-instances/)
    - Amazon EC2 Dedicated Host [参考](https://aws.amazon.com/jp/ec2/dedicated-hosts/)
- 共通の機能
    - ユーザ専有の物理サーバにインスタンスを起動可能（他ユーザのインスタンスは起動しない）
    - 従量課金やクラウドのメリットはそのまま確保
- Dedicated Hosts の特徴
    - 物理ホストへのインスタンス配置が制御・確認可能
    - 物理ホスト単位のソフトウェアライセンスを持ち込み（ BYOL ）可能
    - 物理ホスト単位で課金
    - 一部の OS が利用できないので注意

# インスタンスタイプの選択

EC2 **インスタンスタイプ** のネーミングポリシーは以下の通りです。

```
# フォーマット
[インスタンスファミリー][インスタンス世代][(追加機能)].[インスタンスサイズ]
# 例
c5d.xlarge
```

各桁の意味は以下の通りです。

- インスタンスファミリー（ 1 桁目）
- インスタンス世代（ 2 桁目）
- 追加機能（ 3 桁目）
- インスタンスサイズ（ドット以降）

以上のネーミングポリシーから適したインスタンスタイプを選択してください。

[インスタンスタイプ一覧](https://aws.amazon.com/jp/ec2/instance-types/)

# インスタンスの設定：ネットワーク

## IP の種類

IP には以下の種類があります。

- Privete IP
    - 必ず割り当てられる IP アドレス
    - EC2 作成時に設定可能
    - stop/start しても変化なく固定の IP
- Public IP
    - ランダムに割り当てられる Public IP
    - stop/start すると別の IP が割り当てられる
    - 付与しないこともできる
- Elastic IP (EIP)
    - 別インスタンスへ再マップも可能な固定の Public IP
    - stop/start しても変化なく固定の IP
    - 利用していなくても課金される

[参考](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-instance-addressing.html)

## ENI

ENI ( Elastic Network Interfaces ) は、 VPC 上で実現する仮想ネットワークインターフェース（ NIC ）です。  
以下を ENI に紐づけて管理します。

- Private IP
- Elatic IP
- MAC アドレス
- セキュリティグループ

インスタンス障害時に NIC 付け替えなどの運用が可能です。  
また、インスタンスによって割り当て可能な数が異なることに注意してください。  
[参考](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/using-eni.html)

## 拡張ネットワーキング

EC2 インスタンスの持つ通信性能を最大化する機能です。  
以下の 2 タイプあります。

- Intel 82599VF (ixgbevf)
- Elastic Network Adapter (ENA)

[参考](https://aws.amazon.com/jp/premiumsupport/knowledge-center/enable-configure-enhanced-networking/)

## ネットワーク帯域

EC2 間のネットワーク帯域には以下の制限があります。

- シングルフロー通信
    - 最大 5 Gbps または 10 Gbps （同一 Cluster Placement Group の場合）
- マルチフロー通信
    - 各インスタンスタイプが持つ通信帯域の最大

EC2 間で 5 Gbps 以降の帯域幅を実現するには、通信の多重化とマルチコアへの分散を意識する必要があります。（ RPS/RFS の設定など）

# インスタンスの設定：プレイスメントグループ

**プレイスメントグループ** は、EC2 インスタンスの物理的な配置戦略オプションです。[参考](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/placement-groups.html)  
以下のオプションがあります。

- Cluster
    - EC2 インスタンスを密な場所に配置し、ネットワークパフォーマンスを最適化
    - 単一 AZ に閉じます
- Spread
    - EC2 インスタンスを別々のハードウェアに分散して配置、物理サーバ障害時に複数のインスタンスが影響を受ける確率を低減
    - 同一 AZ にクラスタを展開している際などに有効
    - AZ 跨ぎで定義することが可能で、 1 AZ あたり実行中のインスタンスは最大 7
- partition

# ストレージの設定

EC2 のストレージには、ルートボリューム以外に以下を追加で設定することができます。

- インスタンスストア
    - EC2 インスタンスが起動するホストコンピュータに内臓されたディスク
    - EC2 インスタンスと分割不可能
    - stop/terminate するとクリアされる
    - インスタンスタイプにより利用に制限がある（そもそも利用できるか否か、個数、サイズ）
    - 追加費用無し
- Amazon Elastic Block Store (EBS)
    - ネットワークで接続
    - EC2 インスタンスとは独立して管理
    - stop/terminate してもクリアされない
    - Volume ごとに性能・容量を設定可能
    - 別途費用発生
    - Snapshot を取得して S3 に保存可能

## EBS 最適化オプション

通常のネットワークとは別に EBS 専用帯域を確保するオプションです。

- 起動時に有効/無効を洗濯可能
- 帯域はインスタンスサイズによって異なる
- インスタンスタイプによっていはデフォルトで有効

[参考](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/EBSOptimized.html)

# インスタンスの設定：その他

## Key Pair

[Key Pair](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/UserGuide/ec2-key-pairs.html) は、 EC2 インスタンス上の OS に対する安全な認証を提供します。  
Key Pair を作成すると秘密鍵をダウンロードでき、 EC2 起動時に公開鍵が EC2 に自動的に付与されます。  
EC2 ログイン時に Key Pair （秘密鍵と公開鍵のペア）が照合され、ログイン可能になります。

- 認証鍵は、ユーザ名・パスワードの認証よりも安全な認証方式
- AWS では公開鍵のみ保持し、起動時に公開鍵を EC2 にコピーします
- 秘密鍵は、ユーザにて適切に管理・保管する必要があります

# Amazon EC2 Auto Scaling

[Amazon EC2 Auto Scaling](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/what-is-amazon-ec2-auto-scaling.html) について記載します。  
Amazon EC2 Auto Scaling は [AWS Auto Scaling](https://docs.aws.amazon.com/ja_jp/autoscaling/plans/userguide/what-is-aws-auto-scaling.html) の一部で他には以下があります。

- Amazon EC2 スポットフリートリクエスト
    - スポットフリートリクエストからインスタンスを起動または削除します。または、料金や容量の問題で中断されたインスタンスを自動的に置き換えます
- Amazon ECS
    - 負荷の変化に応じて ECS サービスの必要数の増減を調整します
- Amazon DynamoDB
    - DynamoDB テーブルまたはグローバルセカンダリインデックスを有効にして、プロビジョニングされた読み取りおよび書き込みキャパシティを増減させ、スロットリングなしでトラフィックの増加を処理します
- Amazon Aurora
    - Aurora DB クラスターにプロビジョニングされた Aurora リードレプリカの数を動的に調整して、アクティブな接続やワークロードの変化を処理します

Amazon EC2 Auto Scaling には以下の設定が必要です。

- [起動テンプレート](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/LaunchTemplates.html)
    - スケール時に起動される EC2 インスタンスの AMI ID、インスタンスタイプ、キーペア、セキュリティグループ、ブロックデバイスマッピングなどを指定
- [Auto Scaling グループ](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/AutoScalingGroup.html)
    - EC2 インスタンスの最小数、最大数、希望する数などを設定
- [スケーリングのオプション](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/scaling_plan.html#scaling_typesof)
    - スケールする特定の条件を設定

また、以下に注意が必要です。

- [ヘルスチェック](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/healthcheck.html)
    - [Auto Scaling グループへの Elastic Load Balancing ヘルスチェックの追加](https://docs.aws.amazon.com/ja_jp/autoscaling/ec2/userguide/as-add-elb-healthcheck.html)

## 起動テンプレート

起動テンプレートは、 AMI の ID 、インスタンスタイプ、キーペア、セキュリティグループ、その他 EC2 インスタンスを起動するために使用するパラメータが含まれてバージョンによる世代管理を行うことができる機能です。  
同等の機能として「起動設定」というものがありますが、非推奨となっていますので起動テンプレートを使用してください。  
起動テンプレートはその名の通りテンプレートです。  
これを基に Auto Scaling 時の EC2 インスタンスを作成します。  
設定項目はこれまで記載した EC2 の設定項目とほぼ同じです。

## Auto Scaling グループ

起動テンプレートを基に Auto Scaling グループを作成します。

## スケーリングのオプション

Auto Scaling には以下のオプションがあります。

- 現在のインスタンスレベルの常時維持
- 手動でスケールする
- スケジュールに基づくスケーリング
- 需要に基づくスケーリング
- 予測スケーリングの使用

# おすすめ書籍

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