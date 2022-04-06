---
title:       "AWS WAFまとめ"
URL:         "aws-waf-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-03-23
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- waf
categories:
- tech
---

[AWS WAF](https://docs.aws.amazon.com/ja_jp/waf/latest/developerguide/waf-chapter.html) についてまとめる。

<!--more-->

# 基本

AWS WAF は、 **Web ACLs** 、 **Rules** 、 **Conditions** の 3 つの要素からなる。

- **Condistions** / 条件
- **Rules** / ルール
- **Web ACLs** / Web アクセスコントロールリスト

複数の Condistion を Rules にまとめ、複数の Rules を Web ACLs に登録するという包含関係になっている。

<img src="https://www.shadan-kun.com/blog/wp2/wp-content/uploads/2017/11/AWS-WAF1.png" />


Web ACLs を CloudFront 、 API Gateway 、 ALB に適用して利用する。

# Conditions / 条件

以下の条件を作成可能。

- Cross-site scripting / クロスサイトスクリプティング
- Geo match / 地域制限
- IP addresses / IP アドレス一致
- Size constraints / サイズ制約
- SQL injection / SQL インジェクション
- String and regex matching / 文字列と正規表現の一致

## Cross-site scripting / クロスサイトスクリプティング

悪意のあるスクリプトが含まれていないか検査する箇所を定義する。  
以下の組み合わせで検査箇所を定義する。

- Part of the request to filter on / 検査する場所の定義
  - Header / HTTP ヘッダ
  - HTTP method
  - Query string / クエリ文字列 : URL の `?` の後に続く文字列全て
  - Single query parameter (value only) / 単一クエリパラメータ (値のみ)
  - All query parameters (values only) / すべてのクエリパラメータ (値のみ)
  - URI /
  - Boby / リクエストボディ
- Transformation / 検査の前に行う変換を定義
  - None / 変換なし
  - Convert to lowercase / 小文字へ変換
  - HTML decode / エスケープ（ `&quot;` や `&#xhhhh;` など）のデコード
  - Normalize whitespace / `\f` `\t` `\n` `\r` `\v` の空白文字変換
  - Simplify command line / OS コマンドの場合の整形
  - URL decode / URL デコード、パーセントデコード

ここでは検査箇所を定義するだけで、検査内容・アルゴリズムについては定義しない。

## Geo match / 地域制限

どこの地域からのアクセスなのかを検査する。

- Location type : `Country` のみ
- Location : ロケールの指定

## IP addresses / IP アドレス一致

どの IP からのアクセスなのか検査する。  
そのまんまなので詳細割愛。

## Size constraints / サイズ制約

HTTP の各箇所のデータサイズを検査する。

- Part of the request to filter on / 検査する場所の定義
  - 「Cross-site scripting / クロスサイトスクリプティング」節の同項目に同じ
- Comparison operator
  - 「 Equals 」「 Not equal 」「 Greater than 」「 Greater than or equal 」「 Less than 」「 Less than or equal 」といった比較演算子を定義
- Size (Bytes) / データサイズの定義
- Transformation / 検査の前に行う変換を定義
  - 「Cross-site scripting / クロスサイトスクリプティング」節の同項目に同じ

## SQL injection / SQL インジェクション

悪意のある SQL が含まれていないか検査する箇所を定義する。  
設定内容は「Cross-site scripting / クロスサイトスクリプティング」節に同じ。

## String and regex matching / 文字列と正規表現の一致

「Cross-site scripting / クロスサイトスクリプティング」節の設定と同様の箇所に対して、文字列・正規表現によるマッチングの検査を行う。  
ここまでで雰囲気分かると思うので詳細省略。

- Type
  - 「 String match 」か「 Regex match 」か選ぶ
- Part of the request to filter on / 検査する場所の定義
  - 「Cross-site scripting / クロスサイトスクリプティング」節の同項目に同じ
- Match type
  - 「 Contains 」「 Exactly matches 」「 Starts with 」「 Ends with 」「　Contains words 」から選ぶ
- Transformation / 検査の前に行う変換を定義
  - 「Cross-site scripting / クロスサイトスクリプティング」節の同項目に同じ
- Value is base64-encoded
  - Base64 エンコードされたデータか否か
- Value to match // Regex patterns to match to request
  - 合致する文字列 // 合致する正規表現

# Rules / ルール

ルールは Condistions を束ねるもの。  
ホワイトリスト条件、ブラックリスト条件でまとめるとよい。  
以下の設定がる。

- Rule type / ルールのタイプ
  - Regular rule / 条件を指定するのみのルール
  - Rate-based rule / 条件を満たすアクセス 5 分間で指定数以上来たらアクセスを遮断

# Web ACLs

作成したルールを Web ACLs として適用する。

- If a request matches all of the conditions in a rule, take the corresponding action
  - 作成したルールを追加し、以下を選択する
    - Allow : ルールに合致するリクエストを許可する（ホワイトリスト）
    - Block : ルールに合致するリクエストを拒否する（ブラックリスト）
    - Count : ルールに合致したリクエストをカウント（テスト用）
- If a request doesn't match any rules, take the default action
  - Default action : ルールに合致しないリクエストの扱い（デフォルトでリクエストをどう扱うか）
    - Allow all requests that don't match any rules : 許可
    - Block all requests that don't match any rules : 拒否

# AWS Shield

AWS WAF を適用すると AWS Shield の 「 AWS Shield Standard 」がデフォルトで適用される。  
「AWS Shield Advanced」は有償で適用することができる。

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