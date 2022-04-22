---
title:       "Terraform Modulesで環境を分割・切り替える"
URL:         "terraform-modules-env"
subtitle:    ""
description: "Terraform Modulesはソースコードをモジュール化することでテンプレートとして使うことができるようになる機能です。この記事では、Terraform Modulesを利用した本番・検証などの環境分割・切り替えの方法についてご紹介します。"
keyword:     "Terraform, Modules, Module, モジュール, 環境, 分割, 切り替え"
date:        2022-04-22
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
categories:
- tech
---

Terraform Modulesはソースコードをモジュール化することでテンプレートとして使うことができるようになる機能です。  
この記事では、Terraform Modulesを利用した本番・検証などの環境分割・切り替えの方法についてご紹介します。

<!--more-->

Terraform の入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/terraform-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Terraform入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、Terraformのインストールから使い方までをご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# Terraform Modulesとは

Terraform Modules は、ディレクトリなどにまとめた Terraform のコードをテンプレート・モジュールとして呼び出して利用することができる機能です。  
以下のように呼び出します。

```terraform
module "リソースの名前" {
    source = "モジュールを定義したフォルダのパス"
    パラメーター名 = "パラメーター値"
}
```

また、自身で作成した Module ではなく [Terraform Registry](https://registry.terraform.io/) で提供されているモジュールは以下のように呼び出すことができます。  
[AWS VPC Terraform module](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest) の例です。

```terraform
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  // 以降、変数へ値を代入する
}
```

本記事では Terraform Modules を利用した環境分割方法について記載しますが、他にも [Workspaces](https://www.terraform.io/language/state/workspaces) を利用した方法もあります。

# 命名規則

この記事でご紹介する例は下記の記事の命名規則に従っています。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/terraform-naming" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Terraformの命名規則 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、筆者が利用しているTerraformの命名規則をご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# 環境分割方法

あくまで筆者独自の方法なので参考までにお願いします。  
ディレクトリ構成は以下の通りです。

```bash
.
├── env         # 環境差分用ディレクトリ
│   ├── prd       # prd 環境用ディレクトリ
│   │   └── main.tf # 環境差分に応じたTerraformバージョン、Provider、Backend、変数、Module、Outputsの設定
│   ├── stg       # stg 環境用ディレクトリ
│   │   └── main.tf
│
└── modules     # 各環境共通で利用する resources をモジュール化
    ├── sample-vpc.tf  # resource（sampleサービスのVPC） の設定を記述
    ├── ...            # resource（その他） の設定を記述
    ├── variables.tf   # Module 内で利用する変数定義
    └── outputs.tf     # outputし、他のリソースから tfstate ファイル経由で参照されるデータを記載
```

使い方は対応する環境をカレントディレクトリとして、 `terraform` コマンドを実行するだけです。

```bash
# プロジェクトの初期化
$ cd env/[環境]
$ terraform init

# ファイルフォーマット
$ cd env/[環境]
$ terraform fmt ../../modules
$ terraform fmt

# 個別のリソースのterraform 初期化
$ cd env/[環境]
$ terraform init

# 個別のリソースの設定確認
$ cd env/[環境]
$ terraform validate

# 個別のリソースの差分確認
$ cd env/[環境]
$ terraform plan

# 個別のリソースの適用
$ cd env/[環境]
$ terraform apply

# 個別のリソースの削除
$ cd env/[環境]
$ terraform destroy
```

以降で個々のファイルについて説明していきます。  
`aws_vpc` を 1 つ構築する簡易な例になっています。

## `env/[環境]/main.tf`

`modules` の呼び出し元です。  
`../../modules` でモジュールを呼び出して `modules/variables.tf` に定義した変数に値を代入します。  
これによって環境差分を実現できます。  
`modules` の他にも必要に応じて変数を定義してください。

また、 `output` でリソースの設定結果を出力する対象について記載します。  
`modules/outputs.tf` に記載した `modules` の `output` を実際に出力します。

```terraform:main.tf
#####################################
# Terraform Settings
#####################################
terraform {
  required_version = "~> 1.1.0" // Terraform のバージョン
  required_providers {          // Provider の設定
    aws = {
      source  = "hashicorp/aws"
      version = "~> 3.0"        // AWS Provider のバージョン
    }
  }
  backend "s3" {                 // この設定で State ファイルが S3 に保存されます
    bucket = "your-bucket-name"  // State ファイルを配置するバケット
    key    = "terraform.tfstate" // State ファイルを配置するパス・ファイル名
    region = "ap-northeast-1"    // S3のリージョン
  }
}

#####################################
# Provider Settings
#####################################
provider "aws" {
  region  = local.region
  profile = var.profile
  default_tags { // AWS リソースへのデフォルトタグの設定
    tags = {
      System    = local.system
      Env       = local.env
      Terraform = "true"
    }
  }
}

#####################################
# Variables
#####################################
variable "profile" {}
locals {
  region = "ap-northeast-1"
  system = "system"
  env    = "prd"
}

#####################################
# Module
#####################################
module "modules" {
  source = "../../modules"

  #####################################
  # Common Variables
  #####################################
  system    = local.system
  region    = local.region
  env       = local.env
  base_name = "${local.system}-${local.env}"

  #####################################
  # VPC Variables
  #####################################
  vpc_cidr_block = "10.0.0.0/16"
}

#####################################
# Outputs
#####################################
output "sample_vpc" {
  value = module.modules.sample_vpc
}
```

Provider の設定では `default_tags` を変数で代入する形で設定しておくと非常に便利です。

## `modules/variables.tf`

`modules` 内で利用される変数を管理するファイルです。  
`env/[環境名]/main.tf` にて変数に値を代入します。  
これによって環境差分を実現できます。

```terraform:variables.tf
#####################################
# Common Variables
#####################################
variable "region" {}
variable "system" {}
variable "env" {}
variable "base_name" {}

#####################################
# VPC Variables
#####################################
variable "vpc_cidr_block" {}
```

## `modules/outputs.tf`

`output` でリソースの設定結果を出力する対象について記載したファイルです。  
なお注意しなければならないのは、このファイルはあくまで `modules` からのアウトプットです。  
`env/[環境名]/output.tf` へ `modules/outputs.tf` からのアウトプットをマッピングする必要があります。

```terraform:sample_vpc.tf
#####################################
# VPC
#####################################
output "sample_vpc" {
  value = aws_vpc.sample
}
```

## `modules/sample-vpc.tf`

Amazon VPC のリソース定義を記載します。

```terraform:sample-vpc.tf
#####################################
# VPC
#####################################
resource "aws_vpc" "sample" {
  cidr_block = var.vpc_cidr_block
  tags       = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${var.base_name}-sample" }))
}
```

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