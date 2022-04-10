---
title:       "Go言語のデザインパターン"
URL:         "golang-design-pattern"
subtitle:    ""
description: "Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。また、デザインパターンとは、様々なプログラムで再利用できる汎用的な設計パターンのことです。この記事では一般的なデザインパターンではなく、Go言語の言語仕様に沿った内容をご紹介します。"
keyword:     ""
date:        2022-04-10
author:      "ぺーぺーSE"
image:       ""
tags:
- golang
categories:
- tech
---

Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。  
コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。  
また、デザインパターンとは、様々なプログラムで再利用できる汎用的な設計パターンのことです。  
この記事では一般的なデザインパターンではなく、Go言語らしい書き方やパターンについて紹介します。

<!--more-->

Go 言語の入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/golang-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Go言語入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Go言語はGoogleが開発・公開しているプログラミング言語の 1 つです。コード記述の簡潔さ・理解しやすさと、プログラム実行速度やリソース効率の両立を目指しています。この記事ではGo開発環境の設定から使い方までを記載します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# Go らしいコード

[参考](https://github.com/golang/go/wiki/CodeReviewComments)（ [日本語](https://gist.github.com/knsh14/0507b98c6b62959011ba9e4c310cd15d) ）

## 静的解析

|ツール|機能|
|:---|:---|
|gofmt, goimports|フォーマッタ|
|go vet, golint|チェッカ, リンタ|
|gocode|コード補完|
|errcheck|エラー処理チェッカ|

VS Code などのエディタ・IDE を利用する場合は保存時などに勝手に実行されるものが多いが、以下のコマンドの実行は意識しておいたほうがよいでしょう。

- `go fmt` （ = `gofmt -w -l` ）
    - VS Code ではコード保存時に勝手に実行してくれます
- `go vet ./...`
- `golint ./...`
    - これは VS Code が教えてくれたりもしますが、コマンド叩くと一覧表示されるのでコマンドを併用してもいいでしょう
- `errcheck ./...`
    - `go get -u github.com/kisielk/errcheck`

## コメント

コメントは、記述されているものの名​​前で始まり、ピリオドで終わる必要があります。

```go
// Request represents a request to run a command.
type Request struct { ...

// Encode writes the JSON encoding of req to w.
func Encode(w io.Writer, req *Request) { ...
```

## Context

一連オンライントランザクション処理の流れなどで保持しておく必要のある値をコンテキストで管理します。（セキュリティ資格情報、トレース情報、期限、キャンセルのシグナルなど）  
Contextを利用するほとんどの関数で一番最初の引数に指定して引き回します。

```go
func F(ctx context.Context, /* other arguments */) {}
```

## スライス

```go
var t []string  // Good

t := []string{} // Bad
```

前者は `nil` スライスを返し、後者は非 `nil` スライスを返却します。  
どちらも `cap` , `len`ともにゼロですが、前者が優先スタイルです。  
しかしJSONオブジェクトをエンコードするときなど理由がある場合は後者でも大丈夫です。

## panic は使わない

通常のエラー処理に `painc` を使用せず、エラーと複数の戻り値を使用しましょう。

## エラーの文字列

エラー文字列は、通常、他のコンテキストに従って出力されるため、大文字にしません。  
ただし、固有名詞や頭文字で始まる場合は大文字です。

```go
// Bad
fmt.Errorf("Something bad")
// 通常はこのようにフォーマットされて出力される
fmt.Errorf("something bad")
log.Printf("Reading %s: %v", filename, err)
```

## エラーハンドリング

関数の戻り値としてエラーが返ってくるときに、絶対に受け取り、処理をする必要があります。  
本当に例外の時 のみ panicを利用しましょう。  
また、エラーが発生したときは先にエラーハンドリングを書きましょう。

```go
// Bad
if x, err := f(); err != nil {
    // error handling
    return
} else {
    // use x
}

// Good
x, err := f()
if err != nil {
    // error handling
    return
}
// use x
```

ただし、エラーのみを返却する関数を実行する場合は「 `err` 変数の受け取りを複数回繰り返していると `:=` だけで書けないという状況」に落ちいってしまうため、以下の記載を許容します。

```go
// Good
if err := f(); err != nil {
    // error handling
    return
}
```

## import

名前の衝突を避ける以外の目的でインポートの名前を変更しません。  
衝突を避ける場合には、プロジェクト固有の名前にしましょう。

## レシーバのタイプ

- ポインタレシーバを利用する場合
  - メソッドがレシーバを変更する場合
  - レシーバが `sync.Mutex` または同様の同期フィールドを含む構造体である場合（コピーを避けるため）
  - レシーバが大きな構造体または配列の場合
  - レシーバが構造体、配列、またはスライスであり、その要素のいずれかが変化している可能性のある場合
  - その他判断に迷う場合
- 値レシーバを利用する場合
  - レシーバが map、func、または chan の場合
  - レシーバがスライスであり、メソッドがスライスの再スライスまたは再割り当てを行わない場合
  - レシーバが小さな配列または構造体の場合や可変フィールドとポインターがない場合、またはintやstringなどの単純な基本型である場合

## 参考

- [Effective Go](https://golang.org/doc/effective_go)
  - [日本語1](http://go.shibu.jp/effective_go.html)
  - [日本語2](https://xn--go-hh0g6u.com/doc/effective_go.html)

# エラー処理

- [Golangのエラー処理とpkg/errors](https://deeeet.com/writing/2016/04/25/go-pkg-errors/)

# インターフェース

- [インタフェースの実装パターン #golang](https://qiita.com/tenntenn/items/eac962a49c56b2b15ee8)
- [Goらしいコードを業務でも書くために](https://speakerdeck.com/kender_09/gorasiikodowoye-wu-demoshu-kutameni)

## インターフェースまとめ

`io.Reader` `io.Writer` `io.Closer`

```go
type Reader interface {
    Read(p []byte) (n int, err error)
}
type Writer interface {
    Write(p []byte) (n int, err error)
}
type Closer interface {
    Close() error
}
type ReadWriter interface { // 埋め込み
    Reader
    Writer
}
type WriteCloser interface { // 埋め込み
    Writer
    Closer
}
```

# channel

- [Go の channel 処理パターン集](https://hori-ryota.com/blog/golang-channel-pattern/)

```go
package main

import (
	"fmt"
	"time"
)

func add(c chan int, a, b int) {
	// 疑似処理時間
	time.Sleep(1*time.Second)
	// 本処理
	fmt.Printf("お仕事%d: %d + %d = %d\n", a/2, a, b, a+b)
	c <- 0
}

func main() {
	c, quit := make(chan int), make(chan int)
	// お仕事の作成
	nums := []int{0,1,2,3,4,5,6,7,8,9}
	// お仕事の分配
	for i := 0; i < 5; i++ {
		go add(c, nums[2*i], nums[2*i+1])
	}
	sum := 0
	for {
		select {
		case <-c:
			sum++
			if sum >= 5 {
				close(quit)
			}
		case <-quit:
			fmt.Println("お仕事おしまい。")
			return
		}
	}
}
```

```bash
お仕事1: 2 + 3 = 5
お仕事2: 4 + 5 = 9
お仕事4: 8 + 9 = 17
お仕事0: 0 + 1 = 1
お仕事3: 6 + 7 = 13
お仕事おしまい。
```

`sync.WaitGroup` を利用したら以下も同じ結果になります。

```go
package main

import (
	"fmt"
	"time"
	"sync"
)

func add(wg *sync.WaitGroup, a, b int) {
	defer wg.Done()
	// 疑似処理時間
	time.Sleep(1*time.Second)
	// 本処理
	fmt.Printf("お仕事%d: %d + %d = %d\n", a/2, a, b, a+b)
}

func main() {
	wg := new(sync.WaitGroup)
	quit := make(chan int)
	// お仕事の作成
	nums := []int{0,1,2,3,4,5,6,7,8,9}
	// お仕事の分配
	wg.Add(5)
	for i := 0; i < 5; i++ {
		go add(wg, nums[2*i], nums[2*i+1])
	}
	// お仕事の終わり待ち
    	go func() {
		wg.Wait()
		close(quit)
	}()
	// お仕事終わり検知
	select {
	case <-quit:
		fmt.Println("お仕事おしまい。")
		return
	}
}
```

# その他パターン

## 設計単位

```go
package trace

import (
    "fmt"
    "io"
)
// インターフェース
type Tracer interface {
    Trace(...interface{})
}
// 構造体
type tracer struct {
    out io.Writer
}
// メソッド
func (t *tracer) Trace(a ...interface{}) {
    t.out.Write([]byte(fmt.Sprint(a...)))
    t.out.Write([]byte("\n"))
}
// New
func New(w io.Writer) Tracer {
    return &tracer{out: w}
}
```

なお、 `interface` は必須ではありません。

# 小技

## NoMethod イディオム

例えば、JSON上では時刻を「unixtime形式」で表現し、プログラム中では「time.Time型」として表現することで、プログラム中で時刻の比較や計算をしやすくしたい、といったケースがあったとします。  
その際 `encoding/json` パッケージには、 `Marshaler` と `Unmarshaler` インターフェースが用意されており、以下のようにカスタマイズの実装ができます。

```go
// 対象の構造体
type Resource struct {
        ID        int64     `json:"id"`
        Timestamp time.Time `json:"-"`
}

func (r *Resource) MarshalJSON() ([]byte, error) {
        type Alias Resource
        
        // `timestamp`についてはunixtime形式に変換
        return json.Marshal(&struct {
                *Alias
                AliasTimestamp int64 `json:"timestamp"`
        }{
                Alias:          (*Alias)(r),
                AliasTimestamp: r.Timestamp.Unix(),
        })
}

func (r *Resource) UnmarshalJSON(b []byte) error {
        type Alias Resource

        // JSONからデコード
        aux := &struct {
                *Alias
                AliasTimestamp int64 `json:"timestamp"`
        }{
                Alias: (*Alias)(r),
        }
        if err := json.Unmarshal(b, &aux); err != nil {
                return err
        }

        // `Timestamp`については`time.Time`型に変換
        r.Timestamp = time.Unix(aux.AliasTimestamp, 0)
        return nil
}
```

この時、たままたメソッド定義が被って `Marshaler` と `Unmarshaler` インターフェースに対応してしまっています（ `MarshalJSON()` / `UnmarshalJSON()` を実装してしまっている）が、上記のようなカスタマイズの実装を適用したくないとします。  
その際は以下のようにメソッド定義の無い型として再定義することによって回避することができます。

```go
type Resource struct{}

func (r *Resource) MarshalJSON() ([]byte, error) {
    type NoMethod Resource   // メソッド定義の無い型に再定義（中身は同じ）
    r.MarshalJSON()          // 行いたい処理（たまたまメソッド定義が被ってしまった処理）
    raw := NoMethod(*r)      // メソッド定義が無い型へキャスト
    return json.Marshal(raw) // メソッド定義が無い型へキャストして渡したので、カスタマイズとして実行されない
}
```

[参考](https://qiita.com/weloan/items/def3d8b8b484f5f18dbf)

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