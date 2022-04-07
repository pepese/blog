---
title:       "AWS IAM入門"
URL:         "aws-iam-basics"
subtitle:    ""
description: "AWS IAM （Identity and Access Management）は、 AWS サービスで「認証」と「認可」の設定を行うことができるサービスです。この記事では、AWS IAM をざっくり理解できるよう概要を記載します。（強制 MFA についても記載しています。）"
keyword:     "AWS, IAM, MFA"
date:        2022-04-07
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- iam
categories:
- tech
---

AWS IAM （Identity and Access Management）は、 AWS サービスで「認証」と「認可」の設定を行うことができるサービスです。  
この記事では、AWS IAM をざっくり理解できるよう概要を記載します。（強制 MFA についても記載しています。）

<!--more-->

# 概要

AWS IAM は AWS リソースをセキュアに操作するために、認証・認可の仕組みを提供するサービスです。

- ID と認証情報の管理
  - IAM ユーザーの作成とパスワードポリシー・MFA
  - アクセスキー管理
- アクセス権限の管理
  - IAM ユーザーに対してポリシーを使用した AWS リソースへのアクセス許可
  - グループ単位のアクセス許可
- 権限の委任
  - EC2 などの AWS リソースへのロール付与
  - ロールに対するアクセス許可
- ID と権限のライフサイクル管理
  - AWS アカウントのアクティビティ監視
  - 認証情報の定期的なローテーション含めた管理

# 用語

## プリンシパル

AWS リソースにアクセスするユーザーやアプリケーションのことです。

## **IAM ユーザー**

IAM ユーザーは AWS アカウントの配下に作成され、 **AWSマネジメントコンソール** にアクセスするための ID/Password を割り当てることができます。  
IAM ユーザーの単位で **アクセスキー** 、 **シークレットアクセスキー** などのキーを払い出すことができ、 AWS CLI 実行時などの認証・認可に利用できます。

## **フェデレーションユーザー**

フェデレーションユーザーは、 IAM ユーザーとは異なり、 AWS アカウント配下で管理される ID ではありません。  
フェデレーションユーザーは外部の　**IDプロバイダ**（**IdP**）が提供する ID/Password です。  
ID プロバイダには OpenID Connect や SAML 2.0 などが挙げられます。  
フェデレーションユーザーに **IAM ロール** を IAM 上で作成・アタッチすることでアクセス・権限のコントロールを行うことができます。

## **IAM ユーザーグループ**

IAM ユーザーの集合です。

## **IAM ロール**

IAM ロールは、 **通常は AWS リソースへアクセス権限の無い** ユーザー、アプリケーション、AWS リソース（EC2 など）に役割を **移譲** するために利用します。  
そのため、 IAM ユーザーやグループに権限を与える際は IAM ロールは利用せず、直接ポリシーをアタッチすることになります。  
IAM ユーザ・グループと同様 IAM ロールにも **ポリシー** を割り当てることができ、このポリシーでアクセスコントロールを行います。

## **ポリシー**

IAM ユーザー・IAM グループ・ IAM ロールに割り当てるアクセス許可で、 JSON 形式で管理されます。  
ポリシーには以下の種類があります。

- AWS 管理ポリシー
  - AWSがあらかじめ作成し、IAM 上で提供してくれているポリシー
- カスタマー管理ポリシー
  - ユーザーが作成・管理するポリシー
  - AWS管理ポリシーには存在せず、且つ、好きなポリシーを作ることができます
- インラインポリシー
  - IAM ユーザー・IAM グループ・IAM ロール内に作成するポリシー
  - カスタマー管理ポリシーとは異なり、他のIAMユーザー・IAMグループ・ロールへ再利用することはできません

## Amazon Resource Name （ ARN ）

**ARN** は Amazon Resource Name の略で AWS リソースを一意に識別する値です。  
以下のような形式で表現されます。

```
arn:partition:service:region:account-id:resource
arn:partition:service:region:account-id:resourcetype/resource
arn:partition:service:region:account-id:resourcetype:resource
```

- `partition`
  - リソースが配置されるパーティション（標準のAWSリージョンの場合、単に「 **aws** 」）
- `service`
  - AWSのサービス名（「apigateway」や「ec2」など）
- `region`
  - リソースが配置されるリージョン（東京リージョンであれば「ap-northeast-1」）
- `account-id`
  - リソースを保有している AWS アカウントID（数字のみ）
  - 一部の ARN はこのアカウント番号を必要としないため省略されます
- `resource` `resourcetype/resource` `resourcetype/resource`
  - サービスにより様々
  - リソースタイプの指標（IAMユーザやRDSなど）が含まれることがよくある
  - さらにスラッシュ（/）またはコロン（:）、リソース名自体が続く
  - 一部のサービスではパス（ファイルパス、URIなど）を指定できる（ARNのパス）

