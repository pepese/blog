---
title:       "asdfで利用できるJavaのディストリビューション"
URL:         "java-asdf-distribution"
subtitle:    ""
description: "asdf は複数のプログラミング言語ランタイムおよびそのバージョンを管理することができるツールです。この記事では、 asdf で利用できる Java のディストリビューションついて記載します。"
keyword:     "Java, ディストリビューション, asdf"
date:        2022-04-23
author:      "ぺーぺーSE"
image:       ""
tags:
- java
- asdf
categories:
- tech
---

asdf は複数のプログラミング言語ランタイムおよびそのバージョンを管理することができるツールです。  
この記事では、 asdf で利用できる Java のディストリビューションついて記載します。

<!--more-->

# 前提知識

本編へ入る前に前提となる知識について記載します。

## asdf

以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## JDK

JDK は Java Development Kit の略で Java プログラミングする際に必要な以下のものを詰め合わせたものです。

- コンパイラ
- JRE
- 標準ライブラリ類、など

## JRE

JRE は Java Runtime Environment の略で Java の実行環境になります。  
Java のソースコードをコンパイルするとバイトコードが出力されます。  
そのバイトコードを実行することができる環境です。

## ディストリビューション

JDK は OSS のプロジェクトである [OpanJDK プロジェクト](https://openjdk.java.net/) にて開発されています。  
しかし、ここで提供されているのはソースコードであり、コンパイル済みのバイナリが提供されているわけではありません。  

この OpenJDK のソースコードをビルドするのは一般のエンジニアにとってはハードルが高いです。  
そこで、Java に力を入れている企業やコミュニティが独自の拡張機能を入れてビルド版として提供するものがディストリビューションです。  

なお、 JavaFX は OpenJDK には含まれていないので、 [JavaFX プロジェクト](https://openjfx.io/) から別途入手する必要があります。  
JavaFX にもまたディストリビューションが存在します。

# asdf で利用できる Java ディストリビューション

## AdoptOpenJDK

公式： https://adoptopenjdk.net/

コミュニティ主導でスタートしたプロジェクトで、複数の主要ベンダーにもサポートされています。  
独自サポートもあり、また、 IBM などによる有償サポートも提供されています。

AdoptOpenJDKでは、「HotSpot」と「OpenJ9」の 2 種類の JVM を選択することができます。  
HotSpot は OpenJDK コミュニティの JVM で、現在最も広く使用されている JVM であり、 Oracle の JDK で使用されています。  
OpenJ9 は Eclipse コミュニティーの JVM で、低メモリ使用量と高速起動用に設計されたエンタープライズクラスの JVM であり、 IBM の JDK で使用されています。

なお、AdoptOpenJDKは，2021年7月末で更新を終了しており、2021年8月以降は [Eclipse Adoptiumプロジェクト](https://adoptium.net/) および [IBM Developerサイト](https://developer.ibm.com/languages/java/semeru-runtimes/) から配布が継続されます。

asdf では以下のようなバリエーションで提供されています。

- HotSpot の JDK （例：adoptopenjdk-17.0.2+8）
- HotSpot の JRE （例：adoptopenjdk-jre-17.0.2+8）
- OpenJ9 の JDK （例：adoptopenjdk-openj9-16.0.1+9.openj9-0.26.0）
- OpenJ9 の JRE （例：adoptopenjdk-jre-openj9-16.0.1+9.openj9-0.26.0）
- OpenJ9 の JDK Large Heap版（例：adoptopenjdk-openj9-large_heap-15.0.2+7.openj9-0.24.0）
- OpenJ9 の JRE Large Heap版（例：adoptopenjdk-jre-openj9-large_heap-15.0.2+7.openj9-0.24.0）

## Temurin

公式： https://adoptium.net/

AdoptOpenJDKは，2021年7月末で更新を終了しました。  
2021年8月以降は、 [Eclipse Adoptium プロジェクト](https://adoptium.net/) および [IBM Developerサイト](https://developer.ibm.com/languages/java/semeru-runtimes/) から配布が継続されます。  
Temurin は前者の Eclipse Adoptium プロジェクト提供のディストリビューションです。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：temurin-18.0.0+36）
- JRE （例：temurin-jre-17.0.2+8）

## IBM Semeru Runtime

公式： https://developer.ibm.com/languages/java/semeru-runtimes/

AdoptOpenJDKは，2021年7月末で更新を終了しました。  
2021年8月以降は、 [Eclipse Adoptiumプロジェクト](https://adoptium.net/) および [IBM Developerサイト](https://developer.ibm.com/languages/java/semeru-runtimes/) から配布が継続されます。  
Semeru は後者の IBM 提供のディストリビューションです。

asdf では以下のようなバリエーションで提供されています。

- OpenJ9 の JDK （例：semeru-openj9-17.0.2+8_openj9-0.30.0）

## Amazon Corretto

公式： https://aws.amazon.com/jp/corretto/

AWS によって提供されている JDK です。  
AWS による無償の長期サポートが行われています。  
AWS 上にデプロイする Java アプリケーションの場合はこれでいいでしょう。

asdf では以下のようなバリエーションで提供されています。

- JKD （例：corretto-18.0.0.37.1）

## Liberica JDK

公式： https://bell-sw.com/

Bellsoft によって提供されている JDK です。  
Bellsoft によるサポートが行われています。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：liberica-18+37）
- JRE （例：liberica-jre-18+37）
- JDK FX （例：liberica-javafx-18+37）
- JRE FX （例：liberica-jre-javafx-18+37）
- JDK Lite版 （例：liberica-lite-18+37）

## Microsoft Build of OpenJDK

公式： https://www.microsoft.com/openjdk

Microsoft によって提供されている JDK です。  
Azure 上にデプロイする Java アプリケーションの場合はこれでいいでしょう。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：microsoft-17.35.1）

## SapMachine

公式： https://sap.github.io/SapMachine/

SAP によって提供されている JDK です。  
SAP によるサポートが行われています。  

asdf では以下のようなバリエーションで提供されています。

- JDK （例：sapmachine-18）
- JRE （例：sapmachine-jre-18）

## Azul Zulu

公式： https://www.azul.com/downloads/zulu/

Azul Systemsによって提供されている JDK です。  
無償のCommunity版と有償のEnterprise版（サポート付）があります。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：zulu-18.28.13）
- JRE （例：zulu-jre-18.28.13）
- JDK FX （例：zulu-javafx-18.28.13）
- JRE FX （例：zulu-jre-javafx-18.28.13）

## Oracle JDK

公式： https://www.oracle.com/java/technologies/downloads/

Oracle によって提供されている JDK です。  
Oracle によるサポートが行われています。  
なお、下記記事でも紹介されている通り、無償利用できるようになったようです。

- [オラクル、Oracle JDKを再び無料提供へ、本番環境でも利用可。昨日リリースのJava 17から](https://www.publickey1.jp/blog/21/oracle_jdkjava_17.html)

**【2022年4月23日追記】** 無償範囲があるようですので以下のツイートを参考にしてください。

<blockquote class="twitter-tweet"><p lang="ja" dir="ltr">Oracle JDKって「無償で商用利用」というと語弊があったような？🤔<br><br>(a) running the Program for Your own personal use or internal business operations<a href="https://t.co/8klY48tf2k">https://t.co/8klY48tf2k</a><br><br>社内的に使うの限定でしたっけ<a href="https://twitter.com/yamadamn?ref_src=twsrc%5Etfw">@yamadamn</a> の解説が欲しい</p>&mdash; なぎせ ゆうき (@nagise) <a href="https://twitter.com/nagise/status/1506414770244624384?ref_src=twsrc%5Etfw">March 22, 2022</a></blockquote> <script async src="https://platform.twitter.com/widgets.js" charset="utf-8"></script>

asdf では以下のようなバリエーションで提供されています。

- JDK （例：oracle-17.0.2）

## Open JDK 18

公式： https://jdk.java.net/18/

Oracle がビルドした本番環境での使用に対応した JDK 。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：openjdk-18）

## Kona

公式： https://openjdk.java.net/projects/kona/

Kona プロジェクトの目標は、モノのインターネット（IoT）ドメインで一般的に使用されるネットワークテクノロジーとプロトコル用のJavaAPIを定義して実装することです。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：kona-17.0.2.b1）

## GraalVM

公式： https://www.graalvm.org/

Java で実装された JIT コンパイラで、Graal を 搭載した VM です。

asdf では以下のようなバリエーションで提供されています。

- OpenJDK 11 をベースとした GraalVM （例：graalvm-22.0.0.2+java11）
- OpenJDK 17 をベースとした GraalVM （例：graalvm-22.0.0.2+java17）
- ？ （例：graalvm-19.2.1）

## Trava Open JDK

[Hotswap プロジェクト](https://github.com/HotswapProjects) にてメンテナンスされているディストリビューションです。  
dcevm は Java実行中に動的にコードを差し替えるためのJVMの拡張です。  
HotSwapAgentと組み合わせて、アプリケーションを再起動することなしにコードの変更を反映することができます。

asdf では以下のようなバリエーションで提供されています。

- JDK （例：trava-11.0.11+1）

# おすすめ書籍

<!-- ad link - amazon/rakuten books - java -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"新わかりやすいJava オブジェクト指向徹底解説 第2版 [ 川場隆 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/5007","p":["\/9784798065007_1_3.jpg","\/9784798065007_2.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16977843\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37mQD9Z","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hak9Ku","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16977843\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"Hu62r","s":"s"});
</script>
<div id="msmaflink-Hu62r">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>