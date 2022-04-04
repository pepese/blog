---
title:       "Atomのインストールとパッケージの導入"
URL:         "atom-install-packages"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-05-11
author:      "ぺーぺーSE"
image:       ""
tags:
- atom
categories:
- tech
---

Githubが開発したエディタ[Atom](https://atom.io/)のインストールからパッケージの導入までをまとめる。

<!--more-->

# インストール（Mac）

- [公式](https://atom.io)へアクセス
- 「Download For Mac」をクリック
- ダウンロードした ファイルがzipであれば解凍する
- Macアプリ「Atom」が表示されるので、それを「アプリケーション」フォルダへドラッグ＆ドロップ
- 「アプリケーション」フォルダへ移動し、「Atom」を起動

brew caskが使える人は以下でもよい。

```bash
$ brew cask install atom
```

上記がなんのことかわからない人は以下参照。

- https://blog.pepese.com/mac-homebrew-basics/

# 起動方法

アイコンクリックでもいいがコマンドラインで起動できる。

```bash
$ atom file
```

プロジェクト（カレントディレクトリ）で起動したい場合は以下。

```bash
$ atom .
```

# パッケージ導入方法

- メニュータブの「Packages」->「Settings View」を選ぶと「Settings」タブが開く
    - 「Ctrl+,」（Win）、「Command+,」（Mac）でも開く
- 「Install」メニューを選択し、欲しいパッケージを検索して「Install」ボタンを押すS
- 設定が反映されない場合はAtomを再起動する

# アップデート

## Atom

メニューの「 Atom 」 -> 「 Restart and Install Update 」。

## パッケージ

メニューの「パッケージ」 -> 「 Settings View 」 -> 「 Updates 」。

## トラブルシューティング

アップデートできない人は以下を実行。

```bash
$ sudo chown -R $(whoami) /Applications/Atom.app/
```

# オススメのパッケージ

- japanese-menu
    - メニューバーとコンテキストメニューを日本語化
- japanese-wrap
    - 日本語を折り返してくれるパッケージ
    - 現在のAtomは日本語の折り返しがうまく機能しないため
- goto-defitinion
    - 「Ctrl+Alt+Enter」（Win）、「Option+Cmd+Enter」（Mac）でクラス、メソッドの実装へ飛べるようになる
- terminal-plus（ **platformio-ide-terminal** ）
    - ターミナル画面を追加できるパッケージ
        - 画面を切り替えてターミナル開くのが面倒になった人にオススメ
    - 下部の「+」をクリックするか、 `Ctrl + Shift + @` でターミナルが開く
    - しかしながら、2017/01/19時点では動かなかったため、terminal-plusをforkして作成された **platformio-ide-terminal** がいい
- markdown-preview-enhanced
    - マークダウン表示機能の拡張
    - Pandoc を導入（ `brew install pandoc` ）することで、 Tex 形式のマークダウンも表示可能になる

# ATOM IDE

## インストール

パッケージの導入から **atom-ide-ui** を導入する。  
その後、利用したい開発言語に合わせて以下のものを導入する。

- ide-typescript
- ide-flowtype
- ide-csharp
- ide-java (Java 8 runtime required)
- ide-php

[ここ](https://atom.io/packages/search?q=IDE)で言語対応のパッケージを検索できる。

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>