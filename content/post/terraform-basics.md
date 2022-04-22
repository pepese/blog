---
title:       "Terraform入門"
URL:         "terraform-basics"
subtitle:    ""
description: "Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、Terraformのインストールから使い方までをご紹介します。"
keyword:     "Terraform, IaC"
date:        2022-04-22
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
categories:
- tech
---

Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。  
この記事では、Terraformのインストールから使い方までをご紹介します。

<!--more-->

# 用語

まずは用語について整理します。

|用語|意味|
|:---|:---|
|Configuration|インフラ設定のコード。要するに `*.tf` ファイルに DSL で書く Terraform のコードのこと。|
|HCL|HashiCorp Configuration Languageの略。`*.tf` ファイルで使われている DSL のこと。|
|Resource|Terraform で管理する対象の基本単位。|
|Data Source|Terraform 管理外だけど、 Terraform 内で参照したい参照専用の外部データ。|
|Provider|Resource や Data Source などを作成/更新/削除するプラグイン。aws/google/azurermなど。|
|Provisioner|リソースの作成/削除時に実行するスクリプトなどのプラグイン。local-exec/remote-exec/chefなど|
|State|Terraform が認識しているリソースの状態。 `*.tfstate` ファイルのこと。|
|Backend|State ファイルの保存先。local/s3/gcsなど。|
|Module|Resource や Data Source などを再利用可能なようにまとめた Configuration の単位。|

# Terraformのインストール

asdf を利用して Terraform をインストールします。  
asdf については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

```bash
$ asdf plugin add terraform
$ asdf plugin list
terraform
$ asdf list-all terraform
...
1.1.8
$ asdf install terraform 1.1.8
$ asdf global terraform 1.1.8
$ asdf current terraform
terraform       1.1.8          /Users/xxxxx/.tool-versions
$ terraform -v
Terraform v1.1.8
```

# 使い方

以下のような手順で利用します。

1. `.tf` ファイルを書きます
2. `terraform init` で Terraform プロジェクトを初期化します
    - `.terraform` ディレクトリ、`.terraform.lock.hcl` ファイルが作成され provider のバイナリダウンロードなどの設定が記録されます
3. `terraform plan` で設定に問題無いか・作成されるリソースを確認します
4. `terraform apply` で実際に環境へ適用します

# `.tf` ファイルを書く

以下の構成で作成します。

```bash
basic-flat-env/
├── main.tf        # Terraformバージョン、Provider、Backend、変数、locals変数、Outputsの設定
└── sample-vpc.tf  # resource（sampleサービスのVPC） の設定を記述
```

- `main.tf`
  - Terraform のバージョンに関する設定
  - AWS の Provider に関する設定
  - State ファイルの保存に関する設定
  - Terraform の変数定義
- `sample-vpc.tf`
  - サンプルで作成する Amazon VPC のリソース定義

## `main.tf`

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
  // Common
  region    = "ap-northeast-1"
  system    = "system"
  env       = "prd"
  base_name = "${local.system}-${local.env}"

  // VPC
  vpc_cidr_block = "10.0.0.0/16"
}

#####################################
# Outputs
#####################################
output "sample_vpc" {
  value = aws_vpc.sample
}
```

`profile` 変数への値の代入は、ファイルへの記載ではなく環境変数（ `TF_VAR_profile`  ）や `terraform` コマンドオプション（ `-var` ）からの読み込みも可能です。  
なお、 Terraform の変数へ環境変数から値を代入する場合は、変数名 `xxxxx` に対して環境変数 `TF_VAR_xxxxx` が対応しています。

## `sample-vpc.tf`

```terraform:sample-vpc.tf
#####################################
# VPC
#####################################
resource "aws_vpc" "sample" {
  cidr_block = local.vpc_cidr_block
  tags       = merge(tomap({ "Service" = "sample" }), tomap({ "Name" = "${local.base_name}-sample" }))
}
```

# Variable と Local Values

Variable は `variable` で定義する変数で、外部からのインプットや環境差分が発生する場合に使用します。  
Local Values は `locals` で定義・代入する変数で、モジュール内のローカル変数として使用します。

Variable への値の代入方法は以下があります。

- 実行時に指定
  - `terraform plan` などのコマンドを実行すると、コマンドライン上で値の入力を求められます
- コマンド引数による指定
  - `terraform` コマンド実行時に `terraform plan -var 'profile=hogehoge'` のような形でオプションで指定します
- 環境変数による指定
  - 変数名 `xxxxx` に対して環境変数 `TF_VAR_xxxxx` が対応しています
- 設定ファイルによる指定
  - `.tfvars` ファイルを作成して `terraform` コマンド実行時に `terraform plan -var-file hogehoge.tfvars` のような形でオプションで指定します

`.tfvars` ファイルは以下のように `variable` で定義された変数へ単純に値を代入します。

```terraform
profile = "hogehoge"
region  = "ap-northeast-1"
...
```

# State ファイル

State ファイルは AWS への設定情報を保持し、現在の `.tf` ファイルとの差分を求めることによって AWS へ設定を反映させます。  
先の例では `backend` （ State ファイルの保存場所）に S3 を設定しましたが、通常の場合はローカル（ `.terraform` ディレクトリ）に保存されます。  
State ファイルの保存場所は `terraform init` コマンド実行時の `.tf` 設定に基づいて作成されます。  
複数人で開発する場合、個々人の State ファイルに差分があっては、同じ `*.tf` ファイルであっても実行に差分が発生するため、 S3 など共有できる場所に保存するのがよいでしょう。

`backend` を設定せずに `terraform apply` まで実行すると `terraform.tfstate` というファイルがローカルに作成されます。

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