---
title:       "Visual Studio CodeでJavaの開発環境を作成する"
URL:         "java-vscode-environment"
subtitle:    ""
description: "Visual Studio Code (以降 VSCode) は多くのプログラミング言語に対応した強力なコードエディターです。この記事では、Java、Maven、VSCodeのインストール・設定・使い方について記載します。"
keyword:     "Java, vscode, Visual Studio Code, Maven, 開発環境"
date:        2022-04-08
author:      "ぺーぺーSE"
image:       ""
tags:
- java
- maven
- vscode
categories:
- tech
---

Visual Studio Code (以降 VSCode) は多くのプログラミング言語に対応した強力なコードエディターです。  
この記事では、Java、Maven、VSCodeのインストール・設定・使い方について記載します。

<!--more-->

# Javaのインストール

asdf でインストールします。  
asdf については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

ここでは Amazon ディストリビューションである Corretto を利用します。

```bash
$ asdf plugin add java
$ asdf install java corretto-17.0.2.8.1
$ asdf global java corretto-17.0.2.8.1
```

asdf でインストールした場合、 `JAVA_HOME` の設定には専用のスクリプトが用意されています。（参考： [JAVA_HOME](https://github.com/halcyon/asdf-java#java_home) ）  
シェルの種類に合わせて `.zshrc` などに以下を追記します。

```bash
# To set JAVA_HOME in your shell's initialization add the following:
. ~/.asdf/plugins/java/set-java-home.bash

# For zsh shell, instead use:
. ~/.asdf/plugins/java/set-java-home.zsh

# For fish shell, instead use:
. ~/.asdf/plugins/java/set-java-home.fish

# For xonsh shell, instead use:
source ~/.asdf/plugins/java/set-java-home.xsh
```

```bash
$ exec $SHELL -l
$ java --version
openjdk 17.0.2 2022-01-18 LTS
OpenJDK Runtime Environment Corretto-17.0.2.8.1 (build 17.0.2+8-LTS)
```

# Mavenのインストール

同じく asdf を利用します。

```bash
$ asdf plugin add maven
$ asdf install maven 3.8.4
$ asdf global maven 3.8.4
$ mvn -v
Apache Maven 3.8.4 (9b656c72d54e5bacbed989b64718c159fe39b537)
Maven home: /Users/xxxxx/.asdf/installs/maven/3.8.4
Java version: 17.0.2, vendor: Amazon.com Inc., runtime: /Users/xxxxx/.asdf/installs/java/corretto-17.0.2.8.1
Default locale: ja_JP, platform encoding: UTF-8
OS name: "mac os x", version: "12.1", arch: "x86_64", family: "mac"
```

# プロジェクトの作成

通常の Java プロジェクトを作成する場合は以下のコマンドで作成します。

```bash
$ mvn archetype:create -DgroupId=com.example -DartifactId=sample
```

SpringBoot のプロジェクトを作成する場合は [spring initialzr](https://start.spring.io/) を使用すると便利です。  
各種設定を入力した後に GENERATE すると SpringBoot の雛形プロジェクトをダウンロードできます。  
最低限 Spring Web を Dependencies として追加しておけば REST API アプリケーションを作成できます。  
作成したプロジェクトの実行は以下です。

```bash
$ mvn spring-boot:run
```

# VSCodeのインストールと設定

VSCode のインストールは以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/vscode-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Visual Studio Code入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Visual Studio Codeのインストールからおすすめの拡張機能までを説明。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

以下の拡張機能を入れます。

- Extension Pack for Java （ Microsoft 製）
- Maven for Java （ Microsoft 製）
- Debugger for Java （ Microsoft 製）
- Project Manager for Java （ Microsoft 製）
- Test Runner for Java （ Microsoft 製）
- Spring Initializr Java Suppoirt （ Microsoft 製）
- Spring Boot Dashboard （ Microsoft 製）
- Spring Boot Tools （ Pivotal 製）
- Spring Boot Extension Pack （ Pivotal 製）
- Lombok Annotations Support for VS Code

ショートカットは以下のようなものがありますが、 VSCode メニューの「移動」を見ればすぐ分かります。

- インポート：（ コードのエラーの箇所を選択してから） `Command + .`
- クラス定義に飛ぶ：（該当箇所を選択してから） `F12` （ `fn` 押しながら）
- 参照先へ飛ぶ：（該当箇所を選択してから） `Shift + F12`

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

<!-- ad link - amazon/rakuten books - vscode -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Visual Studio Code完全入門 Webクリエイター\u0026エンジニアの作業がはかどる新世代エディターの操り方 [ リブロワークス ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/3457","p":["\/9784295013457_1_2.jpg","\/9784295013457_2.jpg","\/9784295013457_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/17018592\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3x7jQ3u","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/h54zvN","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/17018592\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"UC6Nd","s":"s"});
</script>
<div id="msmaflink-UC6Nd">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>