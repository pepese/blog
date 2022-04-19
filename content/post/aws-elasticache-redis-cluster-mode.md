---
title:       "Amazon ElastiCache for Redisのクラスターモード"
URL:         "aws-elasticache-redis-cluster-mode"
subtitle:    ""
description: "Amazon ElastiCache for Redisのクラスターモードについて、個人的に理解に苦労しました。この記事では、Amazon ElastiCache for Redisのクラスターモードを説明しつつ Terraform での設定方法について紹介します。"
keyword:     "AWS, ElastiCache, Redis, クラスターモード, Terraform"
date:        2022-04-19
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

Amazon ElastiCache for Redisのクラスターモードについて、個人的に理解に苦労しました。  
この記事では、Amazon ElastiCache for Redisのクラスターモードを説明しつつ Terraform での設定方法について紹介します。

<!--more-->

Redis の入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/redis-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Redis入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Redisは永続化可能なインメモリデータベースで、オープンソースのBSDライセンスで公開されています。この記事ではRedisの概要から基本的な機能までをご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# 用語

用語を確認しながらクラスターモードの概要を押さえましょう。

- レプリケーショングループ（≒ 1 つのノードグループ）
  - Primary Node が 1 個、 Replica Node が 0 〜 5 個で構成されます
  - Replica Node が 1 個以上の場合、マスター・スレーブ型のレプリケーション機能が有効になります
  - 自動フェイルオーバー・マルチ AZ を有効にすることで、耐障害性を向上させて書き込みのダウンタイムを減少させることができます
- ノードグループ（＝ 1 つのレプリケーショングループ）
  - 1 つのシャードに対応する 1 つ以上のノードから構成されるグループです
- クラスターモード
  - ノードグループが 1 つの場合はクラスターモードが無効です
  - ノードグループが 2 つ以上の場合はクラスターモードが有効となり、ノードグループと同数のシャードで構成されます
  - アクセスした際、該当のデータを保持するシャードのノードグループに対してリダイレクトが行われます
  - 1 〜 500 ノードに収まる範囲でシャードを構成することができます
    - 例１）Primary Node が 1 個と Replica Node が 0 個で構成されるノードグループが 500 個で 500 シャード
    - 例２）Primary Node が 1 個と Replica Node が 5 個で構成されるノードグループが 83 個で 83 シャード（ (1+5)x83 = 498 ノード）

<img src="https://docs.aws.amazon.com/ja_jp/AmazonElastiCache/latest/red-ug/images/ElastiCacheClusters-CSN-RedisClusters.png">

# Terraform による設定

## クラスターモードが無効

クラスターモードが有効・無効に関わらず `aws_elasticache_replication_group` を利用します。

```terraform
resource "aws_elasticache_replication_group" "example" {
  replication_group_id        = "replication-group-1"
  description                 = "example description"
  automatic_failover_enabled  = true
  engine                      = "redis"
  engine_version              = "6.x"
  multi_az_enabled            = true
  node_type                   = "cache.m4.large"
  number_cache_clusters       = 2
  parameter_group_name        = "example-pg"
  subnet_group_name           = "example-sn"
  port                        = 6379
}
```

`number_cache_clusters` の設定により Primary/Replica Node の数を設定することができます。

- `number_cache_clusters = 1`
  - Primary Node が 1 個、 Replica Node が 0 個
- `number_cache_clusters = 2`
  - Primary Node が 1 個、 Replica Node が 1 個
- `number_cache_clusters = 6`
  - Primary Node が 1 個、 Replica Node が 5 個

`automatic_failover_enabled` は、 Primary Node が 1 個のみの場合は `false` （デフォルト）、Replica Node が 1 個以上で自動フェイルオーバーが必要な場合は `automatic_failover_enabled = true` および `multi_az_enabled = true` を設定してください。  
なお、クラスターモードを無効にする場合は `num_node_groups` および `replicas_per_node_group` は設定しないでください。

`number_cache_clusters` による設定以外にも、`aws_elasticache_cluster` リソースを利用することで Replica Node を追加することができます。

```terraform
resource "aws_elasticache_replication_group" "example" {
  replication_group_id = "replication-group-1"
  ...省略
}

resource "aws_elasticache_cluster" "replica" {
  count = 1
  cluster_id           = "replication-group-1-${count.index}"
  replication_group_id = aws_elasticache_replication_group.example.id
}
```

`aws_elasticache_cluster` を使えば Replica Node の詳細な設定が可能になりますが、何か事情が無い限りは `aws_elasticache_replication_group` のみで問題ありません。

## クラスターモードが有効

`aws_elasticache_replication_group` を利用します。

```terraform
resource "aws_elasticache_replication_group" "example_cluster_mode" {
  replication_group_id        = "redis-cluster"
  description                 = "cluster mode example description"
  automatic_failover_enabled  = true
  engine                      = "redis"
  engine_version              = "6.x"
  multi_az_enabled            = true
  node_type                   = "cache.m4.large"
  parameter_group_name        = "example-pg"
  subnet_group_name           = "example-sn"
  port                        = 6379

  num_node_groups         = 2
  replicas_per_node_group = 1
}

resource "aws_elasticache_parameter_group" "example_pg" {
  name   = "example-pg"
  family = "redis6.x"

  parameter {
    name  = "cluster-enabled"
    value = "yes"
  }
  ...省略
}

cluster-enabled = true
```

`num_node_groups` と `replicas_per_node_group` の設定により Primary/Replica Node の数を設定することができます。

- `num_node_groups = 2`
  - Primary Node が 1 個 と Replica Node が 0 個で構成されるノードグループが 2 個（シャードは 2 個）
- `num_node_groups = 2` および `replicas_per_node_group = 1`
  - Primary Node が 1 個 と Replica Node が 1 個で構成されるノードグループが 2 個（シャードは 2 個）
- `num_node_groups = 10` および `replicas_per_node_group = 5`
  - Primary Node が 1 個 と Replica Node が 5 個で構成されるノードグループが 10 個（シャードは 10 個）

なお、クラスターモードを有効にする場合は `number_cache_clusters` は設定しないでください。  
また、クラスターモードを有効にする場合は `aws_elasticache_parameter_group` で、`default.redis6.x.cluster.on` などのクラスターモードが有効なパラメータグループファミリを利用するか、 `cluster-enabled` を `yes` に設定する必要があります。

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