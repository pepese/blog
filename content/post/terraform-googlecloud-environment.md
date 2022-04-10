---
title:       "Google Cloud のための Terraform 環境構築"
URL:         "terraform-googlecloud-environment"
subtitle:    ""
description: "Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事ではTerraformを使ってGoogle CloudのIaC環境構築についてご紹介します。"
keyword:     "Terraform, Google Cloud, GCP, 環境構築"
date:        2022-04-10
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
- "google cloud"
categories:
- tech
---

Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。  
この記事ではTerraformを使ってGoogle CloudのIaC環境構築についてご紹介します。

<!--more-->

Terraform の入門については以下の記事をご参照ください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/terraform-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Terraform入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Terraformは、IaC（Infrastructure as Code）を実現するツールの 1 つです。この記事では、Terraformのインストールから使い方までをご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# サービスアカウントの作成

Terraform 実行時の権限を与えるサービスアカウントを作成する。  
権限は editor ロールで、 `terraform` という名称とする。  

Google Cloud コンソールへログインし Cloud Shell にて以下を実行する。  
なお、権限管理は Owner ロールにしか実施できないので下記コマンド実行時のユーザに注意は必要。

```bash
$ gcloud config set project <project_id>

$ gcloud iam service-accounts create terraform --display-name "Used by Terraform"

$ gcloud projects add-iam-policy-binding <project_id> \
  --member serviceAccount:terraform@<project_id>.iam.gserviceaccount.com \
  --role roles/editor
```

Terraform で IAM 関係の操作を行う場合は権限が不足して実行できないので、追加で以下のような権限を付与してやる必要がある。

```bash
$ gcloud projects add-iam-policy-binding <project_id> \
  --member=serviceAccount:terraform@<project_id>.iam.gserviceaccount.com \
  --role=roles/resourcemanager.projectIamAdmin
```

# tfstate ファイル保存用の　Cloud Storage バケットの作成

Google Cloud コンソールにて Terraform の tfstate ファイル配置用 Cloud Storage バケットを公開アクセス禁止で作成する。

# ローカル環境の設定

Google Cloud コンソールにて「サービスアカウントの作成」にて作成したサービスアカウントから JSON 形式で秘密鍵を作成しファイルをダウンロードする。  
ダウンロードしたファイルをローカルマシンの任意の場所に配置し、 以下のように環境変数 `GOOGLE_APPLICATION_CREDENTIALS` の設定を行う。

```bash
$ export GOOGLE_APPLICATION_CREDENTIALS=<path to downloaded json file>
```

# .tf ファイルの作成

.tf ファイルの分割については好みではあるが、ここでは以下を作成する。  
なお、ここでは Modules などは使用せずにフラットにファイルを配置する前提で記載する。

- `backend.tf`
  - tfstate ファイルの保存に関する設定
- `provider.tf`
  - Google Cloud の Provider に関する設定
- `versions.tf`
  - Terraform のバージョンに関する設定
- `variables.tf`
  - Terraform の変数定義（ファイル内容例については省略）
- その他 .tf ファイル
  - 作成したいリソース定義など（ファイル内容例については省略）


## `backend.tf` 

```terraform:backend.tf
terraform {
  backend "gcs" {
    bucket = "<tfstate ファイル保存用の　Cloud Storage バケット名>"
    prefix = "<任意のプレフィックス、環境名などがいいかも>"
  }
}
```

## `provider.tf`

```terraform:provider.tf
provider "google" {
  project = "<project_id>"
  region  = "<region_id>"
  zone    = "<zone_id>"
}
```

## `versions.tf`

```terraform:versions.tf
terraform {
  required_version = "~> 1.1.0"
}
```

# おすすめ書籍

<!-- ad link - amazon/rakuten books - google cloud basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"図解即戦力　Google Cloudのしくみと技術がこれ1冊でしっかりわかる教科書 [ 株式会社grasys ／ Google Cloud 西岡典生、田丸司 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3017\/9784297123017.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37iaebm","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMkNFF","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"ju6Y9","s":"s"});
</script>
<div id="msmaflink-ju6Y9">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>