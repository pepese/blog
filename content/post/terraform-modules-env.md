---
title:       "Terraform Modulesで環境を分割する"
URL:         "terraform-modules-env"
subtitle:    ""
description: "Terraform Modulesはソースコードをモジュール化することでテンプレートとして使うことができるようになる機能です。この記事では、Terraform Modulesを利用した本番・検証などの環境分割の方法についてご紹介します。"
keyword:     "Terraform, Modules, Module, モジュール, 環境, 分割"
date:        2022-04-11
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
categories:
- tech
---

Terraform Modulesはソースコードをモジュール化することでテンプレートとして使うことができるようになる機能です。  
この記事では、Terraform Modulesを利用した本番・検証などの環境分割の方法についてご紹介します。

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

なお、 [Terraform Registry](https://registry.terraform.io/) で提供されているモジュールは以下のように呼び出すことができます。  
[AWS VPC Terraform module](https://registry.terraform.io/modules/terraform-aws-modules/vpc/aws/latest) の例です。

```terraform
module "vpc" {
  source = "terraform-aws-modules/vpc/aws"
  // 以降、変数へ値を代入する
}
```

# 命名規則

この記事でご紹介する例は下記の記事の命名規則に従っています。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/terraform-naming" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Terraformの命名規則 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、筆者が利用しているTerraformの命名規則をご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# 環境分割方法

あくまで筆者独自の方法なので参考までにお願いします。  
ディレクトリ構成は以下の通りです。

```bash
.
├── env              # 環境差分用ディレクトリ（必要分だけ作成）
│   ├── prd           # prd 環境用ディレクトリ
│   │   ├── backend.tf # tfstate ファイルを保存する S3 bucket/key/region 情報を代入、variables.tf で定義した変数へ値を代入
|   |   ├── main.tf
|   |   └── outputs.tf
│   ├── stg
│   │   ├── backend.tf
|   |   ├── main.tf
|   |   └── outputs.tf
│   └── dev
│   │   ├── backend.tf
|   |   ├── main.tf
|   |   └── outputs.tf
└── modules         # 各環境共通で利用する resources をモジュール化
    ├── main_alb.tf  # resource（lb） の設定を記述（設定の記載が分かりやすいよう main_xxx.tf という命名）
    ├── main_rds.tf  # resource（rds） の設定を記述
    ├── main_vpc.tf  # resource（vpc） の設定を記述
    ├── main_ec2.tf  # resource（ec2） の設定を記述
    ├── ...          # resource（その他） の設定を記述
    ├── outputs.tf   # outputし、他のリソースから tfstate ファイル経由で参照されるデータを記載
    ├── provider.tf  # aws などの provider 情報を記載
    ├── variables.tf # local、variables などの変数を記載
    └── versions.tf  # terraform のバージョンを記載
```

使い方は対応する環境をカレントディレクトリとして、 `terraform` コマンドを実行するだけです。

```bash
# ファイルフォーマット
$ cd env/[環境名]
$ terraform fmt ../../modules
$ terraform fmt

# 個別のリソースのterraform 初期化
$ cd env/[環境名]
$ terraform init

# 個別のリソースの設定確認
$ cd env/[環境名]
$ terraform validate

# 個別のリソースの差分確認
$ cd env/[環境名]
$ terraform plan

# 個別のリソースの適用
$ cd env/[環境名]
$ terraform apply

# 個別のリソースの削除
$ cd env/[環境名]
$ terraform destroy
```

以降で個々のファイルについて説明していきます。  
`aws_vpc` を 1 つ構築する簡易な例になっています。


## `modules/versions.tf`

Terraform のバージョンなどの設定ファイルです。

```terraform:versions.tf
terraform {
  required_version = "~> 1.1.0"
}
```

## `modules/provider.tf`

Provider の設定ファイルです。  
AWS を Provider する場合の設定ファイル例を下記しておきます。

```terraform:provider.tf
provider "aws" {
  region = var.region
  default_tags {
    tags = {
      System    = var.system
      Env       = var.env
      Terraform = "true"
    }
  }
}
```

上記のように `default_tags` を変数で体入する形で設定しておくと非常に便利です。

## `modules/variables.tf`

`modules` 内で利用される変数を管理するファイルです。  
`env/[環境名]/main.tf` にて変数に値を代入します。  
これによって環境差分を実現できます。

```terraform:variables.tf
variable "account_id" {}
variable "access_key" {}
variable "secret_key" {}
variable "region" {}
variable "system" {}
variable "env" {}
variable "base_name" {}
variable "vpc_cidr_block" {}
```

## `modules/outputs.tf`

`output` でリソースの設定結果を出力する対象について記載したファイルです。  
なお注意しなければならないのは、このファイルはあくまで `modules` からのアウトプットです。  
`env/[環境名]/output.tf` へ `modules/outputs.tf` からのアウトプットをマッピングする必要があります。

```terraform:main_vpc.tf
output "main_vpc" {
  value = aws_vpc.main
}
```

## `modules/main_xxx.tf`

AWS や Google Cloud などの Provider に対応したリソース定義を記載します。  
ファイル名に `main_` とプレフィックスをつけているのは、単にリソース定義であることを明確にしたいのと、ソートした時に見やすいからです。  
ここでは AWS VPC を作成する `main_vpc.tf` というファイルの例を記載します。

```terraform:main_vpc.tf
resource "aws_vpc" "main" {
  cidr_block = var.vpc_cidr_block
  tags       = merge(tomap({ "Service" = "main" }), tomap({ "Name" = "${var.base_name}-main" }))
}
```

## `env/[環境名]/backend.tf`

State ファイル（ `*.tfstate` ）の保存に関する設定ファイルを作成します。

```terraform:backend.tf
terraform {
  backend "s3" {
    bucket = "your-bucket-name"  // State ファイルを配置するバケット
    key    = "terraform.tfstate" // State ファイルを配置するパス・ファイル名
    region = "ap-northeast-1"    // S3のリージョン
  }
}
```

## `env/[環境名]/main.tf`

`modules` の呼び出し元です。  
`../../modules` でモジュールを呼び出して `modules/variables.tf` に定義した変数に値を代入します。  
これによって環境差分を実現できます。  
`modules` の他にも必要に応じて変数を定義してください。


```terraform:main.tf
variable "account_id" {
  default = "xxxxxxxxxxxx"
}
variable "access_key" {}
variable "secret_key" {}
variable "system" {
  default = "pepese"
}
variable "region" {
  default = "ap-northeast-1"
}
variable "env" {
  default = "tst"
}

module "modules" {
  source     = "../../modules"
  account_id = var.account_id
  access_key = var.access_key
  secret_key = var.secret_key

  // 以降、 modules の variables.tf 内の変数へ値を代入

  #####################################
  # Common
  #####################################
  system = var.system
  region = var.region
  env    = var.env

  #####################################
  # Tags
  #####################################
  // provider.tf の default_tags にて全ての AWS リソースに共通するタグを設定
  // System = var.system, Env = var.env, Terraform = "true"
  base_name = "${var.system}-${var.env}"

  #####################################
  # vpc
  #####################################
  vpc_cidr_block = "10.0.0.0/16"
}
```

## `env/[環境名]/outputs.tf`

`output` でリソースの設定結果を出力する対象について記載したファイルです。  
`modules/outputs.tf` に記載した `modules` の `output` を実際に出力します。

```terraform:outputs.tf
output "vpc" {
  value = module.modules.main_vpc
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