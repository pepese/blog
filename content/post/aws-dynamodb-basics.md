---
title:       "DyanamoDB入門"
URL:         "aws-dynamodb-basics"
subtitle:    ""
description: "DynamoDB は AWS が提供する NoSQL データベースです。この記事では、DynamoDB ざっくり理解できるよう概要と AWS CLI でのアクセスについて記載します。"
keyword:     "DynamoDB, AWS, AWS CLI"
date:        2022-04-12
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- dynamodb
- "aws cli"
categories:
- tech
---

DynamoDB は AWS が提供する NoSQL データベースです。  
この記事では、DynamoDB ざっくり理解できるよう概要と AWS CLI でのアクセスについて記載します。

<!--more-->

# DynamoDB の概要

DynamoDB は AWS が提供する Key-Value 型 NoSQL データベースのマネージドサービスです。  
スキーマレスであるため、テーブル作成時に Key 以外の設定は必要ありません。  
以下の順で説明していきます。

- Value
- Key
- テーブル
- パーティション
- セカンダリインデックス
- キャパシティユニット

## Value

DynamoDB の Value は **Item** （ **項目** ）と呼ばれ、 1 つ以上の **Attribute** （ **属性** ）を持ちます。  
Item は RDB でいうレコードに該当します。  
1 つの Attribute につき、以下の項目を指定できます。

- Attribute Name：属性名
- Attribute Type：属性の型、以下の型があります
  - **スカラー型**
    - **1つ** の値を持つことができ、 **数値** 、**文字列** 、 **バイナリ** 、 **ブール** 、 **null** を選択できます
  - **ドキュメント型**
    - **入れ子** の値を持つことができ、 **リスト** 、 **マップ** を選択できます
    - リスト要素に保存できるデータ型に制限はなく、全て同じ型である必要はありません
    - マップは JSON オブジェクトと同様です
  - **セット型**
    - **複数** の値を持つことができ、 **数値** 、**文字列** 、 **バイナリ** を選択できます
    - **セット内の全ての要素は同じ型** である必要があります
- Attribute Value：Attribute Type に沿った属性の値

DynamoDBのキーは、 Item の中の Attribute から選択することになります。

### ドキュメント型/リストの例

```
FavoriteThings: ["Cookies", "Coffee", 3.14159]
```

### ドキュメント型/マップの例

```
{
    Day: "Monday",
    UnreadEmails: 42,
    ItemsOnMyDesk: [
        "Coffee Cup",
        "Telephone",
        {
            Pens: { Quantity : 3},
            Pencils: { Quantity : 2},
            Erasers: { Quantity : 1}
        }
    ]
}
```

### セット型の例

```
["Black", "Green" ,"Red"]

[42.2, -19, 7.5, 3.14]

["U3Vubnk=", "UmFpbnk=", "U25vd3k="]
```

## Key

DynamoDB では Key を **プライマリーキー** と呼び、以下の2種類の構成から選択できます。

1. **パーティションキー** のみの単純キー
2. **パーティションキー** と **ソートキー** の複合キー

パーティションキー、ソートキー共に「 Value 」節で紹介した **Attribute と同じ定義** となります。  
パーティションキーは **Hash Key** 、 ソートキーは **Range Key** とも呼ばれることもあります。

## テーブル

テーブルは **テーブル名** と **プライマリーキー** （オプションで、ローカルセカンダリインデックス ）で定義することができます。

## パーティション

DynamoDB はテーブルが作成された際に自動でパーティションが作成され、また、自動で管理されます。  
1 つのパーティションには、 **スループット** と **データ容量が** が決まっており、それぞれ上限を超えたら自動的に新規にパーティションが作成されます。  
パーティションは **パーティションキー** と **ソートキー** から決定されます。  

DynamoDB はパーティションキーをハッシュ関数への入力し、その出力値に応じてパーティションを特定します。  
Item はソートキーの値でソートされ、データ量が 10GB を超える場合はソートキーによりパーティション分割されます。

## セカンダリインデックス

テーブルで 1 つ以上の[セカンダリインデックス](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/SecondaryIndexes.html)（単に、インデックスともいう）を作成できます。  
セカンダリインデックスは必須ではありませんが、利用することにより query/scan の速度を向上することができます。  

DynamoDB のインデックスには以下の 2 つあります。

- [グローバルセカンダリインデックス](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GSI.html) （ **Global Secondary Index** / **GSI** ）
  - テーブルと異なるパーティションキー（必須）とソートキー（オプション）を持つインデックス
  - テーブル作成後に変更 **可能**
  - キャパシティーユニットの設定が **必要**
  - [ベストプラクティス](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GuidelinesForGSI.html)
