---
title:       "Goの変数・型・定数・リテラル"
URL:         "golang-var-type-const-literal"
subtitle:    ""
description: ""
keyword:     ""
date:        2019-02-08
author:      "ぺーぺーSE"
image:       ""
tags:
- go
categories:
- tech
---

golang の変数・型・定数・リテラルについてまとめる。

<!--more-->

# 変数

変数は `var 変数名 型` で定義する。

```go
var num int
```

右辺で型が推測できる場合は `変数名 := 値` で変数定義と初期化ができる。

```go
num := 10
```

右辺の型の予測は **リテラル** も参考のこと。

# 型

- 論理値型（ `bool` ）
- 数値型（ `int` `int8` `int16` `int32` `int64` `byte` `uint` `uint8` `uint16` `uint32` `uint64` ）
    - `int` はアーキテクチャにより異なり、32 ビットもしくは 64 ビットとなる
        - 32 ビットの場合の値域は「-1<<31 〜 1<<31 - 1（-2147483648 to 2147483647）」
    - `bype` は `uint8` の別名
    - `rune` は `int32` の別名
- 文字列型（ `string` ）
    - `[]byte` のように振る舞うが **値は不変**
- 配列型（ `[サイズ]型` ）
- スライス型（ `[]型` ）
- 構造体型（ `struct{}` ）
- ポインタ型（ `*型` ）
- 関数型（ `func(引数) 返り値型` ）
- インタフェース型（ `interface{}` ）
- マップ型（ `map[型]型` ）
- チャネル型（ `chan 型` `<-chan 型` `chan<- 型` ）

# 定数

`定数名 = 値` （ `:=` ではない）で定義でき、値を変更することはできない。

# リテラル

**リテラル** とは、プログラムのソースコードにおいて使用される、 **数値や文字列を直接に記述した定数** のこと。  
golang には以下のリテラルがある。

- 整数リテラル
    - 10 進数、 8 進数、 16 進数の整数リテラルがあり、 8 進数は頭に `0` を、 16 進数は頭に `0x` `0X` をつける
- 浮動小数点リテラル
    - 浮動小数点定数を表す小数表現で、整数部・小数点（ `.` ）・小数部・指数部（ `e+` `E+`）がある
        - 100 は `1e2` `1.e2` `1e+2` `1E2` 、 0.01 は `1e-2` `1.e-2` `1E-2` などと表現できる
- 虚数リテラル
    - 複素数定数の虚数部分の小数表現
    - 小数整数と小文字 `i` で構成される
- ルーンリテラル
    - Unicodeコードポイントに一致する整数値（ `int32` ）
    - シングルクォート（ `'` ）でくくって表現する
    - バックスラッシュを使用したマルチ文字シーケンスの各書式でエンコード（ `\'` ）することで、シングルクォート文字自体のUnicode値も表せる
- 文字列リテラル
    - 未加工文字列リテラル（ raw 文字列リテラル）はバッククォート、解釈有文字列リテラル（ interpreted 文字列リテラル）はダブルクォートで囲む

## ルーンリテラル

ちょっとややこしいかもしれないので補足。  
例えば 、リテラル `'a'` は文字 `a` 、Unicode 「 U+0061 」（ `'\u0061'` ） 、値 `0x61` を表す 1 バイトの値。

```go
var a rune
a = 'a'
fmt.Println(a) // 97
a = 0x61
fmt.Println(a) // 97
a = '\u0061'
fmt.Println(a) // 97
a = 97
fmt.Println(a)        // 97
fmt.Printf("%d\n", a) // 97
fmt.Printf("%c\n", a) // a
fmt.Printf("%U\n", a) // U+0061
```

## 文字列リテラル

ちょっとややこしいかもしれないので補足。  
**未加工文字列リテラル** はクオート内を全く解釈することがなく、そのまま出力する。  
一方、 **解釈有文字列リテラル** はダブルクオート内の **バックスラッシュによるエスケープを解釈** する。

```go
var a string
a = `\n`
fmt.Println(a) // \n
a = "\n"
fmt.Println(a) // 改行
a = "\u0061"
fmt.Println(a) // a
```

# その他Tips

## byte と rune

`byte` はあくまで 1 byte を意味し、 `rune` は **1 文字** を意味する。  
1 文字は数 byte で表されることもあるため、 `rune` が存在する。

```go
str := "あいうえお"
buf := []byte("あいうえお")
runes := []rune("あいうえお")

fmt.Println(str)           // あいうえお
fmt.Println(string(buf))   // あいうえお
fmt.Println(string(runes)) // あいうえお

for i := 0; i < len(str); i++ {
    b := str[i]    // byte 単位で取得される
    fmt.Println(b) // 227, 129, 130, 227, 129, 132, 227, 129, 134, 227, 129, 136, 227, 129, 138
    // 「あ」は 3 byte
}

for _, r := range str { // 1 文字（ rune ）単位で取得される
    fmt.Println(r) // 12354, 12356, 12358, 12360, 12362
}
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