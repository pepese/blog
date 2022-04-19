---
title:       "Redis入門"
URL:         "redis-basics"
subtitle:    ""
description: "Redisは永続化可能なインメモリデータベースで、オープンソースのBSDライセンスで公開されています。この記事ではRedisの概要から基本的な機能までをご紹介します。"
keyword:     ""
date:        2022-04-19
author:      "ぺーぺーSE"
image:       ""
tags:
- redis
categories:
- tech
---

Redisは永続化可能なインメモリデータベースで、オープンソースのBSDライセンスで公開されています。  
この記事ではRedisの概要から基本的な機能までをご紹介します。

<!--more-->

# 概要

Redis は Key-Value 形式でメモリ上にデータを保存する NoSQL データベースです。  
データをストレージに永続化する機能もあり、また、マスター・スレーブ型のレプリケーション機能もあります。  
また、単純な Key-Value ストアではなく、複雑な型のデータとそれに対するアトミックな操作もサポートしています。

# 環境設定

Mac でのローカル環境の設定について記載します。

## インストール・起動・停止

```bash
# インストール
$ brew install redis

# 起動（ `Ctrl-C` で停止）
$ redis-server

# brew での起動
$ brew services start redis

# brew でのステータス確認
$ brew services info redis

# brew での停止
$ brew services stop redis
```

Mac 以外のインストールについては [こちら](https://redis.io/docs/getting-started/installation/) を参照してください。

## アクセス方法

ここでは `redis-cli` を利用したアクセスについて紹介します。

```bash
# アクセス ※ローカル環境であればオプション不要
$ redis-cli
127.0.0.1:6379> ping # 入力待ち状態に対して ping Enter
PONG
```

# データ型とコマンド

Redis の Key はバイナリもサポートする文字列を利用できます。  
Value は、以下のデータ型をサポートします。

- 文字列/Strings
  - 最大 512MB のテキストまたはバイナリデータ
- リスト/Lists
  - 追加された順に並べられた文字列の集合
- セット/Sets
  - 順序なしの文字列の集合で他のセット型と交差・和集合・差集合演算が可能です
- ソートされたセット/Sorted Sets
  - 値ごとに並べられたセット
- ハッシュ/Hashes
  - フィールドと値のリストを保存するデータ構造
- ビット配列/Bitmaps
  - ビットレベルの演算を実行できるデータタイプ
- HyperLogLogs
  - データセット内の一意の項目を推定する確率的データ構造
- ストリーム/Streams
  - ログデータ構造メッセージキュー
- 地理空間/Geospatial
  - 経度/緯度ベースのマップ

以降、代表的なデータ型に関する `redis-cli` コマンドを説明します。

## 文字列/Strings

最も単純な Key-Value ストアとして利用できます。  
基本的な操作である `set` `get` `exists` `del` コマンドは以下の通りです。

```bash
# 登録
127.0.0.1:6379> set mykey somevalue
OK

# 参照
127.0.0.1:6379> get mykey
"somevalue"

# 存在確認
127.0.0.1:6379> exists mykey
(integer) 1

# 削除
127.0.0.1:6379> del mykey
(integer) 1
```

## リスト/Lists

`rpush` （右側から要素を追加）、 `lpush` （左側から要素を追加）、 `lrange` （左側から要素を参照）、 `rpop` （右側から要素を取り出し）などの操作が可能です。  
なお、`exists` `del` といった基本的なコマンドはリストにおいても利用可能です。

```bash
# リスト作成・右側から要素を追加
127.0.0.1:6379> rpush mylist A
(integer) 1
# 右側から要素を追加
127.0.0.1:6379> rpush mylist B
(integer) 2
127.0.0.1:6379> rpush mylist C D E # 複数指定可能
(integer) 5
# 参照
127.0.0.1:6379> lrange mylist 0 -1 # 開始・終了要素番号を指定
1) "A"
2) "B"
3) "C"
4) "D"
5) "E"
# 左側から要素を追加
127.0.0.1:6379> lpush mylist first
(integer) 6
127.0.0.1:6379> lrange mylist 0 -1
1) "first"
2) "A"
3) "B"
4) "C"
5) "D"
6) "E"
# 右側から要素を取り出し
127.0.0.1:6379> rpop mylist # 取り出した要素はリストから消える
"E"
127.0.0.1:6379> lrange mylist 0 -1
1) "first"
2) "A"
3) "B"
4) "C"
5) "D"
```

## セット/Sets

`sadd` （要素を追加）、 `smembers` （要素の参照）、 `sismember` （要素がセットに含まれるか）、 `spop` （要素をランダムに取り出し）などの操作が可能です。

```bash
# セット作成・要素を追加
127.0.0.1:6379> sadd myset 1 2 3 # 複数指定可能
(integer) 3
# 参照
127.0.0.1:6379> smembers myset
1) "1"
2) "2"
3) "3"
# 要素がセットに含まれるか確認
127.0.0.1:6379> sismember myset 2
(integer) 1
127.0.0.1:6379> sismember myset 4
(integer) 0
# 要素をランダムに取り出し
127.0.0.1:6379> spop myset
"3"
127.0.0.1:6379> smembers myset
1) "1"
2) "2"
```

その他のデータ型とそれに対する操作・コマンドについては [こちら](https://redis.io/docs/manual/data-types/data-types-tutorial/) を参照してください。

# 永続化

ここでいう永続化はストレージへのデータ保存を指しています。  
Redis には以下のような永続化オプションがあります。

- RDB（Redisデータベース）
  - 指定された間隔でデータセットのポイントインタイムスナップショットを実行します
  - デフォルトではストレージ上に `dump.rdb` というバイナリファイルで保存されます
  - 自動設定もしくは手動実行（ `save` や `bgsave` コマンド）が可能です
- AOF（Append Only File）
  - サーバーが受信したすべての書き込み操作をログに記録します
  - サーバーの起動時に再度再生され、元のデータセットを再構築します
- 永続性なし
  - ストレージにデータを保存せず、インメモリのみになります
- RDB + AOF
  - 同じインスタンスで AOF と RDB の両方を組み合わせることができます
  - この場合、Redisを再起動すると、 AOF が優先されることに注意してください

# データベース

Redis はストア内にいくつかデータベースを設けることができます。

```bash
127.0.0.1:6379> config get databases
1) "databases"
2) "16"
```

デフォルトでは 0 〜 15 に採番された 16 個のデータベースがあります。（ `dbid` といいます）  
`redis-cli` で接続直後の `127.0.0.1:6379>` の状態では 0 番目のデータベースを利用しています。  
データベースの選択には `select` コマンドを利用します。

```bash
127.0.0.1:6379> select 1
OK
127.0.0.1:6379[1]> 
```

`127.0.0.1:6379[1]>` の `[1]` というふうに何番目のデータベースを利用しているかが分かります。  
同一の Key で異なる Value を扱いたい場合などに利用しますが、 Redis Cluster は 0 しかサポートしていないなどの制約がありますので、基本的に 0 のみ利用していればいいと思います。（ [参考](https://redis.io/commands/select/) ）

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>