- [ローカルセカンダリインデックス](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/LSI.html) （ **Local Secondary Index** / **LSI** ）
  - テーブルと同じパーティションキー（必須）と、異なるソートキー（必須）を持つインデックス
  - テーブル作成後に変更 **不可能**
  - キャパシティーユニットは **テーブルのキャパシティーユニットと同じ**
  - [ベストプラクティス](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/GuidelinesForLSI.html)

テーブルあたり最大 5 個の GSI と 5 個の LSI を定義できます。  
全てのセカンダリインデックスは **完全に 1 つのテーブルに関連付け** られ、そこからデータを取得します。  
また、ソートキーの Attribute に条件を適用して **Item をフィルタリング** することができます。

## キャパシティユニット

DynamoDB では **キャパシティーユニット** という単位で、書き込みと読み込みのスループットキャパシティが決定されます。  
DyanamoDB のテーブル作成時には **読み込みキャパシティーユニット** と **書き込みキャパシティーユニット** を指定する必要があります。

- **強力な整合性のある** 読み込みキャパシティーユニット
  - 『1 キャパシティーユニット』＝『「最大 **4** KB の Item 1 つ」を「 1 秒間」に「 1 回」の読み込む』
    - 4 KB より大きい項目を読み込む場合は、追加の読み込みキャパシティーユニットを消費する
- **結果的に整合性のある** 読み込みキャパシティーユニット
  - 『1 キャパシティーユニット』＝『「最大 **4** KB の Item 1 つ」を「 1 秒間」に「 **2** 回」の読み込む』
    - 4 KB より大きい項目を読み込む場合は、追加の読み込みキャパシティーユニットを消費する
- 書き込みキャパシティーユニット
  - 『1 キャパシティーユニット』＝『「最大 **1** KB の Item 1 つ」を「 1 秒間」に「 1 回」の書き込む』
    - 1 KB より大きい項目を書き込む場合は、追加の書き込みキャパシティーユニットを消費する

例えば、 **強力な整合性のある** 読み込みキャパシティーユニットであれば「最大 **4** KB の Item 1 つ」なので、「 3.5 KB の Item 1 つ を 1 秒に 1 回読み込み」でも「 1 キャパシティーユニット」になります。  
また、「 3.5 KB の Item 1 つ を 1 秒に 2 回読み込み」であれば「 2 キャパシティユニット」、「 0.5 KB の Item 1 つ を 1 秒に 10 回読み込み」であれば「 10 キャパシティユニット」、「 2.1 KB の Item 5 つ を 1 秒に 3 回読み込み」であれば「 15 キャパシティユニット」となります。  

**結果的に整合性のある** 読み込みキャパシティーユニットであれば、 **強力な整合性のある** 読み込みキャパシティーユニットの **2倍** のキャパシティー、つまりキャパシティユニットの数字は **半分** となるのみで考え方は同じです。  
書き込みキャパシティーユニットであれば「最大 **1** KB の Item」部分異なるのみで考え方は同じです。  

さらに例を挙げます。  
「 20 KB の Item 1 つを 1 秒間に強力な整合性のある読み込み」を行う場合は「 5 キャパシティーユニット」（ 20 KB = 4 KB × **5** なので）。  
「 20 KB の Item 1 つを 1 秒間に結果的に整合性のある読み込み」を行う場合は「 2.5 キャパシティーユニット」（ 20 KB = 4 KB × 5 の **2倍** のキャパシティなのでキャパシティーユニットの数字は **半分** ）。  
「 40 KB の Item 1 つを 1 秒間に結果的に整合性のある読み込み」を行う場合は「 5 キャパシティーユニット」。  
「 5 KB の Item 1 つを 1 秒間に書き込む」場合は「 5 キャパシティーユニット」。

## プロビジョニングモード と オンデマンドモード

DynamoDB には、以下2つの読み込み/書き込みキャパシティーモードがあります。

- オンデマンドモード
  - 容量計画なしで 1 秒あたりに数千ものリクエストを処理できる柔軟なオプション
- プロビジョニングモード
  - キャパシティユニットを指定
  - Auto Scaling を使用すると、トラフィックの変更に応じて、テーブルのプロビジョンドキャパシティーを自動的に調整できます

トラフィックが予測できない場合は前者、予測できる場合は後者を利用しましょう。  
後者の方がコストを抑えることができます。

# DynamoDB の API

DynamoDB の API は大きく以下があります。

- コントロールプレーン
  - DynamoDB テーブルを作成、管理
- データプレーン
  - テーブルデータのCRUD
- DynamoDB ストリーム
  - テーブルのストリームを有効または無効にし、ストリームに含まれるデータ変更レコードにアクセス

以降、コントロールプレーン、データプレーンについて記載します。

## コントロールプレーン

コントロールプレーンには以下の API があります。

