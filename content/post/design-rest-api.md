---
title:       "REST API設計について整理してみた"
URL:         "design-rest-api"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-05-03
author:      "ぺーぺーSE"
image:       ""
tags:
- design
- rest
categories:
- tech
---

REST APIはWeb APIで最も利用されるAPI設計のうちの 1 つです。  
この記事では、REST APIを設計する際の方針や命名などについての概要をまとめます。

<!--more-->

# 基本方針

以下はリソース URL （↔︎ 非リソース URL ）・ HTTP メソッドについての基本的な設計方針です。

|処理の内容|HTTPメソッド|URL|プログラムメソッド|
|:---|:---|:---|:---|
|リソースデータを全て取得する|GET| `~/resources` | `ListResources` |
|idと一致するリソースを一件取得する            |GET          | `~/resources/{id}` | `GetResource` |
|リソースを一件登録する                      |POST         | `~/resources` | `CreateResource` |
|idと一致するリソースを一件更新する            |PUT          | `~/resources/{id}` | `UpdateResource` |
|idと一致するリソースを一件更新する(ただし提供されたフィールドのみ)|PATCH        | `~/resources/{id}` | `UpdateResource` |
|idと一致するリソースを一件削除する            |DELETE       | `~/resources/{id}` | `DeleteResource` |
|リソースの利用可能なメソッドとパラメータを返す|OPTIONS      | `~/resources` | - |

# 命名規則

命名は **統一することが大切** で、正しいものが 1 つに定まるとは限りません。  
プログラミング言語・ライブラリなどにより流儀がありますので、以下は一例として捉えてください。

- URL は **ケバブケース**
  - NG : `/systemOrders` 、 `/system_orders`
  - OK : `/system-orders`
- URL中の `resources` はリソースのコレクションを表し、 **複数形の名詞** で表現
  - NG : `GET /user` 、 `GET /User`
  - OK : `GET /users`
- URL中のパスパラメータ・リソース ID部分は **キャメルケース**
  - NG : `/system-orders/{order_id}` 、 `/system-orders/{OrderId}`
  - OK : `/system-orders/{orderId}`
  - ※ケバブケース（ `{order-id}` ）への統一でも大丈夫です
- URL はコレクションで始まり、識別子で終わる
  - NG : `GET /shops/{shopId}/category/{categoryId}/price`
  - OK : `GET /shops/{shopId}/` 、 `GET /category/{categoryId}`
- リソース URL に動詞を使わない
  - NG : `POST /updateuser/{userId}` 、 `GET /getusers`
  - OK : `PUT /user/{userId}`
- 非リソース URL は動詞を使う
  - OK : `POST /alerts/245743/resend`
  - リソースへの CRUD ではなく、特定のビジネスロジックを実行する場合です
- クエリパラメータは **スネークケース**
  - OK : `customer_number` 、 `order_id` 、 `billing_address`
- JSON のプロパティは **キャメルケース**
  - OK : `customerNumber` 、 `orderId` 、 `billingAddress`
  - ※スネークケース（ `customer_number` ）への統一でも大丈夫です
- 各 API エンドポイントに `/health` 、 `/version` 、 `/metrics` を実装する
  - `/health` : ヘルスチェック
  - `/version` : バージョン番号
  - `/metrics` : 平均応答時間などのさまざまな metrics を提供
  - `/debug` と `/status` もおすすめです
- リソース名にテーブル名を使わない
  - NG : `product_order`
  - OK : `product-orders`
- レスポンスにリソースの総数を含める

```javascript
{
    users: [
        ...
    ],
    total: 34 # これ
}
```

# ゴールデンルール

API 設計で迷ったときは、以下のルールを参考にしてください。

- フラットはネストより良い
- 単純は複雑より良い
- 文字列は数字より良い
- 一貫性はカスタマイズより良い

# クエリパラメータの使用

クエリパラメータを利用すると URL の複雑性が下がり、 API の柔軟性が上がります。  
以下を参考にクエリパラメータを含めた API 設計を行なってください。

- 検索条件を指定したい場合 -> SELECT 文の WHERE 句のように使用する
  - 例）`~/dogs?age=3` -> 3 歳の犬だけを取得する
- ある1つのリソースの一部の項目だけを取得したい場合（Partial response） -> 「 **fields** 」にカンマ区切りで指定
  - 例） `~/dogs/1234?field=name,color,location`
- あるリソースから件数を指定して取得したい場合（Pagination） -> 「 **limit** 」と「 **offset** 」で指定する
  - 「offset」番目から「limit」件取得する
  - 例）`~/dogs?offset=25&limit=50`
  - 全レコード件数を「metadata」としてレスポンスに含める

