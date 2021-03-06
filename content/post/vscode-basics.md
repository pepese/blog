---
title:       "Visual Studio Code入門"
URL:         "vscode-basics"
subtitle:    ""
description: "Visual Studio Codeのインストールからおすすめの拡張機能までを説明。"
keyword:     "Visual Studio Code, vscode"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- vscode
categories:
- tech
---

Microsoft が開発したエディタ[Visual Studio Code](https://code.visualstudio.com/)（以降、 VS Code ）のインストールから拡張機能の導入までをまとめる。

<!--more-->

# インストール（Mac）

```bash
$ brew install --cask visual-studio-code
```

# 起動方法

アイコンクリックでもいいがコマンドラインで起動できる。

```bash
$ code
```

プロジェクト（カレントディレクトリ）で起動したい場合は以下。

```bash
$ code .
```

# ショートカット・操作

ショートカットの一覧・設定は `Comannd + K -> Command + S` で開く。  
（筆者の場合は「右側をすべて削除」が `Ctrl + K` になっていなかったので登録した。）

- ユーザ設定
    - `Command + ,`
- Markdown Preview
    - `Command + Shift + V`
- 統合ターミナル
    - `Control + Shift + @`
- ワークスペースにプロジェクトを追加
    - 「ファイル」->「ワークスペースにフォルダに追加」

# コマンドパレット

`Command + Shift + P` を押すと VS Code の上部に **コマンドパレット** が開く。  
VS Code で実行できる各種コマンドには名前が付いていて、その名前をこのコマンドパレットに入力することでそれらを実行できる。

# 拡張機能（ Extensions ）

アクティビティーバー（左上に縦に並んでるアイコン）の一番下にあるアイコンをクリックするとサイドバーが拡張機能の画面になる。（ `Command + Shift + X` でも）  
以下のような種類の拡張機能が存在する。

- ［Debuggers］（デバッガー）
- ［Languages］（言語）
- ［Linters］（Lintツール）
- ［Snippets］（スニペット）
- ［Themes］（テーマ）
- ［Other］（その他）

検索欄を空にすると現在インストール済みの拡張機能の一覧が表示される。  
なお、拡張機能の反映には VS Code を再起動するか再読み込みボタンを押す。

# アップデート

## VS Code

「 Code 」 -> 「更新の確認」。

## 拡張機能

拡張機能画面の右上の「・・・」をクリックして「更新の確認」。

# オススメの拡張機能

## EditorConfig

以下のコマンドを実行して VSCode の [EditorConfig](https://editorconfig.org/) 拡張機能を有効にする。

```bash
$ code --install-extension EditorConfig.EditorConfig
```

その後 `.editorconfig` をプロジェクトルートへ作成する。（設定内容はお好みで）

```
root = true

[*]
end_of_line = lf
insert_final_newline = true
charset = utf-8

[*.{js,json}]
indent_style = space
indent_size = 2
```

## 視覚サポート

- Trailing Spaces
    - 改行部分の最後に入る半角スペースの強調、削除
- EvilInspector
    - 文章中の全角スペースを強調, 削除

## Markdown

- Auto-Open Markdown Preview
    - Markdown ファイルを開くときに自動でプレビューを開く
- Markdown+Math
    - `$$` 内に数式を書いて `Ctrl + Shift + .` と打てば、数式対応のプレビューが表示

## Git

- [Git Lens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)
    - 誰が・いつ編集したかをわかりやすく表示
- Git History
    - ツリー表示や差分表示など
- gi
    - [gitignore.io](https://github.com/joeblau/gitignore.io) から gitignore を追加
- gitignore
    - [github/gitignore.io](https://github.com/github/gitignore) から gitignore を追加

## マークアップ

- Auto Complete Tag
    - タグを自動で閉じて、開始・終了タグを変更したらもう片方のタグも自動で変更

## Javascript

- [Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost)
    - importしたファイルの容量を表示してくれる拡張機能
- [Path Intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense)
    - ファイルパスの自動入力
- [Quokka.js](https://marketplace.visualstudio.com/items?itemName=WallabyJs.quokka-vscode)
    - エラーになる箇所を実行前に評価してくれる拡張機能

## その他

- Shortcuts
    - VS Code 最下部にショートカットボタンを追加
- [Material Icon Theme](https://github.com/PKief/vscode-material-icon-theme)
    - ファイルアイコンをマテリアルデザインになる
- [Indent Rainbow](https://github.com/oderwat/vscode-indent-rainbow)
    - インデントレベルごとに背景色が付く（超見やすいw）
- [Bracket Pair Colorizer](https://github.com/CoenraadS/BracketPair)
    - 対応するカッコに縦横線のハイライトを表示（超見やすいw）
- [Base16 Themes](https://marketplace.visualstudio.com/items?itemName=AndrsDC.base16-themes)
    - 気に入った Color Theme
    - Dark Monokai がよい
- [Trailing Spaces](https://marketplace.visualstudio.com/items?itemName=shardulm94.trailing-spaces)
    - 全角や余計なスペースをハイライトしてくれる拡張機能

# おすすめ書籍

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