- **CreateTable**
  - テーブルを作成する
  - オプションで、1 つ以上のセカンダリインデックスを作成し、テーブルに対して DynamoDB ストリーム を有効にできる
- **DescribeTable**
  - プライマリキーのスキーマ、スループット設定、インデックス情報など、テーブルに関する情報を取得する
- **ListTables**
  - すべてのテーブル名のリストを取得する
- **UpdateTable**
  - テーブルまたはそのインデックスの設定を変更、テーブルの新しいインデックスを作成または削除、またはテーブルの DynamoDB ストリーム 設定を変更する
- **DeleteTable**
  - テーブルを削除する

## データプレーン

データプレーンには以下の API があります。

- データの作成
  - **PutItem**
    - テーブルに Item を 1つ書き込む
  - **BatchWriteItem**
    - テーブルに Item を25個書き込みむ
    - PutItem を複数回呼び出すよりも効率的
    - 1つ以上のテーブルを跨った書き込みも可能
- データの読み取り
  - **GetItem**
    - テーブルから Item を 1つ取得する
  - **BatchGetItem**
    - テーブルから Item を 100 個取得する
    - GetItem を複数回呼び出すよりも効率的
    - 1つ以上のテーブルを跨った読み込みも可能
  - **Query**
    - 指定したパーティションキーを持つ全ての Item を取得する
      - パーティションキーは、「テーブル（＝ローカルセカンダリインデックス）のパーティションキー」もしくは「グローバルセカンダリインデックスのパーティションキー」
    - オプションで、ソートキーおよび比較演算子を指定して、検索結果を絞り込める
      - ソートキーは、パーティションキーに対応するソートキーから選択する
    - Item 内の属性全体、もしくは指定した属性のみを取得できる
  - **Scan**
    - 指定したテーブルもしくは（グローバル／ローカル）セカンダリインデックスの全ての Item を取得する
    - オプションで、パーティションキー・ソートキーおよび比較演算子を指定して、検索結果を絞り込める
    - Item 内の属性全体、もしくは指定した属性のみを取得できる
    - **フィルタ式があるかどうかにかかわらず、同じ量の読み込みキャパシティーを消費する**
- データの更新
  - **UpdateItem**
    - テーブルの Item を 1 つ以上更新する
    - 条件付き更新が可能
    - オプションで、アトミックカウンタ（他の書き込みリクエストを妨害することなく、数値属性をインクリメントまたはデクリメント）も可能
- データの削除
  - **DeleteItem**
    - テーブルから Item を 1 つ削除する
  - **BatchWriteItem**
    - テーブルから Item を最大 25 個削除する
    - DeleteItem を複数回呼び出すよりも効率的
    - 1つ以上のテーブルを跨った削除も可能

# AWS CLI で DynamoDB へアクセス

[公式ドキュメント](http://docs.aws.amazon.com/cli/latest/reference/dynamodb/index.html)

## 環境設定

以下を参照ください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/aws-cli-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">AWS CLI入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">AWS CLI は AWS リソースの操作を行う Command Line Interface (CLI) ツールです。AWS のマネージメントコンソールをボチボチする代わりにコマンドを叩くイメージです。この記事では、AWS CLI のインストールと設定で役立つ情報（MFA・多要素認証など）を紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## テーブル定義

以降の AWS CLI によるオペレーションは以下のテーブル定義を前提とします。  
テーブル名は `test_table` です。

|属性名|属性型|プライマリーキー|
|:---|:---|:---|
|test_hash|スカラー型/文字列|HASH Key|
|test_range|スカラー型/文字列|RANGE Key|
|test_value|スカラー型/文字列||

## CreateTable

```bash
$ aws dynamodb create-table \
  --attribute-definitions '[{"AttributeName":"test_hash","AttributeType":"S"},{"AttributeName":"test_range","AttributeType":"S"}]' \
  --table-name 'test_table' \
  --key-schema '[{"AttributeName":"test_hash","KeyType":"HASH"},{"AttributeName":"test_range","KeyType":"RANGE"}]' \
  --provisioned-throughput '{"ReadCapacityUnits":5,"WriteCapacityUnits":5}'
```

## DeleteTable

```bash
$ aws dynamodb delete-table \
  --table-name 'test_table'
```

## ListTables

```bash
$ aws dynamodb list-tables
```

## PutItem

```bash
$ aws dynamodb put-item \
  --table-name 'test_table' \
  --item '{"test_hash":{"S":"xxxxx"},"test_range":{"S":"yyyyy"},"test_value":{"S":"zzzzz"}}'
```

## GetItem

```bash
$ aws dynamodb get-item \
  --table-name 'test_table' \
  --key '{"test_hash":{"S":"xxxxx"},"test_range":{"S":"yyyyy"}}'
```

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