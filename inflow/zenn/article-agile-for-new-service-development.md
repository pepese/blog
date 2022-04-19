---
title:       "新規サービス開発のためのアジャイル"
URL:         "article-agile-for-new-service-development"
subtitle:    ""
description: ""
keyword:     "アジャイル, agile, サービス開発"
date:        2022-04-16
author:      "ぺーぺーSE"
image:       ""
tags:
categories:
- article
draft:       true
---

Description

<!--more-->

# 新規サービス開発に必要なことは？

この記事では新規ビジネス発想のためのビジネスフレームワークといった内容には触れません。  
新規サービスのコアアイデアが固まっていて、自社・個人・スタートアップでシステム開発を開始する際の内容となっています。

新規サービス立ち上げて軌道に乗り始めるまでの間にシステム開発に必要なことはなんでしょうか。  
それは、柔軟性と素早さです。

# なぜアジャイル開発なのか？

よくウォーターフォールとアジャイルが比較されます。  
どちらもシステム構築やソフトウェア開発を伴うサービス開発を行う際の開発プロセス・方法論です。  
ウォーターフォール開発は上から下に各工程を後戻りしない前提で進めていく手法で、アジャイル開発は機能単位で小さくすばやく開発を繰り返していく手法です。

ウォーターフォールは「要件定義 -> 設計 -> プログラミング -> テスト -> リリース」という流れで行い、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">仕様が決まっていて開発途中に仕様変更が起きづらいシステムの開発</span> に適しています。  
アジャイルは、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">要件が定まっておらず仕様変更が起きやすいシステムの開発</span> に適しています。

昨今、アジャイル開発に注目が集まっていますが、それはなぜなのでしょうか？

現代社会はテクノロジーの進化によって、あらゆるものを取り巻く環境が複雑さを増し、将来の予測が困難な状況にあることから、「VUCA（ブーカ）時代」と呼ばれています。  
VUCAは、Volatility（変動性）・Uncertainty（不確実性）・Complexity（複雑性）・Ambiguity（曖昧性）の頭文字をとった造語です。  
このVUCA時代にはさまざまなことが起こります。  
例えば、様々な国の政治の混乱や国家間の争い、伝染病の広がりやそれに伴う経済の低迷など、今までスタンダードが一気に崩れるような <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">想定外の出来事が次々と発生します</span> 。  
このような事態に備えることは難しいでしょう。  
また、 Uber や Airbnb といった <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">業界の概念を覆すサービス</span> が突如登場し、既存の市場が消えそうになったり、思わぬ競合となったりします。

こんな時代に我々は何をすべきなのでしょうか？  
それは不測の事態に対応する様々な準備をするのではなく、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">不測の事態に素早く対応する能力</span> を身に付けることです。  
予測の難しいことに想像で対応するのは時間もお金も無駄というものです。

システム開発ではどのようにすればよいでしょうか。  
1 つの方法としては、アジャイル開発を行うことです。  
一般的にウォーターフォール開発は、よく吟味された多くの機能を一度に開発するため、大規模で長期のプロジェクトになります。  
アジャイル開発は、 MVP（Minimum Viable Product）と呼ばれる、ビジネスのコアコンセプトのみの必要最低限の機能を備えたプロダクトを開発・リリースして、市場の反応を見ながら追加開発をしてサービスを育てていく、小規模で短期のプロジェクトを反服的に行います。  

不測の事態に対して、ウォーターフォールとアジャイル、どちらのほうが対応力が高いでしょうか？

# まずは何をするのか？

前節で「アジャイルが万能」ともとれる内容を記載してしまいまいたが、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">決してそうではありません</span> 。  
<span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">仕様が決まっていて開発途中に仕様変更が起きづらいのであればウォーターフォールのほうが効率がいい</span> です。  
ですので、 MVP をリリースするまではウォーターフォール開発という選択も問題ありません。  
ただし、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">ビジネスのコアコンセプト部分＝必要最低限の機能</span> の開発だけです。  

本節のタイトルは「まずは何をするのか？」です。  
システム開発方法を <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">ウォーターフォールにするのかアジャイルにするのか選ぶことではありません</span> 。  
正直申し上げて、 MVP を早くリリースできるのであれば、どちらでもかまいません。  
ビジネスのコアコンセプトが定まっている前提ですが、まず最初にやることは <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">ビジネスモデルを数値で評価する方法を決定すること</span> です。  
MVP リリース後は、アジャイル開発で <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">仮説と検証</span> を繰り返すことになります。  
何らか評価する方法がなければ、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">仮説を検証できない</span> のです。

ここまで読んでいただいた方はお気づきかもしれませんが、アジャイルのことをあまり書いていません。  
そして以降もあまり書くつもりもありません。アジャイル開発自体について調査されている方、誠に申し訳ありません。  
新規サービス開発において大切なことは、 <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">アジャイルか否かではない</span> ということです。

アジャイルの代表的なプラクティスであるスクラムについては以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/agile-scrum-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">スクラム入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">スクラムはアジャイル開発の方法論の内の 1 つです。この記事ではスクラムの入門として、ざっくり概要をご紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# KPIの設定

KPI は、 Key Performance Indicator の略で、重要業績評価指標と訳されます。  
ここでは、 「目標を達成するための重要な評価指標」 ととらえてください。  
どの KPI を利用するのか・どう収集するのか、をまず検討・決定してください。  
何らか評価する指標がなければ、せっかく MVP をリリースしても <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">そのサービスが成功しているのか失敗しているのかわかりません</span> 。  
さらに <span style="font-weight: bold;background:linear-gradient(transparent 70%, #FFFF00 0%);">次にどのような機能を追加すればよいかもわかりません</span> 。  
直接的に売上がたつようなサービスであればまだよいのですが、何らか別にコアビジネス・サービスがあって、それを補助するようなサービスであればなお KPI が重要になります。  

例を挙げます。  
あなたは何らかのブログをやっています。基本的に Google などの Web 検索から読者が流入しています。  
読者を増やすために Twitter でブログを紹介することにしました。  
その後読者が増えたのか減ったのか、増えたとして Twitter の効果があったのか検索からの流入が増えたのか分かりません。  
どちらの方が流入が多いでしょう？今後どちらに力をいれましょう？止めるのか続けるのか？  
もし、 Twitter からの流入数、 Web 検索からの流入数を KPI として設定して収集・観測できていたらどうでしょうか。

# 最小人数で、クラウド人材必須

なにかシステムを開発するには IT 人材が必要です。  
どのような人材が必要でしょうか？