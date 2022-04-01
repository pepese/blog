---
title:       "AWS Lambda入門"
URL:         "aws-lambda-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-03-31
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- lambda
categories:
- tech
---

[AWS Lambda](https://docs.aws.amazon.com/ja_jp/lambda/) について入門する。

<!--more-->

# AWS Lambda の基本

## 処理の流れ

AWS Lambda は **イベントソース** から **Lambda 関数** が呼び出され **ダウンストリーム** へアクセスする構成。  
「イベントソース -> Lambda 関数 -> ダウンストリーム」という流れ。

**イベントソース** は、イベントの発生元となる AWS サービスやユーザが開発したアプリケーション。  
Lambda 関数が呼び出されるきっかけ。  

**ダウンストリーム** は、 Lambda 関数がアクセスする AWS サービスやユーザが開発したアプリケーション。

## イベントソース

イベントソースは、イベントの発生元となる AWS サービスやユーザが開発したアプリケーション。  
Lambda 関数がイベントを検知するパターンとして以下の 3 つある。

- ポーリング 且 ストリーム
  - Lambda関数自体がイベントを検知（ Kinesis、DynamoDBなど）
- ポーリング 且 ストリームではない
  - Lambda関数自体がイベントを検知（ SQSなど）
- プッシュ
  - Lambda関数にイベントを通知

Lambda 関数の処理タイプとして以下の 2 つある。

- 同期：リクエストの処理結果含め返却
- 非同期：リクエストが正常に受信できたかのみ返却

イベントソースとなる AWS サービスは、上記のイベント検知パターンと処理タイプの組み合わせで分類することができる。  
また、以下のパターンによってリトライの動きが異なる。

- 同期 ＋ ポーリング 且 ストリーム
  - AWS サービス：Amazon DynamoDB、Amazon Kinesis Data Streams
  - リトライ
    - データの有効期限が切れるまでリトライ
    - 失敗の間は同一イベントソースからの新規イベントはブロックされる
- 同期 ＋ ポーリング 且 ストリームではない
  - AWS サービス：Amazon SQS
  - リトライ
    - （Amazon SQSなので） Visibility Timeout のちに再実行される
    - 新規イベントの実行はブロックされない
- 同期 ＋ プッシュ
  - AWS サービス：Amazon Cognito、AWS CloudFormation、Amazon Cloud Watch Logs、Amazon Alexa、Amazon Lex、Amazon API Gateway、Amazon CloudFront、Amazon Kinesis Data Firehose
  - リトライ
    - エラーのステータスコード・内容が返却される
    - リトライはイベントソースの設定による
- 非同期
  - AWS サービス：Amazon S3、Amazon SNS、Amazon SES、Amazon Cloud Watch Events、AWS CodeCommit、AWS Config、AWS IoT ボタン
  - リトライ
    - 2 回まで自動リトライ（遅延あり）され、その後イベントは破棄される
    - Dead Letter Queue（DLQ）を設定することでイベントを Amazon SQS / Amazon SNS へ移動可能

## Lambda 関数

Lambda ファンクションと呼ばれることもある。  
以下の特徴を持つ。

- 標準もしくは [カスタムラインタイム](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/runtimes-custom.html) でサポートされている言語で記述（ [ランタイムサポートポリシー](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/runtime-support-policy.html) ）
- それぞれが隔離されたコンテナ内で実行され、「 1 コンテナ＝ 1 イベント」を処理する
- 実行時は言語の関数もしくは **ハンドラー** を指定し、 **トリガー** となる AWS サービスから JSON 形式の **イベント** データを渡すことが可能
  - イベントソースが S3 の場合、バケット名などがイベントデータとなる
- コードは依存ライブラリをパッケージングした上でアップロードする（ **デプロイパッケージ** ）
  - 内部的には S3に保存されており、S3 の ARN で指定することも可能
- 以下の基本設定が可能（最新情報要確認 -> [Lambda クォータ](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/gettingstarted-limits.html) ）
  - メモリ： 64 MB ごとに 128 MB 〜 3008 MB で指定可能で、 CPU 能力もこれに比例する
  - タイムアウト： Lambda 関数の実行時間（最大 900 秒）
  - 実行ロール： Lambda 関数からアクセス可能な AWS リソースをコントロールするための IAM ロール

## 実行環境

Lambda 関数が実行される環境は Amazon Linux 2 なので、OS のネイティブライブラリを利用する場合はその前提で実装すること。  
また、 [Lambda 命令セットアーキテクチャ](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/foundation-arch.html) も参照のこと。  
なお、 [ランタイムサポートポリシー](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/runtime-support-policy.html) でサポートされる標準以外の実行環境が必要な場合は [カスタムラインタイム](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/runtimes-custom.html) を利用のこと。

## デプロイパッケージ

Lambda 関数コードのデプロイパッケージには以下の 2 種類ある。

- 関数コードとその依存関係を含む .zip ファイルアーカイブ
- 関数コードとその依存関係を含む コンテナーイメージ
  - [Lambda でのコンテナイメージの使用](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/lambda-images.html)

## VPC アクセス

Lambda 関数は指定の VPC 内で起動することにより、インターネットを介さず VPC 内のリソースへアクセスすることができる。  

内部的には ENI がフェッチされるので、 VPC サブネットやセキュリティグループを設定する。  
Lambda 関数がコールドスタートする際は、この ENI フェッチにレイテンシ（10〜30秒）が発生するが、ENI は複数の Lambda 関数から共有される構成になっているため、都度レイテンシが発生するわけではない。  
この ENI にはパブリック IP が付与されないため、インターネットアクセスが必要な場合は VPC に NAT Gateway の設定が必要になる。  
なお、 VPC の制限により ENI のサブネット IP が確保できない場合は起動に失敗する。（非同期呼び出しの場合は CloudWatch Logs に記録されないので注意）

## 実行ロール

IAM ロールで指定して、各種 AWS サービスへのアクセスコントロールを行うことができる。  
最低でも CloudWatch Logs へのアクセス許可が必要。

## 同時実行数

デフォルトは 1000 で、超えるとスロットリングエラーとなる。（最新情報要確認 -> [Lambda クォータ](https://docs.aws.amazon.com/ja_jp/lambda/latest/dg/gettingstarted-limits.html) ）  
`ConcurrentExecutions` と `UnreservedConcurrentExecutions` メトリクスで確認できる。

## ライフサイクル

Lambda 関数の起動から停止までのライフサイクルは以下の通り。

1. ENI の作成（ VPC 利用時、且つコールドスタートの時だけ 10 〜 30 秒）
2. コンテナの作成
3. デプロイパッケージのロード ※内部的には S3 からダウンロードしてロード
4. デプロイパッケージの展開
5. ランタイム起動・初期化
6. 関数の実行
7. コンテナの破棄

上記の 1 〜 6 がコールドスタート時の動きで、 6 のみがウォームタート時の動きとなる。  
間隔をあまり開けずに定期的に Lambda が実行されていればコールドスタートは発生しない。

# AWS Lambda の使い方

## プログラミングモデル

- ハンドラー
  - 言語の関数もしくはメソッドで、実行時のエントリーポイントとなる
  - 1 つ目のパラメータとして JSON 形式のイベントデータが渡される
- コンテキスト
  - ラインタイムに関する情報が含まれる
  - 2 つ目のパラメータとして渡される
- ロギング
  - 標準出力が CloudWatch Logs に書き込まれる
  - CloudWatch Logs の制限を受けるため、スロットリングによって失われる可能性あり
- 例外処理
  - 言語仕様によりことなる
  - 同期呼び出しの場合はレスポンスされる
- 注意点
  - ステートレスな処理モデルにする必要がある
  - 同じインスタンスで実行されるとは限らない
  - ローカルリソースの利用・アクセスは処理実行中のみ有効
  - データは他の永続化サービスへ保存すること

## Lambda 関数の設定

- VPC アクセスの設定
- 同時実行数の管理
  - アカンウト毎に割り当てられたデフォルト同時実行数 1000 を関数毎に割り当て可能
  - ダウンストリーム（ DB とか）に対する流量制御に利用
- 環境変数
  - キーと値のペアで設定可能
    - キーと値の合計が 4kB 以下、英字開始で英数字とアンスコのみ
  - デフォルトの環境変数もあり、関数の中から参照可能（ `AWS_REGION` など）
    - リザーブドがありの場合は上書き不可能
  - 関数のバージョニング機能に環境変数も保存される
  - AWS Key Management Service によりデプロイ後に自動的に暗号化
    - デフォルトでキーが自動生成されるが、独自キーの適用も可能
- バージョニング
  - Lambda 関数と環境変数などの情報を PublishVersion に保存可能
  - バージョンに対してエイリアス設定・付け替え可能
  - routing-config を利用することでエイリアスに対してトラフィックの割合を管理可能（カナリア的な）
- Lambda Layers
  - 各 Lambda 関数から共通で参照可能なコンポーネントを定義・参照可能
  - ZIP でアップロード
  - カスタムランタイムを Layer としてアップロードも可能
  - `/opt` の下に指定した順に展開される（同名の場合後勝ち）
  - 非圧縮で 250MB まで（ Lambda 関数と同じ）
- タグ付け
  - 他の EC2 などのサービスと同様でタグ付け可能で、グループ化とフィルタリングに利用
- デッドレターキュー（ **DLQ** ）
  - 非同期実行時に 2 回リトライ行われた後にも失敗した場合にイベントを退避可能
    - Amazon SQS/SNS
  - 退避先へアクセスする権限付与が必要
  - DLQ 自体に書き込めない場合 `DeadLetterErrors` メトリクスに記録されイベントは破棄される
- Custom Runtimes
  - Linux 互換のランタイムを持ち込み可能 -> 任意の言語を実行可能になる
  - Custom Runtimes を利用する場合は Lambda 関数に `bootstrap` と呼ばれる実行ファイルを含める必要あり
    - Lambda Layers として登録も可能
  - `bootstrap` に以下の 2 つを実装する必要あり
    - `Initialization Tasks` ：ループしてイベントを待ち受ける
      - 環境変数の読み取り、関数の初期化、エラーハンドリングなどを実装する
    - `Processing Tasks` ：請求時間とタイムアウトの対象
      - イベントの取得、トレーシングヘッダの伝播、コンテキストオブジェクトの作成、関数ハンドラ呼び出し、レスポンス・エラーのハンドリング、クリーンアップなどを実装
- モニタリング
  - CloudWatch を利用したメトリクス監視
  - X-Ray を利用したトラブルシューティング
- 注意点
  - Labmda 関数が保証するのは最低 1 回実行されること
    - 冪等性を確保するなど設計しておく必要あり

# おすすめ書籍

<!-- amazon affiliate kindle aws lambda --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B09TVG5H17&linkId=cb907028d3ba6043af684b6f593c2763"></iframe>