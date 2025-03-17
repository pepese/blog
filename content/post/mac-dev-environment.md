---
title:       "Macで開発環境を作る"
URL:         "mac-dev-environment"
subtitle:    ""
description: "ITエンジニアの方でMacを開発環境で利用されている方は多いと思います。この記事では、Macで開発環境を作成する際に筆者が実施することをご紹介します。"
keyword:     "Mac, Homebrew, Git, SDKMAN, asdf, Java, Maven"
date:        2025-03-17
author:      "ぺーぺーSE"
image:       ""
tags:
- mac
- homebrew
- git
- sdkman
- asdf
- java
- maven
categories:
- tech
---

ITエンジニアの方でMacを開発環境で利用されている方は多いと思います。  
この記事では、Macで開発環境を作成する際に筆者が実施することをご紹介します。

<!--more-->

# OSの設定

本設定は macOS Sequoia で確認したものになります。

## Dockの大きさを調整する

- 「りんごマーク」->「システム設定...」->「デスクトップとDock」->「Dock」
    - ここで好きなように設定（サイズは最小、拡大をチェックして最大、が好み）

## トラックパッドの設定

- 「りんごマーク」->「システム設定...」->「アクセシビリティ」->「動作」->「ポインタコントロール」
-  「トラックパッドオプション」-> 「ドラッグにトラックパッドを使用」->ドラッグ方法を「ドラッグロックあり」

これで２タップ目でちょっと動かしたらファイル・ウィンドウをホールドでき、もう１タップしたリリースできる。

## デスクトップをクリックしたら壁紙が拡大されないようにする

- 「りんごマーク」->「システム設定...」->「デスクトップとDock」->「デスクトップステージマネージャ」
    - 「壁紙をクリックしてデスクトップを表示」にて「ステージマネージャ使用時のみ」を選択

## デスクトップの整理

- デスクトップで右クリック（二本指クリック）->「表示順序」->「名前」を選択

## ライブ変換オフ

タイピング中に勝手に変換される、あれ。

- デスクトップ画面右上の「A」とか「あ」をクリック
- 比較的上の方に「ライブ変換」とあるのでチェックがついていればそれをクリックして無効化

## Finderで隠しファイルを表示

```bash
$ defaults write com.apple.finder AppleShowAllFiles TRUE
$ killall Finder
```

# 開発環境作成

## Xcodeをインストール

App Store から Xcode をダウンロード・インストールします。  
また、以下のように Xcode の Command Line Tools をインストールします。

```bash
$ xcode-select --install
```

## Homebrewをインスール

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/mac-homebrew-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Homebrew入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">HomebrewはMacで利用できるソフトウェアパッケージマネージャ。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## iTerm2をインストール

```bash
# インストール
$ brew install iterm2
# 確認
$ brew list | grep iterm2
iterm2
```

Macデフォルトのターミナルより便利です。  
`Command + D` でウィンドウ分割できたりします。

## Gitをインストール

```bash
# インストール
$ brew install git
# 確認
$ brew list | grep git
git
# 確認
$ git --version
git version 2.35.1
```

Gitの使い方は以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/git-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Git入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Gitの環境構築およびGithubの設定・使い方を絡めて説明。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## asdfをインストール

プログラミング言語・コマンドラインツールのインストール・バージョン管理ができるようになります。  
以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## SDKMAN をインストール

Java 系ツールのインストール・バージョン管理ができるようになります。  
asdf がお好みでない方はこちらをご利用ください。  
以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/sdkman-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">SDKMAN入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ぺーぺーSEがプログラミング（Go、Node.js、Python）、クラウド（AWS、Google Cloud）、Web構築などの経験・学習記録・おすすめ書籍などの情報を残すサイト。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## Javaをインストール

asdf・SDKMAN がお好みじゃない方はこちらをご利用ください。

```bash
$ brew install java
```

インストールされたJavaの確認は以下です。

```java
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (1):
    11.0.1, x86_64:     "OpenJDK 11.0.1"        /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
```

PATHが通っていない場合 `~/.bash_profile` に以下を加筆して `source ~/.bash_profile` を実行してください。

```bash
export JAVA_HOME=`/usr/libexec/java_home -v "11"`
```

`11` の部分を `1.8` にするとJava8を使用できるようになります。（もちろんインストールしてからです）  
消す時は以下を実行します。

```bash
$ rm -rf /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
```

## Mavenをインストール

asdf・SDKMAN がお好みじゃない方はこちらを利用してください。

```bash
$ brew install maven
$ mvn -v
Apache Maven 3.6.0 (97c98ec64a1fdfee7767ce5ffb20918da4f719f3; 2018-10-25T03:41:47+09:00)
Maven home: /usr/local/Cellar/maven/3.6.0/libexec
Java version: 11.0.1, vendor: Oracle Corporation, runtime: /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
Default locale: ja_JP, platform encoding: UTF-8
OS name: "mac os x", version: "10.14.1", arch: "x86_64", family: "mac"
```

# エディタ・IDEの設定

## Visual Studio Code

以下を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/vscode-basics/" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Visual Studio Code入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Visual Studio Codeのインストールからおすすめの拡張機能までを説明。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## STS (Spring Tool Suite)

```bash
$ brew install --cask springtoolsuite
```

# その他ツール

## Rest Client

Mac 用の Rest Client ツール [Cocoa Rest Client](https://mmattozzi.github.io/cocoa-rest-client/) です。

```bash
$ brew install cocoarestclient
```

## FileZilla

FTP、FTPS、SFTPのGUIクライアントです。

```bash
$ brew install filezilla
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>