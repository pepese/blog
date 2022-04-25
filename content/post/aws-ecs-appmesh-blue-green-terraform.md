---
title:       "TerraformでECSのBlue/GreenデプロイをApp Meshでやってみる"
URL:         "aws-ecs-appmesh-blue-green-terraform"
subtitle:    ""
description: ""
keyword:     "AWS, ECS, App Mesh, Blue/Green, デプロイ, Terraform"
date:        2022-04-25
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- ecs
- "app mesh"
- terraform
categories:
- tech
---

Amazon ECSはAWS独自のコンテナ実行・管理サービス、AWS App MeshはAmazon EKSでも利用できるサービスメッシュのサービスです。  
この記事では、TerraformでECS+App Meshを構築し、Blue/Greenデプロイメントを実現する方法について記載します。

<!--more-->

なお、独自の方法なので、良い子は真似しないでください。  
利用する場合は、くれぐれも大人の方と一緒に用法・用量守って正しくお使いください。

# 概要

AWS App Mesh については以下のスライドを参照してください。

[20201014 AWS Black Belt Online Seminar AWS App Mesh Deep Dive](https://www.slideshare.net/AmazonWebServicesJapan/20201014-aws-black-belt-online-seminar-aws-app-mesh-deep-dive/27)

Blue/Green デプロイメントの実現方法は、 **Virtual Router** にてリクエストの振り分け先となる **Virtual Node** の重み付けや紐付けを変更することで実現します。  
Blue 用の Virtual Node と Green 用の Virtual Node を作成して Virtual Router の設定を変更することで行います。

# Terraform

ここでは Terraform のコードを交えて Blue/Green デプロイメントを行うための仕組みについて説明します。  
なお、全コードを載せるかなり多くなってしまいますので、ポイントとなる部分のみ載せること、あらかじめご容赦ください。

## Virtual Node

以下は Blue 用の Virtual Node を構築するコードです。

```terraform
#####################################
# App Mesh / Virtual Node
#####################################
resource "aws_appmesh_virtual_node" "sample_blue" {
  count     = local.sample_param["blue_is_active"] ? 1 : 0
  name      = "${local.sample_param["app_name"]}-blue"
  mesh_name = aws_appmesh_mesh.sample.id
  spec {
    listener {
      port_mapping {
        port     = local.sample_param["app_port"]
        protocol = "http"
      }
      health_check {
        protocol            = "http"
        path                = local.sample_param["healthcheck_path"]
        healthy_threshold   = 2
        unhealthy_threshold = 2
        timeout_millis      = 2000
        interval_millis     = 5000
      }
    }
    service_discovery {
      aws_cloud_map {
        attributes = {
          ECS_SERVICE_NAME = "${local.base_name}-sample-blue"
        }
        namespace_name = aws_service_discovery_private_dns_namespace.sample_private_dns.name
        service_name   = local.sample_param["app_name"]
      }
    }
    logging {
      access_log {
        file {
          path = "/dev/stdout"
        }
      }
    }
  }
}

#####################################
# ECS / Virtual Service
#####################################
resource "aws_ecs_service" "sample_blue" {
  count            = local.sample_param["blue_is_active"] ? 1 : 0
  name             = "${local.base_name}-sample-blue"
  cluster          = aws_ecs_cluster.sample.arn
  task_definition  = aws_ecs_task_definition.sample_blue[count.index].arn
  desired_count    = local.sample_param["desired_count"]
  launch_type      = "FARGATE"
  platform_version = local.sample_param["platform_version"]
  network_configuration {
    assign_public_ip = true
    security_groups  = [aws_security_group.sample.id]
    subnets = [
      aws_subnet.sample_protected_1a.id,
      aws_subnet.sample_protected_1c.id,
    ]
  }
  service_registries {
    registry_arn = aws_service_discovery_service.sample.arn
  }
  lifecycle {
    ignore_changes = [task_definition]
  }
}

#####################################
# ECS Task
#####################################
resource "aws_ecs_task_definition" "sample_blue" {
  count                    = local.sample_param["blue_is_active"] ? 1 : 0
  family                   = "${local.sample_param["app_name"]}-blue"
  cpu                      = local.sample_param["task_cpu"]
  memory                   = local.sample_param["task_memory"]
  network_mode             = "awsvpc"
  requires_compatibilities = ["FARGATE"]
  execution_role_arn       = aws_iam_role.sample_exec.arn
  task_role_arn            = aws_iam_role.sample.arn
  container_definitions    = data.template_file.sample_blue[count.index].rendered
  proxy_configuration {
    type           = "APPMESH"
    container_name = "envoy"
    properties = {
      AppPorts         = "${local.sample_param["app_port"]}"
      EgressIgnoredIPs = "169.254.170.2,169.254.169.254"
      IgnoredUID       = "1337"
      ProxyEgressPort  = 15001
      ProxyIngressPort = 15000
    }
  }
}

#####################################
# ECS Task Definition
#####################################
data "template_file" "sample_blue" {
  count    = local.sample_param["blue_is_active"] ? 1 : 0
  template = file("${path.module}/tpl/task-def-sample.tpl")
  vars = {
    // app container def
    name           = "${local.sample_param["app_name"]}-blue"
    image          = local.sample_param["app_image_blue"]
    memory         = local.sample_param["app_memory"]
    containerPort  = local.sample_param["app_port"]
    app_logs_group = aws_cloudwatch_log_group.sample.name
    // envoy container def
    envoy_image          = local.sample_param["envoy_image"]
    envoy_cpu            = local.sample_param["envoy_cpu"]
    envoy_memory_rsv     = local.sample_param["envoy_memory_rsv"]
    envoy_logs_group     = aws_cloudwatch_log_group.sample_envoy.name
    appmesh_resource_arn = "mesh/${aws_appmesh_mesh.sample.name}/virtualNode/${local.sample_param["app_name"]}-blue"
    // xray container def
    xray_image      = local.sample_param["xray_image"]
    xray_cpu        = local.sample_param["xray_cpu"]
    xray_memory_rsv = local.sample_param["xray_memory_rsv"]
    xray_logs_group = aws_cloudwatch_log_group.sample_xray.name
  }
}
```

`local.sample_param["blue_is_active"]` が `true` のときに作成されるようになっています。  
なお、上記のコードで文字列 `blue` を `green` に置換したものを Green 用の Virtual Node を構築するコードとして別途準備してください。

## Virtual Router

以下は Virtual Router を構築するコードです。

```terraform
#####################################
# App Mesh / Virtual Router
#####################################
resource "aws_appmesh_virtual_router" "sample" {
  name      = local.sample_param["app_name"]
  mesh_name = aws_appmesh_mesh.sample.id
  spec {
    listener {
      port_mapping {
        port     = local.sample_param["app_port"]
        protocol = "http"
      }
    }
  }
}

// route for blue and green
resource "aws_appmesh_route" "sample" {
  count               = local.sample_param["blue_is_active"] == "true" && local.sample_param["green_is_active"] == "true" ? 1 : 0
  name                = local.sample_param["app_name"]
  mesh_name           = aws_appmesh_mesh.sample.id
  virtual_router_name = aws_appmesh_virtual_router.sample.name
  spec {
    http_route {
      match {
        prefix = "/"
      }
      retry_policy {
        tcp_retry_events = [
          "connection-error",
        ]
        max_retries = 1
        per_retry_timeout {
          unit  = "s"
          value = 1
        }
      }
      action {
        weighted_target {
          virtual_node = aws_appmesh_virtual_node.sample_blue[count.index].name
          weight       = local.sample_param["blue_weight"]
        }
        weighted_target {
          virtual_node = aws_appmesh_virtual_node.sample_green[count.index].name
          weight       = local.sample_param["green_weight"]
        }
      }
    }
  }
}

// route for blue
resource "aws_appmesh_route" "sample_blue" {
  count               = local.sample_param["blue_is_active"] == "true" && local.sample_param["green_is_active"] == "false" ? 1 : 0
  name                = "${local.sample_param["app_name"]}-blue"
  mesh_name           = aws_appmesh_mesh.sample.id
  virtual_router_name = aws_appmesh_virtual_router.sample.name
  spec {
    http_route {
      match {
        prefix = "/"
      }
      retry_policy {
        tcp_retry_events = [
          "connection-error",
        ]
        max_retries = 1
        per_retry_timeout {
          unit  = "s"
          value = 1
        }
      }
      action {
        weighted_target {
          virtual_node = aws_appmesh_virtual_node.sample_blue[count.index].name
          weight       = local.sample_param["blue_weight"]
        }
      }
    }
  }
}

// route for green
resource "aws_appmesh_route" "sample_green" {
  count               = local.sample_param["blue_is_active"] == "false" && local.sample_param["green_is_active"] == "true" ? 1 : 0
  name                = "${local.sample_param["app_name"]}-green"
  mesh_name           = aws_appmesh_mesh.sample.id
  virtual_router_name = aws_appmesh_virtual_router.sample.name
  spec {
    http_route {
      match {
        prefix = "/"
      }
      retry_policy {
        tcp_retry_events = [
          "connection-error",
        ]
        max_retries = 1
        per_retry_timeout {
          unit  = "s"
          value = 1
        }
      }
      action {
        weighted_target {
          virtual_node = aws_appmesh_virtual_node.sample_green[count.index].name
          weight       = local.sample_param["green_weight"]
        }
      }
    }
  }
}
```

`// route for blue and green` とコメントを打っているのが Blue と Green 両方の Virtual Node へ、 `// route for blue` が Blue のみへ、 `// route for green` が Green のみへルーティングする設定です。  
ぞれぞれ `local.sample_param["blue_is_active"]` と `local.sample_param["green_is_active"]` が `true` か否かによって作成されるようになっており、これらのパラメータの値によってルーティング先を切り替える仕組みとなっています。  
また、 `local.sample_param["blue_weight"]` と `local.sample_param["green_weight"]` の値によってリクエストルーティングの流量を変更することができます。  
Blue/Green デプロイメントとは言っていますが、 この `weight` の値の割合をコントロールすることによって **カナリアリリース** も可能です。

## タスクのロール

以下は ECS タスクが利用するロールを構築するコードです。

```terraform
#####################################
# ECS Task Role
#####################################
resource "aws_iam_role" "sample" {
  name               = "${local.base_name}-sample"
  assume_role_policy = data.aws_iam_policy_document.sample_assume.json
}

data "aws_iam_policy_document" "sample_assume" {
  statement {
    actions = ["sts:AssumeRole"]
    principals {
      type        = "Service"
      identifiers = ["ecs-tasks.amazonaws.com"]
    }
  }
}

data "aws_iam_policy_document" "sample" {
  count = 1
  statement {
    actions = [
      "appmesh:StreamAggregatedResources",
    ]
    resources = local.sample_param["blue_is_active"] == true ? local.sample_param["green_is_active"] == true ? ["${aws_appmesh_virtual_node.sample_blue[count.index].arn}", "${aws_appmesh_virtual_node.sample_green[count.index].arn}"] : ["${aws_appmesh_virtual_node.sample_blue[count.index].arn}"] : ["${aws_appmesh_virtual_node.sample_green[count.index].arn}"]
  }
}

resource "aws_iam_role_policy" "sample" {
  count  = 1
  role   = aws_iam_role.sample.name
  policy = data.aws_iam_policy_document.sample[count.index].json
}

resource "aws_iam_role_policy_attachment" "sample_cloudwatch" {
  role       = aws_iam_role.sample.name
  policy_arn = "arn:aws:iam::aws:policy/CloudWatchFullAccess"
}

resource "aws_iam_role_policy_attachment" "sample_xray" {
  role       = aws_iam_role.sample.name
  policy_arn = "arn:aws:iam::aws:policy/AWSXRayDaemonWriteAccess"
}
```

`local.sample_param["blue_is_active"]` と `local.sample_param["green_is_active"]` が `true` か否かによって、 `appmesh:StreamAggregatedResources` アクションの対象リソースとなる Virtual Node が切り替わるようになっています。

## 変数

以下はこれまでの Terraform コードで利用する変数の定義です。

```terraform
locals {
  sample_param = {
    // service def
    desired_count    = 1
    platform_version = "1.4.0"
    // task def
    task_cpu    = 512  # 0.5 vCPU
    task_memory = 1024 # 1 GB
    // app container def
    app_name         = "sample"
    app_memory       = 256 # ハード制限（上限確保量）
    app_port         = "8080"
    healthcheck_path = "/health"
    // app container def blue
    blue_weight    = 1
    blue_is_active = true
    app_image_blue = "xxxxxxxxxxxx.dkr.ecr.ap-northeast-1.amazonaws.com/sample:0.0.1"
    // app container def green
    green_weight    = 0
    green_is_active = false
    app_image_green = "xxxxxxxxxxxx.dkr.ecr.ap-northeast-1.amazonaws.com/sample:0.0.2"
    // envoy container def
    envoy_image      = "840364872350.dkr.ecr.ap-northeast-1.amazonaws.com/aws-appmesh-envoy:v1.20.0.1-prod"
    envoy_cpu        = 32
    envoy_memory_rsv = 256 # ソフト制限（通常確保量）
    // xray container def
    xray_image      = "amazon/aws-xray-daemon"
    xray_cpu        = 32
    xray_memory_rsv = 256 # ソフト制限（通常確保量）
  }
}
```

ポイントは `blue_is_active` ・ `green_is_active` 、 `blue_weight` ・ `green_weight` 、 `app_image_blue` ・ `app_image_blue` です。  
次節で使い方について説明していきます。

# 使い方

前節にて Terraform のコードについて説明しました。  
本節では実際に Blue/Green デプロイメントを行う手順について説明します。

まず初期状態は各変数が以下の状態です。

```terraform
blue_is_active = true
blue_weight    = 1
app_image_blue = "xxxxxxxxxxxx.dkr.ecr.ap-northeast-1.amazonaws.com/sample:0.0.1" # v1 と呼びます

green_is_active = false
green_weight    = 0
app_image_green = "xxxxxxxxxxxx.dkr.ecr.ap-northeast-1.amazonaws.com/sample:0.0.2" # v2 と呼びます
```

Blue が v1 のアクティブ状態でルーティングも行われています。  
Green は v2 の非アクティブ状態でルーティングは行われていません。  
この状態から v2 の Green をデプロイして、最終的に v1 の Blue を停止させます。

1. `green_is_active = true` として `terraform apply`
2. しばらくすると v2 の Green が起動しますが、リクエストはルーティングされていません -> 正しく起動することを確認
3. `green_weight = 1` として `terraform apply`
4. しばらくすると v2 の Green にもリクエストがルーティングされます -> アプリケーションが正しく処理を行なっていることを確認
5. `blue_weight = 0` として `terraform apply`
6. しばらくすると v1 の Blue にリクエストがルーティングされなくなります -> Blue へのリクエストが無風になることを確認
7. `blue_is_active = false` として `terraform apply`
8. しばらくすると v1 の Blue が停止・削除されます

以上で Blue/Green デプロイメントが完了です。  
上記のように変数の値を変更して `terraform apply` することで切り戻しも簡単に行えます。  
次回は Blue を v3 として Blue/Green デプロイメントすることになるでしょう。

いかがでしたか？  
`blue` と `green` が Virtual Node 名に丸見えになってしまうのがダサくてイヤな感じですが、なかなか使えるのではないでしょうか。

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