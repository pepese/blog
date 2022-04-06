---
title:       "Goで文字列操作"
URL:         "golang-string"
subtitle:    ""
description: ""
keyword:     ""
date:        2019-02-11
author:      "ぺーぺーSE"
image:       ""
tags:
- go
categories:
- tech
---

golang の文字列操作をまとめる。

<!--more-->

# fmt パッケージ

標準出力なイメージだが、以下のような文字列を作成するメソッドがある。

- func Sprint(a ...interface{}) string
    - `Print` の標準出力を文字列で返す
- func Sprintf(format string, a ...interface{}) string
    - `Printf` の標準出力を文字列で返す
- func Sprintln(a ...interface{}) string
    - `Println` の標準出力を文字列で返す

なお、 format の初期は以下の通り。

|種別|フォーマット|説明|
|:---|:---|:---|
|汎用|%v|デフォルトフォーマットを適用した値|
|汎用|%+v|構造体を出力する際、+フラグを加えるとフィールド名が表示される|
|汎用|%#v|この値をGo言語の構文で表現する|
|汎用|%T|この値の型をGo言語の構文で表現する|
|論理値|%t|単語、trueまたはfalse|
|整数値|%b|基数2|
|整数値|%c|対応するUnicodeコードポイントによって表される文字|
|整数値|%d|基数10|
|整数値|%o|基数8|
|整数値|%x|基数16、10以上の数には小文字(a-f)を使用|
|整数値|%X|基数16、10以上の数には大文字(A-F)を使用|
|整数値|%U|ユニコードフォーマット: U+1234; "U+%x"と同じ。デフォルトは、4桁|
|浮動小数、複素数|%e|科学的記数法、例: -1234.456e+78|
|浮動小数、複素数|%E|科学的記数法、例: -1234.456E+78|
|浮動小数、複素数|%f|指数なしの小数、例: 123.456|
|浮動小数、複素数|%g|%e、%fのどちらか出力の短い方|
|浮動小数、複素数|%G|%E、%fのどちらか出力の短い方|
|文字列、バイトスライス|%s|文字列またはスライスそのまま|
|文字列、バイトスライス|%q|Go言語の構文に従いダブルクォート形式でエスケープ|
|文字列、バイトスライス|%x|各バイトを2文字の16進数文字列として|
|ポインタ|%p|16進数文字列、先頭に0x|

また、 `%` の後に以下を記述することでフォーマットを調整できる。

- `+`
    - 数値型のとき、常に符号を出力
- `-`
    - パッドのスペースを左側ではなく右側に出力(フィールドの左寄せ)
- `#`
    - フォーマットの切替: 8進数(%#o)のとき先頭に0、16進数(%#x)のとき先頭に0x、
    - 16進数(%#X)のとき先頭に0Xを出力。%p (%#p)のときは0xを出力しない。
    - %q (%#q)のとき、出力可能であれば生(シングルクォートされた)文字列を出力
- `' '`
    - (スペース) 数値で、符号が出力されなかったときに代わりにスペースを出力(% d)、
    - 文字列またはスライスを16進数で出力したときにバイト間にスペースを出力(% x, % X)
- `0`
    - パッドとしてスペースの代わりに0を出力
    - `04` とすると、 4 桁になるように 0 パディングしてくれる

# strings パッケージ

以下のようなメソッドがある。

- func Contains(s, substr string) bool
    - s 内に substr が含まれるか
- func Count(s, sep string) int
    - s 内の sep をカウント
- func Fields(s string) []string
    - 文字列 s をひとつ以上の連続したホワイトスペースで分割
- func FieldsFunc(s string, f func(rune) bool) []string
    - 関数 `f(c)` が true を返す( c は Unicode コードポイント)位置で文字列 s を分割
- func Join(a []string, sep string) string
    - 文字列スライス a の要素を sep で繋げる
- func Replace(s, old, new string, n int) string
    - 文字越 s が含む old を new で前から n 個置き換える（n=-1 は全て）
- func Split(s, sep string) []string
    - s を sep で分割する
- func ToLower(s string) string
    - 文字列を小文字に変換する
- func ToUpper(s string) string
    - 文字列を大文字に変換する
- func Trim(s string, cutset string) string
    - 文字列 s 内の cutset をトリムする

# strconv パッケージ

以下のようなメソッドがある。

- 文字列から各型へ変換する
    - func Atoi(s string) (int, error)
    - func ParseBool(str string) (bool, error)
    - func ParseFloat(s string, bitSize int) (float64, error)
    - func ParseInt(s string, base int, bitSize int) (i int64, err error)
    - func ParseUint(s string, base int, bitSize int) (uint64, error)
- 数値かた文字列へ変換する
    - func Itoa(i int) string

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