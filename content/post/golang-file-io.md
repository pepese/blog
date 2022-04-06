---
title:       "Goでファイル入出力"
URL:         "golang-file-io"
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

golang のファイル入出力についてまとめる。

<!--more-->

# fmt パッケージ

標準入出力の場合だが fmt パッケージが利用できる。

- http://golang.jp/pkg/fmt

# os パッケージ

読み込みの例。

```go
package main

import (
	"fmt"
	"os"
)

func main() {
	// `in.txt` には「hoge」とある
	file, _ := os.Open(`in.txt`) // *File, error
	defer file.Close()           // お約束の Close

	buf := make([]byte, 3) // 1 回の File.Read で読み込むバッファサイズ
	for {
		n, _ := file.Read(buf)    // int, error
		fmt.Print("n=", n, " : ") // 実際に読み込んだバイト数
		if n == 0 {
			fmt.Println("")
			break
		}
		fmt.Println(string(buf[:n]))
	}
}
// 結果
// n=3 : hog
// n=1 : e
// n=0 : 
```

`os.File` のメソッドには以下のようなものがある。

- func (f *File) Read(b []byte) (n int, err error)
- func (f *File) ReadAt(b []byte, off int64) (n int, err error)
    - ファイルの途中（ `off` byte ）から読み込み開始
- func (f *File) Write(b []byte) (n int, err error)
- func (f *File) WriteAt(b []byte, off int64) (n int, err error)
    - ファイルの途中（ `off` byte ）から書き込み開始
- func (f *File) WriteString(s string) (n int, err error)
- func (f *File) Seek(offset int64, whence int) (ret int64, err error)
- func (f *File) Close() error

上記のメソッドは `io` パッケージに `Reader` `Writer` `Closer` のようなインターフェースとして定義されている。  
書き込みはもう想像つくと思うから省略。  
`os.Create()` というメソッドがあるということだけ買いておこう。

> ちなみに標準入力の `os.Stdin` は `*io.File` 型であり、 `io.Reader` インターフェースを満たしている。

# io/ioutil パッケージ

その名の通り、 io 系のユーティル機能を提供する。  
`Close` しなくてよかったりして楽。  
以下のようなメソッドがある。

- func ReadAll(r io.Reader) ([]byte, error)
    - `io.Reader` から全部読む
- func ReadFile(filename string) ([]byte, error)
    - ファイルから全部読む
- func WriteFile(filename string, data []byte, perm os.FileMode) error
    - ファイルへ全部書く

# bufio パッケージ

`ioutil` はちと大胆なので、通常は行単位の読み書きやバッファリング機能をもつ `bufio` パッケージを使用する。

```go
package main

import (
	"bufio"
	"fmt"
	"os"
)

func main() {
	// 1234[改行]5678[改行]90
	f, _ := os.Open(`ins.txt`) // *File, error
	defer f.Close()

	sc := bufio.NewScanner(f)
	for i := 1; sc.Scan(); i++ { // Scan() は次の読み込み位置へ移動して bool （次読める、読めない）を返す
		line := sc.Text() // 1 行を string で取得
		fmt.Printf("%d行目: %s\n", i, line)
	}
}
```

`bufio.Scanner` は内部に `MaxScanTokenSize` という定数を持っており、 **64 KB を超える行を読み込めない** ので注意が必要。  
他には以下のメソッドがある。

- func (s *Scanner) Bytes() []byte
    - 1 行を `[]byte` で取得
- func (s *Scanner) Buffer(buf []byte, max int)
    - 1 行の読み込み上限サイズ（ `MaxScanTokenSize` ）を変更できる
- func (s *Scanner) Split(split SplitFunc)
    - デフォルトでは行単位の読み込み単位を変更できる
    - 行単位（ `bufio.ScanLines` : デフォルト）単語単位（ `bufio.ScanWords` ）、バイト単位（ `bufio.ScanBytes` ）、文字（ `rune` ）単位（ `bufio.ScanRunes` ）
    - また、 `func(data []byte, atEOF bool) (advance int, token []byte, err error)` を満たす関数であれば独自カスタマイズも可能

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