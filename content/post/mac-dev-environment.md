---
title:       "Macで開発環境を作る"
URL:         "mac-dev-environment"
subtitle:    ""
description: "Macbookに開発環境を作成する際にやること。"
keyword:     "Mac, Homebrew, Git, SDKMAN, anyenv, asdf, Java, Maven"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- mac
- homebrew
- git
- sdkman
- anyenv
- asdf
- java
- maven
categories:
- tech
---

Mac で開発環境を作成する際に概ね実施することをまとめてみた。

<!--more-->

# OSの設定

## Dockの大きさを調整する

- 「りんごマーク」->「システム環境設定」->「Dock」
    - ここで好きなように設定（サイズは最小、拡大をチェックして最大、がオススメ）

## トラックパッドの設定

- 「りんごマーク」->「システム環境設定」->「トラックパッド」
    - ここで好きなように設定（「タップでクリック」を選択）

## ドラッグ＆ドロップの設定

- 「りんごマーク」->「システム環境設定」->「アクセシビリティ」->「マウスとトラックパッド」を選択
-  「トラックパッドオプション」-> 「ドラッグを有効にする」->「ドラッグロックあり」

これで２タップ目でちょっと動かしたらファイル・ウィンドウをホールドでき、もう１タップしたリリースできる。

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

App Store から Xcode をダウンロード・インストール。  
また、以下で Xcode の Command Line Tools をインストール。

```bash
$ xcode-select --install
```

## Homebrewをインスール

以下を参照。

- [Homebrew入門](https://blog.pepese.com/mac-homebrew-basics/)

## iTerm2をインストール

```bash
# インストール
$ brew install iterm2
# 確認
$ brew list | grep iterm2
iterm2
```

Macデフォルトのターミナルより便利。  
`Command + D` でウィンドウ分割できたりする。

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

Gitの使い方は以下を参照。

- [Git入門](https://blog.pepese.com/git-basics/)

## anyenvかasdfをインストール

プログラミング言語・コマンドラインツールのインストール・バージョン管理ができるようになる。  
以下を参照。

- [すべての\*\*envを管理するanyenv](https://blog.pepese.com/anyenv/)
- [マルチランタイムバージョン管理ツールasdf](https://blog.pepese.com/asdf-basics/)
    - こっちのがおすすめ

## SDKMAN をインストール

Java 系ツールのインストール・バージョン管理ができるようになる。  
anyenv・asdf がお好みでない方はこちら。  
以下を参照。

- [SDKMAN入門](https://blog.pepese.com/sdkman-basics/)
- [公式](http://sdkman.io/)

## Javaをインストール

asdf・SDKMAN が好みじゃないひとはこっち。

```bash
$ brew install java
```

インストールされたJavaの確認は以下。

```java
$ /usr/libexec/java_home -V
Matching Java Virtual Machines (1):
    11.0.1, x86_64:     "OpenJDK 11.0.1"        /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home

/Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
```

PATHが通ってなかったら `~/.bash_profile` に以下を加筆して `source ~/.bash_profile` 。

```bash
export JAVA_HOME=`/usr/libexec/java_home -v "11"`
```

`11` の部分を `1.8` にするとJava8を使用できる。（もちろんインストールしてから）  
消す時は以下。

```bash
$ rm -rf /Library/Java/JavaVirtualMachines/openjdk-11.0.1.jdk/Contents/Home
```

## Mavenをインストール

asdf・SDKMAN が好みじゃないひとはこっち。

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

以下を参照。

- [Visual Studio Code入門](https://blog.pepese.com/vscode-basics/)

## STS (Spring Tool Suite)

```bash
$ brew install --cask springtoolsuite
```

# その他ツール

## Rest Client

Mac 用の Rest Client ツール [Cocoa Rest Client](https://mmattozzi.github.io/cocoa-rest-client/)。

```bash
$ brew install cocoarestclient
```

## FileZilla

FTP、FTPS、SFTPのGUIクライアント。

```bash
$ brew install filezilla
```
