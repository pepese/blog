---
title:       "Google Cloud のための Terraform 環境構築"
URL:         "terraform-googlecloud-environment"
subtitle:    ""
description: "Terraform を使って Google Cloud の IaC (Infrastructure as Code) 環境を構築する。"
keyword:     "Terraform, Google Cloud, GCP, 環境構築"
date:        2022-03-30
author:      "ぺーぺーSE"
image:       ""
tags:
- terraform
- "google cloud"
categories:
- tech
---

[Terraform](https://www.terraform.io/) を使って Google Cloud の IaC (Infrastructure as Code) 環境を構築する。

<!--more-->

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
  required_version = "~> 1.0.0"
}
```

# おすすめ書籍

<!-- amazon affiliate kindle google cloud basics --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B09DP1BR35&linkId=cfdf74ae820a1204fd3aae3a2d07d272"></iframe>