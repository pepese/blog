---
title:       "Amazon Auroraのバックアップ・リストア"
URL:         "aws-aurora-backup-restore"
subtitle:    ""
description: "Amazon AuroraはAWSが提供するMySQL・PostgreSQL互換の高性能なリレーショナルデータベースサービスです。いかに高可用とはいえ、バックアップ・リストア（復元）の方法を確立しておかなければ不安でしょうがありません。この記事では、Amazon Auroraのバックアップ・リストアの方法を Terraform の設定を交えてご紹介します。"
keyword:     "AWS, Aurora, バックアップ, リストア, 復元, Terraform"
date:        2022-04-20
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- aurora
- terraform
categories:
- tech
---

Amazon AuroraはAWSが提供するMySQL・PostgreSQL互換の高性能なリレーショナルデータベースサービスです。  
いかに高可用とはいえ、バックアップ・リストア（復元）の方法を確立しておかなければ不安でしょうがありません。  
この記事では、Amazon Auroraのバックアップ・リストアの方法を Terraform の設定を交えてご紹介します。

<!--more-->

# バックアップ

Terraform にて Amazon Aurora PostgreSQL を構築する例を交えてバックアップ方法について記載します。  
以下は Terraform の例です。

```terraform
// auroraクラスタを構築するための定義
resource "aws_rds_cluster" "sample" {
  cluster_identifier              = "${var.base_name}-sample"
  engine                          = var.sample_rds_param["engine"]
  engine_version                  = var.sample_rds_param["engine_version"]
  master_username                 = var.sample_rds_param["username"]
  master_password                 = var.sample_rds_param["password"]
  database_name                   = var.sample_rds_param["db_name"]
  port                            = var.sample_rds_param["port"]
  vpc_security_group_ids          = [aws_security_group.sample_rds.id]
  db_subnet_group_name            = aws_db_subnet_group.sample.name
  db_cluster_parameter_group_name = aws_rds_cluster_parameter_group.sample.name

  apply_immediately            = false
  deletion_protection          = true
  preferred_maintenance_window = var.sample_rds_param["maintenance_window"]
  preferred_backup_window      = var.sample_rds_param["backup_window"]
  backup_retention_period      = var.sample_rds_param["backup_retention_period"]
  copy_tags_to_snapshot        = true
  storage_encrypted            = true
  kms_key_id                   = aws_kms_key.sample_rds.arn
  skip_final_snapshot          = false
  final_snapshot_identifier    = "${var.base_name}-sample-final-snapshot"

  enabled_cloudwatch_logs_exports = [ // 「/aws/rds/cluster/${var.base_name}-sample/postgresql」ロググループ
    "postgresql",
  ]

  lifecycle {
    ignore_changes = [
      master_password,
      availability_zones
    ]
  }

  tags = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${var.base_name}-sample" }))
}

// クラスタインスタンスを構築するための定義
resource "aws_rds_cluster_instance" "sample" {
  count                           = 2
  cluster_identifier              = aws_rds_cluster.sample.id
  identifier                      = "${var.base_name}-sample-${count.index}"
  engine                          = aws_rds_cluster.sample.engine
  engine_version                  = aws_rds_cluster.sample.engine_version
  instance_class                  = var.sample_rds_param["instance_class"]
  monitoring_interval             = 60 #拡張モニタリング
  monitoring_role_arn             = aws_iam_role.sample_rds_monitoring.arn
  db_subnet_group_name            = aws_db_subnet_group.sample.name
  db_parameter_group_name         = aws_db_parameter_group.sample.name
  apply_immediately               = false
  performance_insights_enabled    = true
  performance_insights_kms_key_id = aws_kms_key.sample_rds_pi.arn
  tags                            = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${var.base_name}-sample-${count.index}" }))
}
```

## 定期的にスナップショットを取得する

`aws_rds_cluster` リソースに対して以下の設定を行なってください。

- `backup_retention_period`
  - バックアップの保存期間です（単位：日）
  - このパラメータが設定されていることにより、日次自動バックアップが有効になります
  - デフォルトは 1 ですので、好みの日数に変更してください
- `preferred_backup_window`
  - 自動バックアップが実行される日々の時間範囲です
  - `backup_retention_period` の設定が有効であることが前提となります
  - UTC で `15:00-16:00` のような形式で設定します
- `copy_tags_to_snapshot` （任意）
  - バックアップで作成されたスナップショットに Aurora のタグをコピーするか否かの設定です（ true or false ）

