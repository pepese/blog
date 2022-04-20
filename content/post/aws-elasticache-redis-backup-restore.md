---
title:       "Amazon ElastiCache for Redisのバックアップ・リストア"
URL:         "aws-elasticache-redis-backup-restore"
subtitle:    ""
description: "Amazon ElastiCache for RedisはAWSが提供するRedis互換の高性能なインメモリデータストアサービスです。この記事では、Amazon ElastiCache for Redisのバックアップ・リストアの方法を Terraform の設定を交えてご紹介します。"
keyword:     "AWS, ElastiCache, Redis, バックアップ, リストア, 復元, Terraform"
date:        2022-04-20
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- elasticache
- redis
- terraform
categories:
- tech
---

Amazon ElastiCache for RedisはAWSが提供するRedis互換の高性能なインメモリデータストアサービスです。  
この記事では、Amazon ElastiCache for Redisのバックアップ・リストアの方法を Terraform の設定を交えてご紹介します。

<!--more-->

# バックアップ

Terraform にて Amazon ElastiCache for Redis を構築する例を交えてバックアップ方法について記載します。  
クラスターモード無効のレプリケーショングループで作成します。  
以下は Terraform の例です。

```terraform
resource "aws_elasticache_replication_group" "sample" {
  replication_group_id       = "${var.base_name}-sample"
  description                = "Redis cluster for ${var.base_name}-sample."
  automatic_failover_enabled = var.sample_elasticache_param["automatic_failover_enabled"]
  engine                     = "redis"
  engine_version             = var.sample_elasticache_param["engine_version"]
  multi_az_enabled           = var.sample_elasticache_param["multi_az_enabled"]
  node_type                  = var.sample_elasticache_param["node_type"]
  num_cache_clusters         = var.sample_elasticache_param["number_cache_clusters"]
  parameter_group_name       = aws_elasticache_parameter_group.sample.id
  subnet_group_name          = aws_elasticache_subnet_group.sample.name
  security_group_ids         = [aws_security_group.sample_elasticache.id]
  port                       = 6379
  maintenance_window         = var.sample_elasticache_param["maintenance_window"]
  snapshot_window            = var.sample_elasticache_param["snapshot_window"]
  snapshot_retention_limit   = 35
  final_snapshot_identifier  = "${var.base_name}-sample-final-snapshot"
  apply_immediately          = false
  auto_minor_version_upgrade = true
  at_rest_encryption_enabled = true
  transit_encryption_enabled = true
  kms_key_id                 = aws_kms_key.sample_elasticache.arn
  auth_token                 = var.sample_elasticache_param["auth_token"]
  lifecycle {
    ignore_changes = [
      number_cache_clusters,
      apply_immediately
    ]
  }

  tags = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${var.base_name}-sample" }))
}
```

## 定期的にスナップショットを取得する

`aws_elasticache_replication_group` リソースに対して以下の設定を行なってください。

- `snapshot_retention_limit`
  - バックアップの保存期間です（単位：日）
  - このパラメータが 0 設定されている場合、日次自動バックアップがオフになります
- `snapshot_window`
  - 自動バックアップが実行される日々の時間範囲です
  - `snapshot_retention_limit` の設定が有効であることが前提となります
  - UTC で `15:00-16:00` のような形式で設定します

以上の設定で日次バックアップが取得されます。（ [ElastiCache コンソール](https://console.aws.amazon.com/elasticache/) -> ナビゲーション「バックアップ」にあります。「フィルタ」に注意してください。）  
ただし、 `terraform destroy` などにより ElastiCache を削除してしまった場合、 **スナップショットも削除されます** のでご注意ください。

## 削除時にスナップショットを取得する

前節で紹介したスナップショットの取得では、 **ElastiCache を削除してしまった場合スナップショットも削除されます** 。  
ここでは、 ElastiCache を削除時に自動バックアップが走る設定をご紹介します。  
`aws_elasticache_replication_group` リソースに対して以下の設定を行なってください。

- `final_snapshot_identifier`
  - ElastiCache が削除されたときに作成されるスナップショットの名前です
  - この値が有効であることで最終的なスナップショットが作成されます

## 手動でスナップショットを取得する

マネージメントコンソールにて以下の手順でスナップショットを取得します。（ [参考](https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/backups-manual.html) ）

1. AWS Management Console にサインインして、[ElastiCache コンソール](https://console.aws.amazon.com/elasticache/) を開きます
2. ナビゲーションペインで、[Redis] を選択すると、[Redis clusters] 画面が表示されます
3. バックアップする Redis クラスターの名前の左にあるチェックボックスをオンにします
4. [Backup] を選択します
5. [Create Backup] ダイアログで、[Backup Name] ボックスにバックアップの名前を入力します
6. [Create Backup] を選択します
7. クラスターのステータスが snapshotting に変わり、ステータスが [available] に戻ると、バックアップの作成が完了です

# リストア

**ElastiCache は稼働中のサービスにスナップショットをリストアすることはできません** 。  
リストアする際は再構築することが前提になります。

## Terraformでリストア

Terraform での設定例は以下のようになります。

```terraform
resource "aws_elasticache_replication_group" "sample" {
  ...省略
  final_snapshot_identifier  = "${var.base_name}-sample-final-snapshot"
  snapshot_name              = "${var.base_name}-sample-final-snapshot"

  lifecycle {
    ignore_changes = [
      ...省略
      "final_snapshot_identifier",
      "snapshot_name",
    ]
  }
  ...省略
}
```

`aws_elasticache_replication_group` リソースに対して以下の設定を行なってください。

- `snapshot_name`
  - スナップショットから ElastiCache を作成する際にスナップショットの名前を設定します
- `lifecycle` -> `ignore_changes`
  - `final_snapshot_identifier` と `snapshot_name` を設定します
  - `terraform apply` で再構築完了後に `snapshot_name` を削除しましょう

## 手動でリストア

マネージメントコンソールにて以下の手順でリストア・復元を行います。（ [参考](https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/backups-restoring.html) ）

1. AWS Management Console にサインインして、 [ElastiCache コンソール](https://console.aws.amazon.com/elasticache/) を開きます
2. ナビゲーションペインで、[Backups] を選択します
3. バックアップのリストで、復元元のバックアップ名の左にあるチェックボックスをオンにします
4. [復元] を選択します
5. [Restore Cluster] ダイアログボックス、および、すべての [Required] フィールドとデフォルト値から変更するその他のフィールドに入力します
6. すべての設定が正しいことを確認したら、[作成] を選択します

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