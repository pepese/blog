---
title:       "Go Modules入門"
URL:         "golang-modules-basics"
subtitle:    ""
description: "Go Modules は、依存関係管理ツールです。この記事では、Go Modules の概要と使い方を記載します。"
keyword:     "Go, Go言語, Go Modules, go.mod, go.sum"
date:        2022-04-09
author:      "ぺーぺーSE"
image:       ""
tags:
- golang
categories:
- tech
---

Go Modules は、依存関係管理ツールです。  
この記事では、Go Modules の概要と使い方を記載します。

<!--more-->

# 概要

**Go Modules** （旧名 vgo）は Go v1.12 から正式リリースされている依存関係管理ツールです。  
ここでは、その概要を記載します。

## Minimal Version Selection

Go Modulesは [Semantic Versioning](https://semver.org/) に基づいてモジュールのバージョン管理を行ないます。

## GOPATH mode と module-aware mode

Go Modulesは **GOPATH mode** と **module-aware mode** という２つのモードがあり、環境変数 `GO111MODULE` で切り替えることができます。

- `GO111MODULE=off` ： **GOPATH mode**
- `GO111MODULE=on` ： **module-aware mode**
- `GO111MODULE=auto` ： `$GOPATH/src` 配下では GOPATH mode 、それ以外では module-aware mode で動作します

GOPATH mode と module-aware mode のそれぞれの特徴は以下の通りです。

### GOPATH mode

`go get` コマンドで取得したモジュールは `$GOPATH/src` に保存されます。  
標準 pkg 以外のモジュールはすべてここで管理されることになります。  
`go get` コマンドで取得するモジュールは基本的に Github などのリポジトリで管理されており、`$GOPATH/src` 配下に `git clone` した形で保存されます。  
モジュールのバージョンは、 `git clone/pull` したタイミングのバージョン（ `go1` タグ・ブランチもしくは `master` ）や、 `git checkout` で変更した branches のバージョンになります。  

モジュールのバージョンコントロールが面倒な点が、 GOPATH mode の難点です。

### module-aware mode

`go get` コマンドで取得したモジュールは `$GOPATH/pkg/mod` に保存され、さらに、`go.mod` ファイルに記録され依存関係管理されます。  
標準 pkg 以外のモジュールはすべてここで管理されることになります。  
`go get` コマンドで取得するモジュールは基本的に Github などのリポジトリで管理されており、`$GOPATH/pkg/mod` 配下に **Semantic Versioning された単位** で保存されます。  
Semantic Versioning された単位は、 `go.mod` に記載されたモジュールのバージョンに対応します。  
`go.sum` ファイルは依存モジュールのチェックサムの管理に利用されます。

従来通り `$GOPATH` 配下でプロジェクトを作成・開発し且つ module-aware mode を利用したい場合には `GO111MODULE=on` とする必要があります。  
また、 `$GOPATH` 配下以外でプロジェクトを作成する場合であっても module-aware mode を利用すれば `$GOPATH/pkg/mod` 配下で依存モジュールが管理されるので、好きな場所で Go プロジェクトを作成することができます。

## `$GOPATH`

`$GOPATH` 配下の構造がわからないとイメージつかないかもしれませんので、記載しておきます。

```zsh
$GOPATH
├─bin/ # Goツール類の実行ファイルが格納されるディレクトリ
│
├─pkg/ # ビルドしたパッケージオブジェクトが格納されるディレクトリ
│  ├─darwin_amd64/
│  │ ├─github.com/
│  │ │  └─GitHubアカウント名
│  │ │    ├─`*.a`ファイル[^1]
│  │ │    └─GitHubレポジトリ名/`*.a`ファイル[^2]
│  │ └─pkg.in/
│  │   ├─パッケージ名/
│  │   └─`*.a`ファイル
│  │
│  └─mod/ # Go Modules で取得したモジュール類が格納されるディレクトリ
│    ├─cache/      # download したモジュール類のメタ情報や実態のzipなどのキャッシュ
│    ├─github.com/ # Github リポジトリから取得したモジュール
│    │ └─GitHubアカウント名
│    │    └─GitHubレポジトリ名@バージョン/ # この「@バージョン」部分でバージョンを識別して管理している
│    └─gopkg.in/   # gopkg.in リポジトリから取得したモジュール
│
└─src/ # GOPATH mode: パッケージごとのソースコードを配置するディレクトリ
  ├─gopkg.in/
  │  └─パッケージ名/
  │    └─LICENSEとか`*.go`とかREADMEとか
  └─github.com/
    ├─GitHubアカウント名
    │  └─GitHubレポジトリ名/
    │    └─LICENSEとか`*.go`とかREADMEとか
    │
    └─<あなたのGitHubユーザ名> # Go Modules OFF の場合
      └─GitHubレポジトリ名/ # プロジェクトディレクトリ（複数）
        ├─go.mod
        ├─go.sum
        ├─main.go
        └─その他、あなたが開発中のソフトウェアのコード
```

# 使い方

ここでは Go 1.16 以降前提に記載します。

## go.mod の作成

`go mod init` を使用して、作成するプロジェクトのリポジトリを指定します。  
Go で作成するプロジェクトは Github などのリポジトリ管理されることが前提で考えられているためです。

```bash
$ go mod init github.com/pepese/golang-gin-sample
```

なお、 GOPATH 配下で `go mod init` する場合、自明なのでリポジトリを省略することができます。

```bash
$ mkdir -p $GOPATH/src/github.com/<あなたのGithubアカウント名>
$ cd $GOPATH/src/github.com/<あなたのGithubアカウント名>
$ mkdir <golangプロジェクト> # プロジェクトディレクトリの作成
$ cd <golangプロジェクト>
$ export GO111MODULE=on
$ go mod init              # `GO111MODULE=on go mod init` のように一行でも
go: creating new go.mod: module github.com/<あなたのGithubアカウント名>/<golangプロジェクト>
```

`go mod init` が完了すると `go.mod` が作成されています。

## go.mod/go.sum の更新

Go 1.15 までは `go build` や `go test` などのコマンドで `go.mod/go.sum` の内容が勝手に更新されていましたが， 1.16 からは自動では更新されなくなっています。  
そのため、何らかの拡張機能を有したコードエディタ上の import で新しいモジュールを追加しても `go.mod/go.sum` に追加されない場合があります。  
さらに利用のないモジュールが残ったりした場合なども含めて、 `go.mod/go.sum` を最適化するために以下のコマンドを利用しましょう。

```bash
$ go mod tidy
```

なお、上記ではモジュールのバージョンは最新化されません。  
`go get` を使って `go.mod/go.sum` に記述されているモジュールのバージョンを変更しましょう。

```bash
$ go get golang.org/x/tools/gopls@v0.6.5
```

バージョン番号サフィックスが有効になっており、 `@latest` なども利用できます。

## go install/get

go install/get の違いは以下の通りです。

- バイナリのビルドとインストールのための `go install`
- go.mod 編集のための `go get`

ツールの導入は `go install` 、 `go.mod` の編集は `go get` というふうに使い分けましょう。

## go.mod

go.mod では以下のディレクティブが使用されます。

|ディレクティブ|例|説明|
|:---|:---|:---|
|module|module github.com/pepese/golang-gin-sample|モジュール名、 `go mod init` で指定したリポジトリ|
|go|go 1.17|有効な Go のバージョン|
|require|require other/thing v1.0.2|インポート・モジュール|
|exclude|exclude old/thing v1.2.3|除外モジュール|
|replace|replace bad/thing v1.4.5 => good/thing v1.4.5|モジュールの置換|
|retract|retract v1.0.5|撤回バージョン|

### go ディレクティブの更新

Go のバージョンを変更したい場合は以下のコマンドを実行してください。

```bash
$ go mod tidy -go=1.17
```

# おすすめ書籍

<!-- ad link - amazon/rakuten books - golang -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"改訂2版 みんなのGo言語 [ 松木雅幸、mattn、藤原俊一郎、中島大一、上田拓也、牧 大輔、鈴木 健太 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/7277\/9784297107277.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/15956516\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DHsTJX","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hwxl6y","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/15956516\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"nAQxh","s":"s"});
</script>
<div id="msmaflink-nAQxh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>