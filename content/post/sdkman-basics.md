---
title:       "SDKMAN入門"
URL:         "sdkman-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-12-15
author:      "ぺーぺーSE"
image:       ""
tags:
- sdkman
- java
- maven
- gradle
categories:
- tech
---

Java 系の バージョン管理ツール [SDKMAN](http://sdkman.io/index.html) をさわってみる。

<!--more-->

# 概要

SDKMAN を導入することで、以下のツールのバージョン管理が可能となる。

- Ant
- AsciidoctorJ
- Ceylon
- CRaSH
- Gaiden
- Glide
- Gradle
- Grails
- Griffon
- Groovy
- GroovyServ
- Java
- JBake
- Kobalt
- Kotlin
- kscript
- Lazybones
- Leiningen
- Maven
- sbt
- Scala
- Spring Boot
- Sshoogr
- Vert.x

ここでは Java と Maven のみ記載する。

# 環境設定

## SDKMAN

```bash
$ curl -s "https://get.sdkman.io" | bash
$ source "$HOME/.sdkman/bin/sdkman-init.sh"
```

init を叩かないと `sdk` コマンドのパスが通らないため、 `~/.bash_profile` に以下をいれておく。

```
source "$HOME/.sdkman/bin/sdkman-init.sh"
```

# 各ツールの導入

USAGE は以下。  
ツール名を「 xxxx 」とする。

```bash
$ sdk help                     # help を表示
$ sdk version                  # SDKMAN 自体のバージョン
$ sdk selfupdate force         # SDKMAN 自体のバージョンアップ
$ sdk install xxxx             # xxxx をインストール
$ sdk install xxxx [version]   # xxxx を version 指定でインストール
$ sdk uninstall xxxx [version] # xxxx を version 指定でアンインストール
$ sdk list xxxx                # xxxx の version の一覧を表示
$ sdk use xxxx [version]       # xxxx の現ターミナルの version を指定
$ sdk default xxxx [version]   # xxxx のデフォルトの version を指定
$ sdk current xxxx             # xxxx の現在の version を表示
```

[公式](http://sdkman.io/usage.html)

## Java

```bash
$ sdk install java
$ sdk list java
==========================
Available Java Versions
==========================
     9.0.1-zulu
     9.0.1-oracle
     9.0.0-zulu
 > * 8u152-zulu
     8u151-oracle
     8u144-zulu
     8u131-zulu
     7u141-zulu
     6u65-apple
==========================
+ - local version
* - installed
> - currently in use
==========================
$ sdk install java 8u151-oracle
$ sdk default java 8u151-oracle
```

`-zulu` は OpenJDK 、 `-oracle` は Oracle Java を指す。  
`/usr/libexec/java_home` もちゃんと有効だった。

## Maven

```bash
$ sdk install maven 3.5.2
$ sdk list maven
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>