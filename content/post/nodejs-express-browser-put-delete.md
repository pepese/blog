---
title:       "Node.js・ExpressでブラウザからPUT・DELETEリクエストを発行する"
URL:         "nodejs-express-browser-put-delete"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-09-20
author:      "ぺーぺーSE"
image:       ""
tags:
- express
- node.js
- npm
- javascript
- yarn
categories:
- tech
---

Node.js/Expressアプリケーションに対してブラウザからPUT/DELETEリクエストを発行する方法について記載する。  
通常、ブラウザからPUT/DELETEリクエストは発行できない。  
ここで紹介する方法も実際にブラウザからPUT/DELETEリクエストを発行しているのではなく、 *Node.js/Express側でPUT/DELETEリクエストとして解釈* しているだけ。

<!--more-->

以下の記事を読んだ前提で書く。

- [Express入門](https://blog.pepese.com/nodejs-express-basics/)

# 環境設定

先の記事で紹介したプロジェクトにて以下を実行する。

```sh
$ yarn add method-override
```

# 実装

先の記事で紹介したプロジェクトを基準に記載する。

`app.js` へ以下を加筆する。

```javascript
const methodOverride = require('method-override');

// 中略

app.use(methodOverride('_method'));

// 省略
```

上記を記載すれば、ブラウザからリクエストを発行する際、 **クエリパラメータ** に `_method=PUT` や `_method=DELETE` を記載することによって Node.js/Express 側で PUT/DELETE リクエストとして解釈される。  
つまり、 `express.Router().put()` や `express.Router().delete()` へルーティングされるようになる。  
ブラウザからのリクエストの発行の例は以下のようになる。

```html
form(action="/clients?_method=PUT")
  input(type="submit" value="submit")
```

```html
a(href="/clients?_method=DELETE")
```

`router.put('/clients', ...` や `router.delete('/clients', ...` を実装すれば、上記のブラウザからのリクエストを処理することができるようになる。

# おすすめ書籍

<!-- amazon affiliate kindle node.js --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B08HRMTXHB&linkId=f02e6af82c7864b6df5fd31c0639d4bf"></iframe>