---
title:       "スクラム入門"
URL:         "agile-scrum-basics"
subtitle:    ""
description: "スクラムはアジャイル開発の方法論の内の 1 つです。この記事ではスクラムの入門として、ざっくり概要をご紹介します。"
keyword:     "スクラム, Scrum, アジャイル, 開発"
date:        2022-04-15
author:      "ぺーぺーSE"
image:       ""
tags:
- agile
- scrum
categories:
- tech
---

スクラムはアジャイル開発の方法論の内の 1 つです。  
この記事ではスクラムの入門として、ざっくり概要をご紹介します。

<!--more-->

# 概要

スクラムは、複雑な問題に対応する適応型のソリューションを通じて、⼈々、チーム、組織が価値を⽣み出すための軽量級フレームワークです。

本記事ではスクラムの全体像を詳細を省いてご紹介します。  
詳細まで学習したい方は [スクラムガイド](https://scrumguides.org/docs/scrumguide/v2020/2020-Scrum-Guide-Japanese.pdf) をご参照ください。

# 理論

スクラムは「経験主義」と「リーン思考」に基づいています。  
外部環境の変化によるあらゆるリスクに対応するために、反復的に実装を行います。  
この反復をスプリントと呼び、スプリントに含まれるイベントを通じて「透明性」「検査」「適応」を実現します。

## 透明性

創発的なプロセスや作業は、すべての人に見えている必要があります。  
スクラムにおける作成物は透明性があり、すべての人に認知されることによって検査することができます。  
透明性がない状態で検査を行なっても誤解・無駄を省くことはできません。

## 検査

スクラムの作成物とゴールに向けた進捗状況は、スプリントのイベントによって検査されます。  
これにより、望ましくない変化・問題・課題を検知することができます。  
そしてこれらの変化・問題・課題に対応することにより適応が可能となります。

## 適応

検査にて検知された変化・問題・課題に対応するためには、プロセスや作成物の構成要素を調整する必要があります。  
しかし、関係者に権限がなかったり自己管理できていない場合は調整が難しくなります。  
検査により学んだことに素早く適応する必要があります。

# ロール

スクラムチームは PO 、 SM 、 Dev ロールによって構成されます。  
PO 1 名、 SM 1 名、 Dev 数名の合計 10 名以下が望ましいチームサイズです。  
プロダクトゴールを達成するために、機能横断型で必要なすべてのスキルを備えている必要があります。

## PO: プロダクトオーナー

ビジネス、 UX 、 テクノロジなどの様々な観点からプロダクトの方向性を決め、その価値を最大化することに責任を持ちます。  
プロダクトの機能・特徴を定義し、それに基づいてブロダクトバックログを作成します。

- プロダクトゴールを策定し、明確化します
- プロダクトバックログ（以降、 PBL）を作成し、チームへ伝えます
- PBL の優先順位を決定します

## SM: スクラムマスター

スクラムチームのプロセスの遵守を促し、スクラムチームの生産性向上のための改善を行うことに責任を持ちます。  
SM は、チームにスクラムの理論とプラクティスを理解してもらえるよう活動・支援することによってその責任を果たします。

- 自己管理型のチーム・メンバになるようコーチします
- チームの活動を妨害する障害物を排除し、チームが作成物に集中できるよう支援します
- ボジティブで、生産的で、時間を遵守したスクラムイベントを進行します

SM は 様々な形でチームを支援します。

- PO が効果的にブロダクトバックログを作成・管理できるよう支援します
- 必要に応じてチームメンバとステークホルダーのコラボレーションを促進・調整し、障壁を取り除きます
- チームへのスクラム導⼊を指導・トレーニング・コーチします
- 組織にスクラムの実行方法を助言します
- 自身の経験によるアプローチ・プラクティスをチームやステークホルダーに理解・実践してもらいます

## Dev: デベロッパ

自ら考え行動し（自己管理型）、プロダクトの価値を作成物で具現化します。  
職能横断的なスキルが求められるため、幅広い技術知識を有する必要があります。  
未知の技術領域に対しても自発的に情報収集して問題解決を行います。  
以下の責任を持ちます。

- スプリントの計画（スプリントバックログ）を作成します
- 完成の定義を忠実に守り、スプリントゴールに向けて日々計画を適応させます
- 技術領域や作成物に対して専門家として責任を持ちます

# 作成物

主に 3 つの作成物があります。

## ブロダクトバックログ

優先順位づけされたプロダクトバックログアイテム（以降、 PBI）の一覧です。  
PBI はユーザーストーリーとも呼ばれます。  
スプリント開始前までに PO によって作成され、十分に詳細化され見積り可能な状態にする必要があります。  
また、リファインメントにより透明性を向上させます。

PBI は INVEST を満たすことにより、良いユーザーストーリーとなります。

- Independent（独立している）
  - PBIは、他のPBIに固有の依存関係がないように、自己完結型である必要があります
- Negotiable（交渉可能である）
  - PBIは明示的な契約ではなく、議論の余地を残す必要があります
- Valuable（価値がある）
  - PBIは、利害関係者に価値を提供する必要があります
- Estimatable（見積もり可能である）
  - PBIのサイズは常に見積もることができなければなりません
- Small（小さい）
  - PBIは、正確さのレベル内で計画/タスク/優先順位付けを行うことが不可能になるほど大きくすべきではありません
- Testable（テスト可能である）
  - PBIまたはその関連する説明は、テスト開発を可能にするために必要な情報を提供する必要があります

また、テンプレートとしては『＜who＞として、＜what＞を達成したい。それは＜why＞だからだ』という形式がよく用いられます。

## スプリントバックログ

Dev はスプリントゴールを達成するために作成する計画です。  
所謂 タスクであり、デイリースクラムで進捗を確認できる程度の詳細さが必要です。

## インクリメント

システム開発においてはアプリケーションなどの「動くソフトウェア」に該当します。  
スプリントレビューにて PO に提示し、完了条件を満たすことによってリリースが可能となります。

# イベント

スプリントでは以下のイベントを実行します。

|イベント|主催|タイムボックス/比率|1週間スプリント|2週間スプリント
|:---|:---|:---|:---|:---|
|スプリントプランニング 第一部        |PO|5%|2時間|4時間|
|スプリントプランニング 第二部        |SM|↑に含む|↑に含む|↑に含む|
|デイリースクラム                   |SM|-|15分/日|15分/日|
|スプリントレビュ                   |SM|2.5%|1時間|2時間|
|スプリントレトロスペクティブ(振り返り)|SM|1.875%(最低1時間)|1時間|1.5時間|
|バックログリファインメント           |PO|10%|4時間|8時間|

## スプリントプランニング 第一部

スプリントの最初に実施し、事前に PO が作成した PBL/PBI を共有します。  
PBI のチケット化、優先順位の設定、見積り（プランニングポーカーなどでストーリーポイントを見積る）を行います。  
スプリントのゴールと受入条件（実装機能の品質、ドキュメント有無、自動化実施有無など）を設定します。

## スプリントプランニング 第二部

PBI を詳細化します。  
PBI のタスク分割、チケット化、優先順位の設定、作業量の見積り（時間）を行います。  
作業量の見積りの結果、スプリントで消化する PBI を決定し、 PO に伝えます。

## デイリースクラム

毎日決まった時間に 15 分以内で以下を実施します。

- 前日のタスク実施状況
- 当日のタスク実施予定
- 課題

課題についてはその場で解決策の議論・検討を行わず、 SM が持ち帰り対応方法について検討します。  
その結果早期対応が必要な場合は優先度を加味してタスク・チケット化しメンバにアサインします。  
可能な限り次以降のスプリントで消化しましょう。  
また、チームで消化できない課題はエスカレーションします。

## スプリントレビュ

スプリント内に実装した「動くソフトウェア」を PO にデモンストレーションします。  
チームの成果物に対して PO がスプリントプランニングの受入条件に基づいて受入可否を判定します。

## スプリントレトロスペクティブ(振り返り)

チームの生産性や品質特性、残課題・残バグの状況や解決策を共有・議論します。  
KPT などを用いて、チーム遂行面での成功点・反省点を洗い出し、次スプリントでのアクションプランを作成・実施します。

## バックログリファインメント

次のスプリントで実装する可能性・優先度の高い PBI について確認します。  
実現性や見積りが曖昧なものを詳細化し優先度を設定します。  
1 スプリントに 1 回は実施しましょう。

# おすすめ書籍

<!-- ad link - amazon/rakuten books - agile scrum -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"SCRUM BOOT CAMP THE BOOK【増補改訂版】 スクラムチームではじめるアジャイル開発 [ 西村 直人 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3680\/9784798163680.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16284702\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"id":1,"u_tx":"Amazonで見る","u_bc":"#ff9900","u_url":"https:\/\/amzn.to\/3jESqtU","a_id":0,"p_id":0,"pl_id":0,"pc_id":0,"s_n":"custom_1","u_so":0},{"u_tx":"楽天ブックスで見る","u_bc":"#bf0000","u_url":"https:\/\/a.r10.to\/hMMehy","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":2,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16284702\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"TULoG","s":"s"});
</script>
<div id="msmaflink-TULoG">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>