# リソース操作

計算・翻訳・変換など、リソースへの CRUD 以外の操作も存在します。  
その場合は名詞ではな **動詞** を使用しましょう。

- 例） `~/convert?from=EUR&to=CNY&amount=100`


この API はリソース URL の設計からはやや外れることになりますので、その特殊性について認識できるよう明記するようにしましょう。  
また、この API で取得できるデータはデータベースの値を直接返すのではなく、ロジックで計算して得た結果であるということもまた認識できるよう明記しましょう。

# コンテンツネゴシエーション

Restful API においてクライアントからサーバへレスポンスのフォーマットを要求することを **コンテンツネゴシエーション** といいます。  
クライアントからサーバへコンテンツのフォーマットを要求する方法として以下のようなものがあります。

- URLの拡張子による指定
  - 例）HTML形式の要求： `https://myserver/myapp/accounts/list.html`
  - 例）spreadsheet形式の要求： `https://myserver/myapp/accounts/list.xls`
- リクエスト（クエリ）パラメータによる指定
  - 例）spreadsheet形式の要求
    - `https://myserver/myapp/accounts/list?format=xls`
    - `https://myserver/myapp/accounts/list?mediaType=xls`
- Acceptヘッダによる指定
  - 例）JSON形式の要求： `Accept: application/json`

個人的には **Accept ヘッダ** による指定を推奨します。  
「Accept-Language」「Accept-Encoding」も併せて利用するとよいでしょう。

# API 設計のステップ

API 設計の大まかな流れについてご紹介します。

1. 対象となるデータを洗い出します
    - ER 図等を記載します
2. 洗い出したデータをリソースに分割します
    - ER 図の 1 エンティティを 1 リソースにしたり、結合したテーブルを 1 リソースにしたりします
3. リソースに URL を割りあてます
    - 先述の命名規則に従いましょう
    - URL が右に行くほど自然な階層構造・サブセットになっているか確認しましょう
    - 例）オーナー 5678 が飼っている犬を表す場合
        - `~/owners/5678/dogs`
        - `~/resources/{id}/resources/{id}` 以上に階層を深くしないようにしましょう
        - これ以上複雑にしたい場合はクエリパラメータの使用などを検討してください
4. リソースに対してインターフェースを割り当てます
    - HTTPメソッド（GET、POST、PUT、DELETE等）の選択
5. クライアントから受信するメディアタイプを 1 つ以上決めます
    - `application/json` 、 `application/xml` 、等
6. クライアントへ返信するメディアタイプを 1 つ以上決めます
    - `application/json` 、 `application/xml` 、等
7. 接続性（Connectedness）を高めます
    - リソースはハイパーメディアリンクによって他のリソースと接続することができます
    - クライアントは自ら URL を組み立てることは行わず、サーバから送られてくるハイパーメディアリンクを利用することができます
8. 正常系を考えます（適切なリクエストがあったとき）
    - ステータスコードを選択します
9. 例外条件を考えます（不適切なリクエストがあったとき）
    - ステータスコードを選択します
    - ステータスコードと例外をマッピングします

ステータスコードは概ね下記のものを検討すればよいでしょう。

|HTTP Status Code        |意味                                                           |発生し得る契機          |
|:---|:---|:---|
|200 OK                   |正常に処理が完了                                                |GET、PUT、DELETEの結果   |
|201 Created              |新規リソースが作成された                                        |POSTの結果               |
|204 No Content           |正常に完了したが、特に返却するBodyが無い                        |POST、DELETEの結果       |
|301 MovedPermanently     |アクセスしたリソースが別のURIに恒久的に移動した                 |リソースのURIが変わった時|
|                         |Locationヘッダに移動先のURIを付与する                           |                         |
|303 See Other            |別のURIにアクセスしてほしい                                     |アクセスURIを変えてほしい|
|304 Not Modified         |リソースの変更をしなかった                                      |POST、PUT、DELETEの結果  |
|307 Temporary Redirect   |一時的なリダイレクト                                            |閉塞時等                 |
|                         |Locationヘッダにリダイレクト先のURIを付与する                   |                         |
|400 Bad Request          |クライアントからのHTTPリクエストに誤りがありサーバで処理できない|                         |
|401 Unauthorized         |クライアントが認証されていない                                  |                         |
|403 Forbidden            |クライアントの権限不足                                          |                         |
|404 Not Found            |アクセスしたURIが存在しない                                     |                         |
|406 Not Acceptable       |リクエストのAcceptヘッダがサーバで受け入れられない              |コンテンツネゴシエーション失敗|
|415 Unsupported MediaTYpe|リクエストのContent-Typeヘッダがサーバで受け入れられない        |サーバ側でBodyを解釈できない|
|409 Conflict             |リソースの現状の状態の矛盾している                              |更新順序の前後等|
|500 Internal Server Error|サーバ内でエラーが発生した                                      |上記以外のサーバエラー|