以上の設定で日次バックアップが取得されます。（ [Amazon RDS コンソール](https://console.aws.amazon.com/rds/) -> ナビゲーション「スナップショット」 -> 「システム」タブ、にあります）  
ただし、 `terraform destroy` などにより Aurora を削除してしまった場合、 **スナップショットも削除されます** のでご注意ください。

## 削除時にスナップショットを取得する

前節で紹介したスナップショットの取得では、 **Aurora を削除してしまった場合スナップショットも削除されます** 。  
ここでは、 Aurora を削除時に自動バックアップが走る設定をご紹介します。  
`aws_rds_cluster` リソースに対して以下の設定を行なってください。

- `final_snapshot_identifier`
  - Aurora が削除されたときに作成されるスナップショットの名前です
  - この値が有効であることと `skip_final_snapshot = false` になることで最終的なスナップショットが作成されます
- `skip_final_snapshot`
  - Aurora が削除される前に最終的なDBスナップショットを作成するかどうかの設定です
  - true を指定するとスナップショットは作成されません（デフォルト：false）
  - `final_snapshot_identifier` の設定値でスナップショット名が決定します

## 手動でスナップショットを取得する

マネージメントコンソールにて以下の手順でスナップショットを取得します。（ [参考](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/USER_CreateSnapshotCluster.html) ）

1. AWS Management Console にサインインし、[Amazon RDS コンソール](https://console.aws.amazon.com/rds/) を開きます
2. ナビゲーションで、[データベース] を選択します
3. DB インスタンスのリストで、DB クラスターの書き込みインスタンスを選択します
4. [アクション]、[スナップショットの取得] の順に選択すると、[DB スナップショットの取得] ウィンドウが表示されます
5. [スナップショット名] ボックスに DB クラスタースナップショットの名前を入力します。
6. [Take Snapshot] (スナップショットの取得) を選択します

# リストア

## 再構築時にリストア

うっかりなどで Aurora を削除してしまった際のリストア方法をご紹介します。  
うっかり防止のために `deletion_protection = true` を設定しておくのもよいでしょう。  
なお、この方法は **Terraform で Aurora を再作成・再構築する際に有効な方法** で、既に構築済みの Aurora には適用できませんのでご注意ください。

```terraform
// auroraクラスタを構築するための定義
resource "aws_rds_cluster" "sample" {
  ...省略
  final_snapshot_identifier = "${var.base_name}-sample-final-snapshot"
  snapshot_identifier       = "${data.aws_db_cluster_snapshot.final_snapshot.id}"

  lifecycle {
    ignore_changes = [
      ...省略
      "final_snapshot_identifier",
      "snapshot_identifier",
    ]
  }
  ...省略
}

// クラスタインスタンスを構築するための定義
resource "aws_rds_cluster_instance" "sample" {
  ...省略
}

// snapshotを参照するためのdata定義
data "aws_db_cluster_snapshot" "final_snapshot" {
  db_cluster_snapshot_identifier = "${var.base_name}-sample-final-snapshot"
  most_recent                    = true
}
```

`aws_rds_cluster` リソースに対して以下の設定を行なってください。

- `snapshot_identifier`
  - スナップショットから Aurora クラスタを作成する際にスナップショットの ARN を設定します
  - `aws_db_cluster_snapshot` データリソースを参照するのがよいでしょう
- `lifecycle` -> `ignore_changes`
  - `final_snapshot_identifier` と `snapshot_identifier` を設定します
  - `terraform apply` で再構築完了後に `snapshot_identifier` を削除しましょう

## 稼働中クラスタへの手動リストア

マネージメントコンソールにて以下の手順でリストア・復元を行います。（ [参考](https://docs.aws.amazon.com/ja_jp/AmazonRDS/latest/AuroraUserGuide/USER_RestoreFromSnapshot.html) ）

1. AWS Management Console にサインインし、[Amazon RDS コンソール](https://console.aws.amazon.com/rds/) を開きます
2. ナビゲーションで、[スナップショット] を選択します
3. 復元の元にするスナップショットを選択します（「手動」「システム」などのタブがあるので適切なものを選択します）
4. [Actions (アクション)]、[Restore snapshot (スナップショットの復元)] の順に選択します
5. [スナップショットの復元] ページで、[DB インスタンス識別子] に対象の Aurora クラスタの名前を入力します
6. [DB インスタンスの復元] を選択します

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