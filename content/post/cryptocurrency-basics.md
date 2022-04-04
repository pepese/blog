---
title:       "仮想通貨（暗号資産）入門"
URL:         "cryptocurrency-basics"
subtitle:    ""
description: "仮想通貨（暗号資産）についてブロックチェーンも交えて紹介します。"
keyword:     "仮想通貨, ブロックチェーン, 暗号資産, blockchain"
date:        2022-04-04
author:      "ぺーぺーSE"
image:       ""
tags:
- cryptocurrency
- blockchain
categories:
- tech
---

仮想通貨（暗号資産）についてブロックチェーンも交えて紹介する。

<!--more-->

# 仮想通貨とは？

**仮想通貨** は **暗号資産** とも呼ばれる。（正式名称は後者。英語では Cryptocurrency 。）  
電子データのみでやりとりされる通貨で、主にインターネット上での取引などに用いられる。  
まずは単なるデータだと思って差し支えない。

仮想通貨にはブロックチェーンというテクノロジが利用されている。  
正確には、最初に **ビットコイン** があって、通貨以外でも利用できる汎用的なデータベースとして切り出されたテクノロジがブロックチェーンだ。

# ブロックチェーンとは？

後述するのは一般的なブロックチェーンの性質を記載するものであって、特定のブロックチェーンに当てはまらないものもあることに注意。

- P2P ネットワーク
  - コンピュータ（ノード）同士が同じ目的で接続し、ネットワークを形成する方式
  - どのノードでも同じ処理ができるので、ネットワーク全体におけるノードの少数台が停止しても全体には影響せず処理を継続できる
- 分散型台帳
  - 同じ台帳データが複数ノードに記録される -> 同じデータが保存されたデータベースが、ネットワーク内の全てのノードにあると思って差し支えない
  - ブロックチェーン上で処理を行う際、複数のノードのデータを更新する必要があるため、処理完了までの遅延などにデメリットがある
- トレーサビリティと透明性
  - ブロックチェーンは最新の情報だけでなく、過去の取引履歴も全て記録されておりトレーサビリティおよび取引の透明性がある
- データの改竄が困難
  - ブロックチェーンを改竄するには膨大な計算量が必要であり、リソース的に困難な仕組みになっている
- 合意形成アルゴリズム/コンセンサスアルゴリズム
  - 承認者のノード間で合意形成をする仕組み
- スマートコントラクト
  - その名前の通り、コントラクト（契約）をスマートに行えるプロトコルのこと

# 合意形成アルゴリズム/コンセンサスアルゴリズム

以下のようなアルゴリズムがある。  
アルゴリズムが使用できる OSS やブラットフォームも挙げる。

- PoW （ Proof of Work ）
    - Bitcoin 、 Ethereum など
- PoS （ Proof of Stake ）
    - Ethereum 、 mijin など
- PBFT （ Practical Byzantine Fault Tolerance ）
    - Hyperledger Fabric など

## PoW （ Proof of Work ）

取引記録を P2P ネットワークで受信した参加者たちは受け取った取引記録の承認作業を行う。  
確率的に解答が困難な問題を一番早く解いた（ **Proof of Work** ）人にブロックの生成を許可し、その報酬としてインセンティブが得られる。  
また、通信の断絶状態などによって不整合が生じ、チェーンが分岐した場合は一番長いチェーンを正とすることでデータの一貫性を保証する。  
以下の問題が存在する。

- 51% 問題
  - 特定の人物・組織が所有するマイナーが過半数を占めた場合、結果を自由に操作できる
- ファイナリティの不確実性
  - チェーンが分岐し、短い方を正としていたノードが長い方へ切り替わった場合、自分の口座残高が急に変化したり、取引自体が無くなったりする
  - ビットコインでは、取引が確定しても 6 ブロック待たないと次の取引が実行できないといった制限で発生確率を下げている
- 性能限界
  - ネットワーク伝搬時間を無くすことは不可能で性能を上げることが難しく、リアルタイム性の高い業務には不向き
- ブロックチェーンの容量
  - 全てのノードが全てのブロック情報を保有する必要がある

## PoS （ Proof of Stake ）

基本的に PoW と変わらないが、貨幣量を多く所有している承認者が優先的にブロックを生成できるという特徴がある。  
これは、「大量の通過を所有している参加者は、その貨幣価値を守るためにシステムの信頼性を損なうことはしないであろう」という前提に基づく。

## PBFT （ Practical Byzantine Fault Tolerance ）

PoW や PoS が持つ問題であった「ファイナリティの不確実性」に対応した方式。  
ネットワークへの参加者をあらかじめ知っている前提で、 1 人がプライマリ（リーダ）となり、全参加者へ要求を送り、多数を占める値でブロックを確定させる。条件を満たさない取引は確定されない。  
この方式ではプライマリが処理の確定を行うので、ブロックチェーンが分岐しない。  
デメリットとしては、常に全ノードと意思疎通を行うため、ノードの追加とともに通信量が加速度的に増加するため、ネットワークとしては数十ノードが限界。

# ブロックチェーンの構造

- トランザクション
  - 1 回の取引
  - なお、 トランザクションには電子署名が付与される
- 電子署名
  - トランザクションで取引をする対象の本人確認と改竄防止
- ブロック
  - 一定時間溜まった、あるいは一定数のトランザクションをまとめた 1 つの塊、台帳の 1 ページ
  - 1 つのブロックにはトランザクションだけでなく後述のハッシュ、ナンスも含まれる
- ブロックチェーン
  - ブロックを時系列に単方向リストのように連ねた台帳
- ハッシュ
  - 前のブロックの取引内容を要約したもの
- ナンス（ nonce ）
  - ハッシュ計算で求められるパラメータ

## 改竄が困難な理由

ブロックチェーンでは最新のデータが保持されるだけでなく、「過去の取引の履歴」が過去のブロックに保持されている。  
仮に過去の取引・ブロックを改竄した場合、その次のブロックのハッシュが変更となる。  
すると、そのブロックのナンスもハッシュに基づいて再計算する必要があり、後続のブロック全てについてもハッシュ・ナンスを再計算しなければ整合性がとれなくなる。  
ブロックチェーンにおいて改竄を成立させるためには、正当に伝搬しているブロックよりも早くブロックを成立させるか、過去のブロックを全て作り直す必要があり、計算量的に改竄が困難な仕組みになっている。

## 電子署名による偽造防止

公開鍵暗号技術により電子署名を用いて相手との安全な取引を実現している。

# おすすめ書籍

<!-- amazon affiliate kindle blockchain --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B07XDM9WVP&linkId=0acfeeddd965a225ec4c8def5fecda33"></iframe>

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>