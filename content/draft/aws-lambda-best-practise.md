---
title:       "Aws Lambda Best Practise"
URL:         "aws-lambda-best-practise"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-04-01
author:      "ぺーぺーSE"
image:       ""
tags:
- tag1
- tag2
categories:
- tech
draft:       true
---

Description

<!--more-->

## アプリケーションの管理

- AWS Serverless Application Repository
    - Lambda アプリケーションのリポジトリ
- AWS Serverless Application Model （ SAM ）
    - サーバーレスアプリケーションの定義とパッケージング・デプロイ
    - サーバーレスに最適化した Cloud Formation
    - インテグレーションテストでも活用
    - ローカル開発では AWS SAM CLI を利用
- Github -> CodeBuild -> SAM -> 3rd Party Tools でテスト -> プロダクションリリース（SAM）
    - 全体を CodePipeline で

# ペストプラクティス

## Lambda を速くするポイント

- いかに関数を効率よく実行するか
- コールドスタートに関わる部分を速くする
- 関数コードを速くする

## コールドスタート

Lambda の実行は以下を経て行われる。

1. 関数コードのダウンロード
2. 新しいコンテナの開始
3. runtime の起動
4. 関数コードの実行

1 〜 3 をコールドスタート、 4 をウォームスタートと呼ぶ。  
1 と 2 は AWS が最適化する部分、 3 と 4 はユーザが最適化する部分となる。  
1 と 2 はメトリクスでは確認できないが、一部は X-Ray （ Initiallization および差分 ）で確認可能。

## コールドスタートを速くする

- コンピューティングリソースを増やす
  - メモリ設定 -> メモリに比例して CPU 能力も上がる
  - コストを気にしてメモリを小さくしても、実行時間が短くなればコストは下がる -> コストはあまり変わらない可能性
- ランタイムを変える
  - 例えば JVM の起動は遅い -> 一度起動すると速いが
- パッケージサイズを小さくする（効果小）
  - ダウンロードを速く、依存関係を減らしロードを速く
  - ProGuard(Java)、UglifyJS(Node.js) のようなコード最適化ツールを使う
- VPC は必要でない限り利用しない（効果大）

## 効率的な関数コード

- コードの最適化
  - 各言語のベストプラクティス
- Fat でモノリシックな関数にしない
  - コード内ではオーケストレーションしない -> Lambda から Lambda を実行しない -> Step Function とか使う
- **ハンドラとコアロジックは分離させる**
  - コアロジック（＝ビジネスロジック）の単体テストが容易になる
- コンテナの再利用を有効化する
  - グローバルスコープを賢く活用する
    - AWS SDK や DB クライアントの初期化はハンドラの外側で行う
    - グローバルスコープはコールドスタートでしか実行されない
- 必要なデータのみロードする
  - DB アクセスの select 最適化など
- レジリエンスの向上
  - エラーハンドリング -> タイムアウトを適切に管理し、リトライ処理を実装する
  - Lambda のリトライポリシーを理解する -> 同期はリトライなし、非同期は 2 回リトライ、ストリームは期限が切れるまで
  - Dead Letter Queue を活用 -> 関数毎にできれば Queue も分けて設定する
- 複雑な依存関係を避ける
- 組み込まれた SDK は使用しない
  - Lambda の実行環境にはランタイムによって AWS SDK などが組み込み済み
  - ラインタイムは更新されるので、ユーザで SDK をパッケージングする
- 非同期実行を活用する
  - 同期実行は同時実行数の制限に引っかかりやすく、スケーラビリティの観点では非同期がおすすめ
  - 非同期はバーストも若干許容される
- 冪等性を確保する
  - Lambda で保証するのは「最低 1 回実行すること」、「 1 回だけ実行すること」ではない
  - DynamoDB を利用するなどして冪等性を担保する -> トリガーされた時のイベント ID を管理
  - S3 がイベントトリガーの場合は、処理のインプットとアウトプットのバケットを分ける
- Think Parallel
  - Lambda の中でループするのではなく、ループ回数分 Lambda を並列に起動・実行するなど
- Java の場合
  - POJO ではなくバイトストリームを使う
- ハードコーディングしない
  - コード、設定、データを分離する
  - 環境変数を利用 -> 特に認証情報は
  - AWS Systems Manager Parameter Store や AWS Secrets Manager の利用も検討
- AWS Systems Manager Parameter Store
  - 階層型で、パスワード、データベース文字列などのデータをパラメータ値として保存
  - 値は暗号化可能
  - Amazon SNS を用いた変更通知可能
  - AWS KMS、CloudWatch、CloudTrail と統合可能
  - 追加料金不要
  - `ssm_cache` ライブラリの利用を検討
- AWS Secrets Manager
  - DB 認証情報、パスワードなどの秘密情報を一元管理可能
  - ライフサイクルを通じて容易にローテーション、管理、取得可能
  - RDS、 RedShift、DocumentDBとの統合が組み込み済み
  - 有料、、、
- ストリーム型
  - Kinesis Data Streams、DynamoDB Streams
  - バッチサイズは Lambda 関数の実行あたりの最大レコード数
    - バッチサイズが大きいと同時実行数は低下する
    - バッチサイズに関わらず、 1 バッチで最大 6 MB までしか処理できない
  - 処理失敗するとブロックされるため、 Lambda は成功を返却するようにする
    - ログ出力や DLQ へ流して成功を返すようにする
  - 複数のコンシューマを用意するのは避ける
  - ファンアウトパターン
    - ストリームから読み取る Lambda を 1 つの Dispatcher として、そこから複数の Lambda 呼び出し・並行処理する
  - リトライや順序が重要でない場合は SNS の利用も考える
