---
title:       "Visual Studio CodeでGo言語の開発環境を作成する"
URL:         "golang-vscode-environment"
subtitle:    ""
description: "Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。この記事ではGo開発環境の設定から使い方までを記載します。"
keyword:     "Go言語, golang, 開発環境, vscode, Visual Studio Code"
date:        2022-04-20
author:      "ぺーぺーSE"
image:       ""
tags:
- go
- vscode
categories:
- tech
---

Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。  
コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。  
この記事ではGo開発環境の設定から使い方までを記載します。

<!--more-->

# Goのインストール・設定

asdf を利用します。  
asdf については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/asdf-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">マルチランタイムバージョン管理ツールasdf | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">asdfはプログラミング言語やCLIのマルチランタイムバージョン管理ツール。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

```bash
$ xcode-select --install # Mac の場合はとりあえずやっておく
$ asdf plugin add golang
$ asdf list-all golang
...
1.17.8
$ asdf install golang 1.17.8
$ asdf global golang 1.17.8
$ go version
go version go1.17.8 darwin/amd64
```

## 環境変数の設定

Go 言語の環境変数には以下があります。

- `GOROOT`
    - go のバイナリのホームまでのパス
	- `go env GOROOT` で値確認
- `GOPATH`
    - `go env GOPATH` で値確認
    - go の各種資材が配置パスであってプロジェクトのパスでないことに注意
    - プロジェクトのパスは `$GOPATH/src/github.com/<Githubアカウント名>/<プロジェクト名>`
	    - ただし、後述する **GOPATH mode** の場合
- `GOOS`
    - コンパイルして作成するバイナリの対象 OS を指定する
- `GOARCH`
    - コンパイルして作成するバイナリの対象 CPU を指定する

お好みのプロファイル（ zsh の場合は `.zshrc` 、 bash の場合は `.bash_profile` など ）に以下を追記します。

```bash
export GOPATH=`go env GOPATH`
export PATH=$PATH:$GOPATH/bin
export GO111MODULE=on
```

個人的には不要ですが、 GOROOT を含める場合は以下です。

```bash
export GOROOT=`go env GOROOT`
export GOPATH=`go env GOPATH`
export PATH=$PATH:$GOROOT/bin:$GOPATH/bin
```

以下のコマンドでプロファイルの変更を反映させましょう。

```bash
$ exec $SHELL -l
```

また、 `$GOPATH` 以下のディレクトリ構造は以下のようになる。

## 依存関係管理ツール Go Modules

Go Modules については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/golang-modules-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Go Modules入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Go Modules は、依存関係管理ツールです。この記事では、Go Modules の概要と使い方を記載します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

## VS Code の設定

VS Code には Go の各種ツールと連携する拡張機能があり、 VS Code 内ターミナルから以下のようにコマンドラインツールを導入することにより自動で拡張機能インストールの案内をしてくれます。  
Go の拡張機能をインストールしてから、VSCode でコマンドパレット( `Cmd+Shift+P` )を開いて `GO: Install/Update tools` で検索した後、全チェックしてインストールしてもいいし、以下で 1 つずつ入れても大丈夫です。

- goimports
    - 過不足のimportの自動補完
	- `go get golang.org/x/tools/cmd/goimports`
- gocode
    - ヘルパー機能
	- `go get -u -v github.com/nsf/gocode`
- godef
    - 呼び出し関数へのジャンプなど
	- `go get -u -v github.com/rogpeppe/godef`
- gogetdoc
    - `go get -u -v github.com/zmb3/gogetdoc`
- golint
    - lint
	- `go get -u -v golang.org/x/lint/golint`
- go-outline
    - `go get -u -v github.com/lukehoban/go-outline`
- goreturns
    - `go get -u -v sourcegraph.com/sqs/goreturns`
- gorename
    - `go get -u -v golang.org/x/tools/cmd/gorename`
- gopkgs
    - `go get -u -v github.com/tpng/gopkgs`
- go-symbols
    - `go get -u -v github.com/newhook/go-symbols`
- guru
    - `go get -u -v golang.org/x/tools/cmd/guru`
- gotests
    - `go get -u -v github.com/cweill/gotests/...`

## .editorconfig

.editorconfig のおすすめの設定を記載します。

```
root = true

[*]
end_of_line = lf
insert_final_newline = true
trim_trailing_whitespace = false
charset = utf-8
indent_style = space
indent_size = 2

[{Makefile,go.mod,go.sum,*.go}]
indent_style = tab
indent_size = 4
trim_trailing_whitespace = true
```

## デバッグ環境作成

デバッガとして **Delve** を導入します。

- デバッガツール delve のインストール
    - `go get -u github.com/derekparker/delve/cmd/dlv`
- VSCodeにGo言語の拡張機能をインストール
    - `Rich Go language support for Visual Studio Code`

コードにブレークポイントを設定して、 VSCode の `Debug` から `Start Debugging` を実行します。（ `F5` でも大丈夫です）  
以下を実行可能です。

- 継続実行（Continue）
    - 次のブレークポイントに到達するまで処理を継続させる
- ステップオーバー（Step Over）
    - 見えているソースコードの次の行に移動する。 カーソル位置の関数の中は実行され、終了するところまで処理が進む
- ステップイン（Step Into）
    - 関数呼び出しの中に飛び込む。下のレイヤーに降り ていくときに使う
- ステップアウト（Step Out）
    - いま実行している関数が終了するところまで処理 を進める
- 再スタート（Restart）
    - 一度終了して再度実行を開始する
- 停止（Stop）
    - 一度終了する

例えば `fmt.Println()` をステップインして細かいところを見ていきましょう。  
`syscall.Write()` などでシステムコールされているのがわかります。（ Win だと別コード）  
今回はデバッガのステップインでコードを掘っていきましたが、カーソルがあたっている位置の関数で `Go to Definition` （ `F12` ）しても関数の定義に飛びます。  
また、カーソルがあたっている位置の関数や変数で `Find All Reference` （ `bashift+F12` ） すれば、使われている位置がリストされます。

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