---
title:       "Amazon ElastiCache for Redisの認証トークンの設定・変更"
URL:         "aws-elasticache-redis-auth-token"
subtitle:    ""
description: "Amazon ElastiCache for RedisはAWSが提供するRedis互換の高性能なインメモリデータストアサービスです。この記事では、Amazon ElastiCache for Redisの認証トークンの設定・変更方法を Terraform の設定を交えてご紹介します。"
keyword:     "AWS, ElastiCache, Redis, 認証, トークン, Terraform"
date:        2022-06-15
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
この記事では、Amazon ElastiCache for Redisの認証トークンの設定・変更方法を Terraform の設定を交えてご紹介します。

<!--more-->

# Terraform

Terraform にて Amazon ElastiCache for Redis を構築する例を交えて認証トークン方法について記載します。  
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
  log_delivery_configuration {
    destination      = aws_cloudwatch_log_group.sample_elasticache_slowlog.name
    destination_type = "cloudwatch-logs"
    log_format       = "json"
    log_type         = "slow-log"
  }

  tags = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${var.base_name}-sample" }))
}
```

`transit_encryption_enabled = true` （転送中の暗号化を有効）にして `auth_token` に認証トークンを設定しておくのがポイントです。

# 認証トークンの変更・設定

認証トークンの変更は、ROTATE と SET の 2 つの戦略をサポートしています。  
ROTATE 戦略では、以前のトークンを保持しながら、サーバーに別の AUTH トークンを追加します。  
SET 戦略では、1 つの AUTH トークンのみをサポートするように、サーバーを更新します。

```bash
$ aws elasticache modify-replication-group \
  --replication-group-id replication-group-sample \
  --auth-token new-token \
  --auth-token-update-strategy ROTATE \
  --apply-immediately
```

上記の ROTATE 戦略コマンドで新規認証トークン `new-token` が追加されます。  
認証トークンは最大2つまで保持されるため、この状態ではまだ以前の認証トークンで認証可能な状態です。  
以下の SET 戦略コマンドで `new-token` でのみ認証可能な状態とします。

```bash
$ aws elasticache modify-replication-group \
  --replication-group-id replication-group-sample \
  --auth-token new-token \
  --auth-token-update-strategy SET \
  --apply-immediately
```

なお原因は不明ですが、筆者が Terraform にて Amazon ElastiCache for Redis を構築した段階では認証トークンによる認証が必須ではなく Redis に接続・操作可能でした。  
本節でご紹介した認証トークンの変更・設定を実施したところ、 Redis への接続・操作にトークンによる認証が必須となりました。

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