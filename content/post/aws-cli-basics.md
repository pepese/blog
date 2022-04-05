---
title:       "AWS CLI入門"
URL:         "aws-cli-basics"
subtitle:    ""
description: "AWS CLI は AWS リソースの操作を行う Command Line Interface (CLI) ツールです。AWS のマネージメントコンソールをボチボチする代わりにコマンドを叩くイメージです。この記事では、AWS CLI のインストールと設定で役立つ情報（MFA・多要素認証など）を紹介します。"
keyword:     "AWS, AWS CLI, MFA, 多要素認証"
date:        2022-04-04
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- "aws cli"
categories:
- tech
---

AWS CLI は AWS リソースの操作を行う Command Line Interface (CLI) ツールです。  
AWS のマネージメントコンソールをボチボチする代わりにコマンドを叩くイメージです。  
この記事では、AWS CLI のインストールと設定で役立つ情報（MFA・多要素認証など）を紹介します。

<!--more-->

# インストール

Homebrew を利用してインストールします。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/mac-homebrew-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Homebrew入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">HomebrewはMacで利用できるソフトウェアパッケージマネージャ。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

```bash
$ brew install awscli
```

# プロファイル

AWS CLI を実行する際、以下のような環境変数でクレデンシャルを設定することにより認証され AWS にアクセスすることができます。

```bash
$ export AWS_ACCESS_KEY_ID=<アクセスキー>
$ export AWS_SECRET_ACCESS_KEY=<シークレットキー>
$ export AWS_DEFAULT_REGION=ap-northeast-1 # 任意のリージョン
```

しかし、複数の AWS 環境があるなど、いちいち環境変数を変えるのは面倒です。  
そういう時に **プロファイル** が利用できます。

```bash
# プロファイルの作成
$ aws configure --profile <プロファイル名>
```

上記コマンドでプロファイルを作成すると以下のような `~/.aws/credentials` と `~/.aws/config` ファイルが作成されます。

```bash
# ~/.aws/credentials

[default]
aws_access_key_id = <デフォルトのアクセスキー>
aws_secret_access_key = <デフォルトのシークレットキー>

[<プロファイル名>]
aws_access_key_id = <プロファイル名のアクセスキー>
aws_secret_access_key = <プロファイル名のシークレットキー>
```

```bash
# ~/.aws/config

[default]
region = <デフォルトのリージョン>
output = <デフォルトの出力形式>

[profile <プロファイル名>]
region = <プロファイル名のリージョン>
output = <プロファイル名の出力形式>
```

なお、 `--profile` オプションで `<プロファイル名>` を指定しない場合は `default` という名前のプロファイルが設定されます。  
各コマンドでプロファイルのクレデンシャルを指定して実行したい場合も以下のように `--profile` オプションを付けて実行します。

```bash
$ aws s3 ls --profile <プロファイル名>
```

いちいち `--profile` オプションを指定するのは面倒です。  
そんな時は、以下の環境変数を指定すると `--profile` オプションを省略してもプロファイルが適用されます。

```bash
$ export AWS_DEFAULT_PROFILE=<プロファイル名>
# もしくは
$ export AWS_PROFILE=<プロファイル名>
```

プロファイルの一覧は以下のコマンドで取得できます。

```bash
$ aws configure list-profiles
```

# MFA認証（多要素認証）

AWS CLI で MFA 認証（多要素認証）が必要な場合、通常、以下を実施する必要があります。

- [AWS CLI 経由で MFA を使用してアクセスを認証する](https://aws.amazon.com/jp/premiumsupport/knowledge-center/authenticate-mfa-cli/)

しかし、超面倒です。  
そんな時は [aws-mfa](https://github.com/broamski/aws-mfa) というサポートツールを利用することで少し楽になります。

## aws-mfa を利用した MFA 認証

インストールは以下です。  
なお、 Python(pip) のインストールについては asdf が楽なのでおすすめです。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

```bash
$ pip install aws-mfa
or
$ pip3 install aws-mfa
```

`~/.aws/credentials` ファイルに以下を追記します。

```bash
[<プロファイル名>-long-term]
aws_access_key_id = <あなたのIAM Userのアクセスキー>
aws_secret_access_key = <あなたのIAM Userのシークレットキー>
aws_mfa_device = arn:aws:iam::312574223261:mfa/<あなたのIAM User ID>
```

任意のプロファイル名に `-long-term` がついているのがポイントです。  
上記が出来たら、以下のコマンドを実行します。

```bash
$ aws-mfa --profile <プロファイル名>
```

ワンタイムトークンの入力が求められ、入力後 `~/.aws/credentials` に以下が追記されます。

```bash
[<プロファイル名>]
assumed_role = False
aws_access_key_id = <一時的なアクセスキー>
aws_secret_access_key = <一時的なシークレットキー>
aws_session_token = <一時的なセッショントークン>
aws_security_token = <一時的なセキュリティトークン>
expiration = <キー・トークンの有効期限>
```

`<プロファイル名>-long-term` プロファイルを作成して `aws-mfa --profile <プロファイル名>` コマンドを実行したことにより、 `<プロファイル名>` のプロファイルが一時トークン・セッションを含んだ形で作成されるという流れです。  
生成されたプロファイルを有効にするために以下を実行します。

```bash
$ export AWS_DEFAULT_PROFILE=<プロファイル名>
# もしくは
$ export AWS_PROFILE=<プロファイル名>
```

以上でトークン・セッションの有効期限内においては AWS CLI が利用可能になります。  
有効期限が切れたら、 `aws-mfa --profile <プロファイル名>` を再実行すればよいです。

# おすすめ書籍

<!-- amazon affiliate kindle aws basics --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08QTQBJKZ&linkId=b2aea688fd22ddb6076a65af2f9c3738"></iframe>

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>