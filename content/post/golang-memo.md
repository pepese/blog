---
title:       "Golang Memo"
URL:         "golang-memo"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-05-06
author:      "ぺーぺーSE"
image:       ""
tags:
- tag1
- tag2
categories:
- tech
draft:       true
---

Description

<!--more-->

# context.Context

- 主に goroutine や API コールの際に、タイムアウト制御を適切に行うために使用
- UserID 、 JWT など、リクエストスコープに限定される値を Get/Set するときのみ Value を使う
- Go 1.7 以降ではリクエストから context.Context を取得可能（ `ctx := r.Context()` ）
- その他の用途、受け渡しでは絶対使わない

↓

- 無駄に構造体に context を入れない
- Context を適切にハンドラに渡す
  - タイムアウトは ServeHTTP で行う（ Middleware でも可）
  - `func Handler(ctx context.Context, w http.ResponseWriter, r *http.Request)`
- 各種クライアントや Logger は Handler のレシーバを定義してその中に入れる
  - `function (app *App) GetItemHandler(ctx context.Context, ...)`

```go
type AppHandler struct {
    Impl func(ctx context.Context, w http.ResponseWriter, r *http.Request)
}

func (a AppHandler) ServeHTTP (w http.ResponseWriter, r *http.Request) {
    ctx := appengine.NewContext(r)
    ctx, cancel := context.WithTimeout(ctx, TimeOutLimit)
    defer cancel()
    a.Impl(ctx, w, r)
}
```