- Lambda から利用するデータベース
  - RDBMS はアンチパターン
    - コネクション数の問題：DB側の橙同時接続数を超えやすい
    - VPC コールドスタートの問題：通常のコールドスタートに比べて 10 秒程度の時間を必要とする
    - 実行数が少なく遅延が許容できる場合は問題ない
  - 選択肢
    - DynamoDB：コネクション数の問題がなく、 VPC 外リソースとしてアクセス可能、サービス停止無しでスケールアップ
    - RDBMS の場合は、、、
      - 反映は非同期にする -> ストリーム・キューを介して Lambda でデータ反映
      - 同時実行するを制限する -> RDBMS へアクセスする Lambda を限定してから
    - Amazon Aurora Serverless
      - サーバーレス用の Aurora **ではない** -> 現状は若干飛び道具？
- IP 固定はアンチパターン
  - コースドスタート問題、、、
- IAM ポリシーはきつく絞る
- Lambda で全部やろうとしない

# セキュリティ

- [AWS 責任共有モデル](https://aws.amazon.com/jp/compliance/shared-responsibility-model/)
  - Security **IN** the Cloud
  - Security **OF** the Cloud

## AWS Lambda のセキュリティ

AWS Lambda のサービスはコントロールプレーンとデータプレーンに分けられる

- コントロールプレーン
  - Lambda 関数管理 API を提供
  - AWS サービスとのインテクレーションを管理
- データプレーン
  - Invoke API のコントロール
  - Lambda 関数が Invoke されると実行環境を新たに割り当てる、もしくは既存の実行環境を選択する

### データプレーンと MicroVM -> 実行環境 と MicroVM

Lambda 関数が Invoke される **実行環境** は **MicroVM** 上で実行される。  
MicroVM は AWS アカウント毎に占有で割り当てられ、同一アカウント内の Lambda 関数は全てここで実行される。  
そしてこの Micro VM 上に Lambda 関数毎に実行環境が作成される。  
実行環境は異なる関数間で共有されることはなく、 MicroVM は異なる AWS アカウント間で共有されることはない。

### 実行環境の構成

各実行環境は以下のコピーを持っている

- 関数コード
- Lambda layer
- 組み込みもしくはカスタムラインタイム
- Amazon Linux 2 ベースの最小限ユーザーランド

実行環境は Linux カーネルベースのコンテナテクノロジで隔離され、他の環境に属するデータへのアクセスおよび変更は不可。

- cgroup
  - 実行環境ごとにに CPU 、メモリ、ディスク帯域、ネットワーク帯域へのリソースアクセスを制限
- namespace
  - プロセス ID 、ユーザ ID 、ネットワーク・インターフェースとその他のリソースをグルーピング
  - 各実行環境は専有の namaspace で実行される
- seccomp-bpf / セキュアコンピューティングモード
  - 実行環境内から利用されうる syscall を制限
- iptables/routing tables
  - 実行環境をお互い隔離
- chroot
  - ファイルシステムへの特定範囲でのアクセスを提供

### ペイロード

Lambda 関数の呼び出しタイプによってペイロード（ユーザから送信されるデータ）は異なる経路で処理される。

- 同期呼び出し（ RequestResponse ）
    - API Caller （ API Gatewary や SDK など）からロードバランサに渡され、その後 Lambda invoke service に渡される
    - Lambda invoke service はリクエストされた関数の実行環境を特定し、ペイロードをその実行環境へ渡す
    - インターネットごしのロードバランサへのトラフィックは TLS で保護される
    - Lambda サービスないのトラフィックは単一リージョンにおけるインターナル VPC を通る
- 非同期呼び出し（ Event ）
    - Amazon SQS にキューイング
    - 内部的な poller プロセスがメッセージを取得し Lambda invoke service へ渡す
    - SQS のキューは Lambda さーびすによって管理されていてユーザには見えない
    - このパス上のトラフィックは TLS で保護されているが、 SQS でのデータ保管に当たっては追加で何かしらの保護はしていない

### Audit

- Amazon CloudTrail
    - AWS アカウント全体の統制、コンプライアンス、オペレーション監査、リスク監査を実装可能
- AWS Config
    - Lambda 関数、ランタイム、タグ、ハンドラ名、コードサイズ、メモリ割り当て、タイムアウト設定、同時実行数の設定に対する設定変更や削除を追跡可能

### 関数コードのセキュリティ

- AWS アカウントをセキュアにする
    - ルートアカンウトを無効にする
    - 環境ごとに AWS アカウントごと分けてしまうのも有効
- AWS IAM のロールを最小限の権限で割り当てる
- 認証情報をハードコーディンスしない
    - 環境変数の利用、 KMS による暗号化、 AWS Systems Manager Parameter Store や AWS Secrets Manager を利用
- アプリケーション固有のバリデーションは適切な方法で実装する
- デプロイ前に依存ライブラリの脆弱性スキャンを実施（ CI/CD パイプラインへ組み込む）
    - OWASP 、 Snyk 、 Twistlock

# ユースケース・事例

- Amazon API Gateway -> Lambda -> DynamoDB
    - WebSocket への対応も可能
- AWS AppSync -> Lambda -> DynamoDB
- S3 -> Lambda -> S3 など
    - MapReduce ライクに並列実行も可能
- SQS -> Lambda -> ...
- Amazon Kinesis Data Stream -> Lambda -> ...
- Alaxa スキル
- AWS IoT Corea -> Lambda -> DynamoDB
- Amazon DynamoDB Streams -> Lambda -> ...
- CloudWatch ジョブ/CRON -> Lambda

上記組み合わせて利用することも可能。