---
title:       "Dockerfile入門"
URL:         "docker-dockerfile-basics"
subtitle:    ""
description: "Dockerfile は Docker イメージを作成する際の手順・命令を記したテキストファイルです。この記事では、 Dockerfile の基本的な書き方とマルチステージビルドなどのベストプラクティスについて記載します。"
keyword:     "Docker, Dockerfile, マルチステージビルド"
date:        2022-04-08
author:      "ぺーぺーSE"
image:       ""
tags:
- docker
categories:
- tech
---

Dockerfile は Docker イメージを作成する際の手順・命令を記したテキストファイルです。  
この記事では、 Dockerfile の基本的な書き方とマルチステージビルドなどのベストプラクティスについて記載します。

<!--more-->

# 構文

よく利用する構文・命令文を説明します。  
網羅性はなく、また、詳しく記載していないので注意してください。

## `FROM`

[Dockerfile リファレンスの FROM 命令](http://docs.docker.jp/engine/reference/builder.html#from)

```dockerfile
# USAGE
FROM [--platform=<プラットフォーム>] <イメージ名> [AS <名前>]
FROM [--platform=<プラットフォーム>] <イメージ名>[:<タグ>] [AS <名前>]
FROM [--platform=<プラットフォーム>] <イメージ名>[@<ダイジェスト>] [AS <名前>]

# 例
FROM alpine:3.15
FROM golang:1.18.0-alpine3.15 as BUILD
```

基となるイメージを指定します。  
[Alpine](https://hub.docker.com/_/alpine/) をお勧めします。

## `RUN`

[Dockerfile リファレンスの RUN 命令](http://docs.docker.jp/engine/reference/builder.html#run)

```dockerfile
# USAGE:シェル形式 - Linux は 「/bin/sh -c」 、 Windows は 「cmd /S /C」
RUN <コマンド>
# USAGE: 実行形式 - シェル形式ではない、つまり Linux は 「/bin/sh -c」 、 Windows は 「cmd /S /C」を使用しない
RUN ["実行ファイル", "パラメータ1", "パラメータ2"]
# USAGE:複数命令
RUN <コマンド> && <コマンド>

# 例
RUN apt-get update && apt-get install -y curl nginx
```

`docker build` 時に実行されるコマンドです。

## `CMD`

[Dockerfile リファレンスの CMD 命令](http://docs.docker.jp/engine/reference/builder.html#cmd)

```dockerfile
# USAGE:実行形式 - シェル形式ではない、つまり Linux は 「/bin/sh -c」 、 Windows は 「cmd /S /C」を使用しない
CMD ["実行ファイル","パラメータ1","パラメータ2"]
# USAGE:ENTRYPOINT 命令に対するデフォルトのパラメータとして扱う
CMD ["パラメータ1", "パラメータ2"]
# USAGE:シェル形式 - Linux は 「/bin/sh -c」 、 Windows は 「cmd /S /C」
# 実行形式のほうが望ましい
CMD コマンド パラメータ1 パラメータ2
```

**コンテナ実行時のコマンドを指定** するため利用します。（ `RUN` とは異なります）  
実行ファイルを含める・含めない場合もありますが、実行ファイルを含まない場合は `ENTRYPOINT` 命令の指定が必要です。  
`ENTRYPOINT` 命令に対するデフォルトの引数を `CMD` で指定する場合は、 `CMD` ・ `ENTRYPOINT` 命令の両方を指定する必要があります。

## `EXPOSE`

[Dockerfile リファレンスの EXPOSE 命令](http://docs.docker.jp/engine/reference/builder.html#expose)

```dockerfile
# USAGE
EXPOSE <ポート> [<ポート>/<プロトコル>...]
```

コンテナの Listen ポートを指定します。

## `ENV`

[Dockerfile リファレンスの ENV 命令](http://docs.docker.jp/engine/reference/builder.html#env)

```dockerfile
# USAGE
ENV <キー>=<値> ...
```

環境変数 `<キー>` に対し、値を `<値>` として設定します。

## `ADD` と `COPY`

[Dockerfile リファレンスの ADD 命令](http://docs.docker.jp/engine/reference/builder.html#add)  
[Dockerfile リファレンスの COPY 命令](http://docs.docker.jp/engine/reference/builder.html#copy)

```dockerfile
# ADD USAGE:パスに空白を含む場合は後者
ADD [--chown=<ユーザ>:<グループ>] <追加元>... <追加先>
ADD [--chown=<ユーザ>:<グループ>] ["<追加元>",... "<追加先>"]

## COPY USAGE:パスに空白を含む場合は後者
COPY [--chown=<ユーザ>:<グループ>] <コピー元>... <コピー先>
COPY [--chown=<ユーザ>:<グループ>] ["<コピー元>",... "<コピー先>"]
```

`COPY` のほうが望ましいです。  
`COPY` はローカルファイルをコンテナの中にコピーする基本機能のみですが、 `ADD` は複数機能（ローカル上での tar 展開や、リモート URL のサポート）を持つためです。

## `ENTRYPOINT`

[Dockerfile リファレンスの ENTRYPOINT 命令](http://docs.docker.jp/engine/reference/builder.html#entrypoint)

```dockerfile
# USAGE:実行形式 - こちらを推奨
ENTRYPOINT ["実行ファイル", "パラメータ1", "パラメータ2"]
# USAGE:シェル形式
ENTRYPOINT コマンド パラメータ1 パラメータ2
```

`ENTRYPOINT` のベストな使い方は、イメージにおけるメインコマンドの設定です。  
`CMD` ではなく `ENTRYPOINT` を利用しましょう。

## `VOLUME`

[Dockerfile リファレンスの VOLUME 命令](http://docs.docker.jp/engine/reference/builder.html#volume)

```dockerfile
# USAGE
VOLUME ["/data"]
```

指定した名前で マウントポイント を作成します。  
データベース・ストレージ領域、設定用ストレージ、Docker コンテナによって作成されるファイルやフォルダの公開のために利用します。

## `USER`

[Dockerfile リファレンスの USER 命令](http://docs.docker.jp/engine/reference/builder.html#user)

```dockerfile
# USAGE
USER <ユーザ>[:<グループ>]
USER <UID>[:<GID>]
```

`USER` を使えば非 `root` ユーザで実行できます。  
利用するには Dockerfile でユーザとグループ作成（例： `RUN groupadd -r postgres && useradd -r -g postgres postgres` ）しておく必要があります。

## `WORKDIR`

[Dockerfile リファレンスの WORKDIR 命令](http://docs.docker.jp/engine/reference/builder.html#workdir)

```dockerfile
# USAGE
WORKDIR /path/to/workdir
```

Dockerfile 内で以降に続く `RUN` 、 `CMD` 、 `ENTRYPOINT` 、 `COPY` 、 `ADD` 命令処理時のカレントディレクトリを指定します。  
WORKDIR が存在していなければ作成されます。

## `ARG`

[Dockerfile リファレンスの ARG 命令](http://docs.docker.jp/engine/reference/builder.html#arg)

```dockerfile
# USAGE
ARG <名前>[=<デフォルト値>]
```

`docker build` 時にユーザが渡せる変数を定義します。  
`docker biuld` コマンドで `--build-arg <変数名>=<値>` を指定します。

# ベストプラクティス

## イメージサイズを小さくする

Docker イメージの配布やプロセスの立ち上がり時間など、サイズが小さいほど効率があがります。  
また、不要なファイルが含まれるほどセキュリティインシデントのリスクも上がります。  
可能な限りイメージのサイズは最小にしましょう。

- 軽量コンテナ（ 例えば [Alpine](https://hub.docker.com/_/alpine/) ）を利用する
- 余計なファイルは含めない
- 不要なパッケージをインストールしない

## `.dockerignore` を使う

[.dockerignore ファイル](http://docs.docker.jp/engine/reference/builder.html#dockerignore-file) に記載されたディレクトリ・ファイルは `ADD` `COPY ` 実行時に除外されます。  
これを利用すれば複雑な `COPY` 命令を書かずとも Docker イメージに余計なファイルを含めずに済みます。

```
# コメント
*/temp*
*/*/temp*
temp?
```

|ルール|挙動|
|:---|:---|
| `# コメント` |無視されます。|
| `*/temp*` | root 以下のサブディレクトリで `temp` で始まるファイル・ディレクトリを除外します。|
| `*/*/temp*` | root から2階層以下のサブディレクトリで `temp` で始まるファイル・ディレクトリを除外します。|
| `temp?` | ルートディレクトリで `temp` と 1 文字が一致する名前のファイル・ディレクトリを除外します。|

## 1 コンテナ ＝ 1 プロセス

１つのコンテナの中で１つのプロセスだけ実行すべきです。  
複数種類のアプリケーションを実行したい場合、各々別コンテナで実行したほうが各々の事情で水平スケールできリソース効率もいいです。  
アプリケーション間に何らか関連があるが普通です。 API 連携しましょう。

## レイヤーキャッシュを活かす

Dockerfile の 1 つ 1 つ（1 行 1 行）の命令はレイヤー構造になっており、命令の実行結果を 1 層 1 層積み重ねています。  
レイヤーに変更がなければ Docker は以前の結果を再利用してくれます。  
これを **レイヤーキャッシュ** といいます。

レイヤーに変更があった場合、それ以降の命令は再実行されます。

- レイヤを作成するのは `RUN` 、 `COPY` 、 `ADD` 命令のみとなっています
- `COPY` ・ `ADD` はファイルのチェックサムを元に変更がないか確認されます
- `RUN` は単に実行するコマンド文字列を比較されます（ファイルが変更されたかは確認されません）

このレイヤーキャッシュを活かすために以下をおこないましょう。

- `COPY` ・ `ADD` の実行は、変更の少ないファイルを先に実行しましょう
  - 例） `go.mod` や `yarn.lock` などの依存ライブラリ管理ファイル
- 命令数を減らしましょう
  -  例） `RUN` コマンドが続く場合は `&&` などを利用して命令数を減らす
- 可読性を上げたい場合はバックスラッシュ（ `\` ）で改行しましょう

例えば、下記のように書くと、ソースコードを書き換えてもライブラリに変更がなければレイヤーキャッシュが利用されます。

```dockerfile
FROM golang:1.18.0-alpine3.15

WORKDIR /work

COPY go.mod go.sum .
RUN go mod download

COPY . .
RUN CGO_ENABLED=0 go build -o app .

CMD ["/work/app"]
```

## マルチステージビルド

**マルチステージビルド** は、アプリケーションのビルド用コンテナと実行用コンテナを分けることによって実行用コンテナを最小サイズにする方法です。  
アプリケーションの実行形式さえあれば余計なファイル（ソースコードなど）は不要です。  
また、ビルド用コンテナがあれば、ビルド環境を構築する必要もありません。  
Dockerfile では複数の `FROM` 命令を記載することができます。  
例を記載しておきます。

### Go のマルチステージビルド

```dockerfile
############
# Builder
############
FROM golang:1.18.0-alpine3.15 AS BUILD

WORKDIR /build

COPY go.mod go.sum .
RUN GO111MODULE=on go mod download

COPY . .
RUN CGO_ENABLED=0 GOOS=linux GOARCH=amd64 go build main.go

############
# Executer
############
FROM alpine:3.15.4

RUN apk add --no-cache tzdata
ENV TZ Asia/Tokyo

COPY --from=BUILD /build/main /usr/local/bin/main
EXPOSE 8080
ENTRYPOINT ["/usr/local/bin/main"]
```

### Java のマルチステージビルド

```dockerfile
############
# Builder
############
FROM maven:3.8.4-amazoncorretto-17 as BUILD
 
WORKDIR /build
COPY . .
 
RUN --mount=type=cache,target=/root/.m2 \
     mvn clean package
 
############
# Executer
############
FROM amazoncorretto:17.0.2-alpine3.15

RUN apk add --no-cache tzdata
ENV TZ Asia/Tokyo
 
COPY --from=BUILD /build/<project>/target/<project>-*.jar /home/java/app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/home/java/app.jar"]
```

# おすすめ書籍

<!-- ad link - amazon/rakuten books - docker -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Dockerコンテナ開発・環境構築の基本 （impress top gear　impress top gear） [ 市川　豊 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/1736\/9784295011736_1_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16742446\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3JfjoTm","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hakvUs","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16742446\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"FzcPh","s":"s"});
</script>
<div id="msmaflink-FzcPh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>