[参考](https://docs.aws.amazon.com/ja_jp/general/latest/gr/aws-arns-and-namespaces.html)

# IAM ロール

IAM ロールは自分の AWS アカウントの権限を移譲するためのものです。  
そして、 IAM ロールを移譲する際には、 **移譲対象を信頼** する必要があります。  
この信頼する対象に関するポリシーを **信頼ポリシー** と呼びます。  
信頼ポリシーは以下のような **AssumeRolePolicyDocument** で表現します。

```javascript
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "",
      "Effect": "Allow",
      "Principal": { // 信頼する対象
        "AWS": [ "123456789012" ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

上記は「 AWS アカウント `123456789012` を信頼する」という意味になります。  
AWS STS (AWS Security Token Service) の 「sts:AssumeRole」という機能を通じて、信頼する対象に対してロールを付与します。  
信頼ポリシーは、ロールが信頼する相手に関する情報を記載しているだけなので、何かしらの権限を付与するためにはロールに追加でポリシーをアタッチする必要があります。  
さらに例を挙げると、以下は「AWS サービス EC2 を信頼する」という意味になります。

```javascript
{
  "Version" : "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": { // 信頼する対象
        "Service": [ "ec2.amazonaws.com" ]
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

# アクセス権限の管理

## ポリシードキュメント（ JSON ）

ポリシーは JSON ドキュメントで表現します。  
項目には以下があります。

- Version
  - ポリシー言語のバージョン（ `2012-10-17` ※最新版を記載するようにしましょう）
- Statemant
  - アクセス許可に関する要素（ Effect/Action/Resource など ）を複数含むブロック
  - 複数作成可能
- Effect
  - アクセスを要求した結果の許可/拒否の指定 `Allow` `Deny`
  - 「暗黙的な Deny（デフォルト） ＜ 明示的な Allow ＜ 明示的な Deny」の基準で評価されます
- Principal（誰が）
  - アクセスを許可/拒否される主体を ARN 形式で記述します
- Action（何を）
  - アクセスを許可/拒否されるアクション `Action` `NotAction` （[一覧](https://docs.aws.amazon.com/ja_jp/AWSEC2/latest/APIReference/API_Operations.html)）
- Resource（何に対して）
  - アクセス許可/拒否対象のリソースを ARN 形式で記述します
- Condition（いつ）
  - ポリシーが有効になるタイミング
  - 条件演算子・条件キーを利用して記載します

具体例としては以下のようになります。  
「example_bucket」という名前の S3 バケットへのアクセス許可を表すポリシーになります。

```javascript
{
    "Version": "2012-10-17",
    "Statement": {
        "Effect": "Allow",
        "Action": "s3:ListBucket",
        "Resource": "arn:aws:s3:::example_bucket"
    }
}
```

## ポリシーの種類

ポリシーには以下の種類があります。

- AWS 管理ポリシー
  - AWS があらかじめ用意してくれているポリシー
  - 例） `arn:aws:iam::aws:policy/IAMReadOnlyAccess`
- カスタマー管理ポリシー
  - ユーザが独自に作成して命名するポリシーで、再利用可能
- インラインポリシー
  - ユーザが独自に IAM ユーザー・グループ・ロールに対して埋め込むポリシー
  - ポリシー名が無く、再利用不可能

通常は AWS 管理ポリシーを利用し、適当なものが無い場合は カスタマー管理ポリシーを利用します。  
インラインポリシーはあまりお勧めできません。

## IAM ユーザ・グループへの権限付与

IAM ユーザ・グループへポリシーをアタッチすることによって権限管理を行います。  
IAM ユーザ一人一人に対して権限管理を行うのは非効率なので、 Administoator 、 SystemOperator といった IAM グループを作成して権限を付与し、 IAM グループへ IAM ユーザを所属させることによって権限管理することをお勧めします。  
IAM ユーザーは複数の IAM グループ（最大 10 ）へ所属することが可能です。

## IAM ロールへの権限付与

他 AWS アカウントや自 AWS アカウント内の AWS サービス（例えば EC2）など、対象毎に IAM ロールを作成します。  
その IAM ロールに対して信頼ポリシー（誰を信頼するのか？）と権限を与えるポリシー（何が出来るのか？）を付与します。  
（厳密には Permissions boundary といったポリシーもありますが、ここでは割愛します。）  
以上のように作成した IAM ロールを対象に付与します。

# IAM ユーザーに MFA を強制する

セキュリティを上げるために MFA （多要素認証）を導入するケースが多くあります。  
ここでは MFA を強制するためのポリシーについて紹介します。

```javascript
{
    "Effect": "Deny",
    "NotAction": [
        "iam:CreateVirtualMFADevice",
        "iam:EnableMFADevice",
        "iam:GetUser",
        "iam:ListUsers",
        "iam:ListMFADevices",
        "iam:ListVirtualMFADevices",
        "iam:ResyncMFADevice",
        "sts:GetSessionToken",
        "iam:ChangePassword" // 初回ログイン時にパスワード変更が必要な場合には記載
    ],
    "Resource": "*",
    "Condition": {
        "BoolIfExists": {
            "aws:MultiFactorAuthPresent": "false"
        }
    }
}
```

上記のポリシーを IAM ユーザーもしくは IAM グループに適用することで、 MFA 認証していない場合にあらゆる権限がなくなります。  
「 MFA 認証していない場合、 NotAction 以外の操作は全て拒否」という意味になります。  
なお、上記の例では `NotAction` に `iam:ChangePassword` を記載しているので、 MFA 認証無しでもパスワード変更が可能ということになります。  
`iam:ChangePassword` を削除した上で、初回ログイン後に MFA デバイスを登録し、ログインし直してパスワード変更するオペレーションのほうが好ましいかもしれません。

なお、 AWS CLI での MFA 認証は以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/aws-cli-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">AWS CLI入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">AWS CLI は AWS リソースの操作を行う Command Line Interface (CLI) ツールです。AWS のマネージメントコンソールをボチボチする代わりにコマンドを叩くイメージです。この記事では、AWS CLI のインストールと設定で役立つ情報（MFA・多要素認証など）を紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# おすすめ書籍

<!-- ad link - amazon/rakuten books - aws basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"AWSではじめるインフラ構築入門 安全で堅牢な本番環境のつくり方 [ 中垣 健志 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3437\/9784798163437.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3j7fBNl","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/h6WEYh","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"e0IUt","s":"s"});
</script>
<div id="msmaflink-e0IUt">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>