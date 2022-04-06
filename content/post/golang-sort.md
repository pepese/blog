---
title:       "Goでソート"
URL:         "golang-sort"
subtitle:    ""
description: ""
keyword:     ""
date:        2019-02-10
author:      "ぺーぺーSE"
image:       ""
tags:
- go
categories:
- tech
---

Golang におけるソートについてまとめる。

<!--more-->

# sort パッケージ

sort パッケージでは、以下の `Interface` に合わせた実装をしておけば、独自の type でも `sort.Sort(data Interface)` でソートしてくれる。

```go
type Interface interface {
	Len() int // 要素数
	Less(i, j int) bool // i 番目の要素が j 番目の要素より小さくなる条件
	Swap(i, j int) // i 番目の要素と j 番目の要素を入れ替えるロジック
}
```

とはいえ面倒なので、`IntSlice` （ `[]int` ）、`Float64Slice` （ `[]float64` ）、`StringSlice` （ `[]string` ）は用意されており、キャストして使用できる。

```go
var a []int
sort.Sort(sort.IntSlice(a))
```

キャストしたら、メンバに `Sort()` メソッドも実装されている。

```go
var a []int
sort.IntSlice(a).Sort()
```

もしくはキャストしなくとも、`Ints([]int)` 、 `Float64s([]float64)` 、 `Strings([]string)` が用意されている。

```go
var a []int
sort.Ints(a)
```

## Slice

`Interface` の実装が面倒なら `less` だけ実装すればよい関数もある。

- func Slice(slice interface{}, less func(i, j int) bool)
- func SliceStable(slice interface{}, less func(i, j int) bool)

`slice` はソート対象。
また、 `SliceStable` は [安定ソート](https://ja.wikipedia.org/wiki/%E5%AE%89%E5%AE%9A%E3%82%BD%E3%83%BC%E3%83%88) 。

# sort パッケージで独自のソートを作ってみる

## Interface の実装

```go
package main

import (
	"fmt"
	"sort"
)

type user struct {
	name string
	age  int
}

type users []user

func (u users) Len() int {
	return len(u)
}

func (u users) Less(i, j int) bool {
	return u[i].age < u[j].age
}

func (u users) Swap(i, j int) {
	u[i], u[j] = u[j], u[i]
}

func main() {
	list := []user{
		user{name: "A", age: 2},
		user{name: "B", age: 1},
		user{name: "C", age: 4},
		user{name: "D", age: 3},
	}
	us := users(list)
	sort.Sort(us)
	fmt.Println(us)
}
```

## Sliceの利用

```go
package main

import (
	"fmt"
	"sort"
)

type user struct {
	name string
	age  int
}

func main() {
	list := []user{
		user{name: "A", age: 2},
		user{name: "B", age: 1},
		user{name: "C", age: 4},
		user{name: "D", age: 3},
	}
	sort.Slice(list, func(i, j int) bool {
		return list[i].age < list[j].age
	})
	fmt.Println(list)
}
```

# スクラッチでソートしてみる

sort パッケージ使わずに。

## バブルソート

```go
package main

import (
	"fmt"
)

func sort(list []int) {
	eNum := len(list)
	for i := eNum; i > 0; i-- {
		for j := 0; j < i-1; j++ {
			if list[j] > list[j+1] {
				list[j], list[j+1] = list[j+1], list[j]
			}
		}
	}
}

func main() {
	list := []int{3, 4, 1, 2, 8, 5}
	sort(list)
	fmt.Println(list)
}
```

暇になったら他にも作ろうかなー。

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