ステータスコードに対応したレスポンス内容は以下のようになります。

|API処理の結果            |レスポンスStatus Code   |レスポンスBody                                        |
|:---|:---|:---|
|リソースの取得に成功      |200 OK                   |取得対象データ                                         |
|リソースの登録に成功      |201 Created              |追加したデータ（Locationヘッダには追加したデータのURL）|
|リソースの更新・削除に成功|200 OK                   |空                                                     |
|リソースが存在しない      |404 Not Found            |空                                                     |
|リクエストに検証エラー    |400 Bad Request          |エラー情報                                             |
|サーバ側でエラー          |500 Internal Server Error|エラー情報                                             |

# APIの表し方

その URL が API であることを表す方法には以下のようなものがあります。

1. サブドメインを置く
    - 例） `https://api.example.com/`
2. パスに置く
    - 例） `https://example.com/api/`
3. 表現しない

どのパターンも見るので、どれが良いとかは断言しづらいです。  
開発するサービス特性により選択してください。  
API をパスで表現していて、且つ、同じドメインでWebページを公開しているサービスの場合、 `https://example.com/web/` のようにするサービスも見かけます。

# API バージョンの表し方

API の仕様が変更して後方互換性の維持が難しくなる場合を想定して、 URL や HTTP ヘッダに API バージョン情報を付与する方法があります。

- URL にバージョン情報を付与する
  - 例） `http://example.com/api/2/` ：バージョン番号（整数）のみの付与
  - 例） `http://example.com/api/2.0/` ：バージョン番号（少数）のみの付与
  - 例） `http://example.com/api/v2/` ：「v」をつける（整数）
- クエリパラメータにバージョン情報を付与する
  - 例）`~/resources?v=2`
- HTTP ヘッダに付与する（ **メディアタイプバージョニング** ）
  - `Content-Type` ヘッダに `application/vnd.github.v3+json` のようにバージョン情報を付与してレスポンスする
  - クライアントは `Accept` ヘッダに `application/vnd.github.v3+json` のようにバージョン情報を付与してリクエストする
- バージョン情報を付与しない
  - 異なる API を新規に作成する

これまた正解は無い（分からない）ので、サービス特性などにより選択してください。

# その他、決める際に困りそうなネタ

ほぼメモです。スルーしてください。

- リソースの移動、コピー
- 複数レコードを選択して更新する API
  - 207 Multi-Statusで返したくなる、、、

# Web画面の基本方針

某フレームワークのスキャフォルドっぽいですが、 Web 画面の URL 設計方針についてもメモしておきます。

|処理の内容     |HTTPメソッド|URL                       |
|:---|:---|:---|
|一覧画面表示    |GET |~/web/resources            |
|詳細画面表示    |GET |~/web/resources/{id}       |
|追加Form表示   |GET |~/web/resources/new        |
|追加アクション  |POST|~/web/resources/new        |
|編集フォーム表示|GET |~/web/resources/{id}/edit  |
|編集アクション  |POST|~/web/resources/{id}/edit  |
|削除画面表示    |GET |~/web/resources/{id}/delete|
|削除アクション  |POST|~/web/resources/{id}/delete|

# 参考

- [Cloud APIs API 設計ガイド](https://cloud.google.com/apis/design/)
- [Zalando RESTful API と イベントスキーマのガイドライン](https://restful-api-guidelines-ja.netlify.app/)
  - Original: [Zalando RESTful API and Event Guidelines](https://opensource.zalando.com/restful-api-guidelines/)

# おすすめ書籍

<!-- ad link - amazon/rakuten books - rest api -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Web　APIの設計　ユーザブルでセキュア、効率的で進化可能なWeb　APIをデザインする際に知っておくべきこと","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/booxstore\/cabinet\/01034\/bk4798167010.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/booxstore\/bk-4798167010\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"id":1,"u_tx":"Amazonで見る","u_bc":"#ff9900","u_url":"https:\/\/amzn.to\/37c3sV0","a_id":0,"p_id":0,"pl_id":0,"pc_id":0,"s_n":"custom_1","u_so":0},{"u_tx":"楽天ブックスで見る","u_bc":"#bf0000","u_url":"https:\/\/a.r10.to\/hanTgw","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":2,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/booxstore\/bk-4798167010\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"wrEa9","s":"s"});
</script>
<div id="msmaflink-wrEa9">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>