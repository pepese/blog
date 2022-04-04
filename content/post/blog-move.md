---
title:       "ブログ移転時に気をつけること"
URL:         "blog-move"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-05-11
author:      "ぺーぺーSE"
image:       ""
tags:
- seo
- javascript
categories:
- tech
---

筆者は「はてなブログ」を使っているが、諸事情により一部の記事を本ブログへ移転している。  
その際、元の記事を残しておくと、内容が重複することからSEO的によろしく無い。
よろしくない理由は以下の通り。

- 重複コンテンツは同時に検索結果に表示されない
- 被リンク評価が分散する

ここでは、「はてなブログ」の記事を別のブログへ移転する方法について記載する。  
ブログ全体の移転の話ではないことに注意。

<!--more-->

一般的にブログを引っ越しする際には以下のようなことを実施する。

- HTTPステータスコード301でリダイレクトさせる
- headタグ内に `link rel="canonical"` を設置する
    - `<link rel="canonical" href="[移転先のURL]">`

ここではブログ全体を移転せず一部の記事のみを移転する方法を記載する。  
前提は以下。

- ブログ全体ではなく、一部の記事のみを移転する
- 記事の移転をスムーズにするために、閲覧者・検索エンジンに教えてあげる
- 元の記事は「はてなブログ」を使用している
- Google検索エンジンは簡単なJavaScriptであれば理解してくれる

という訳で、移転元の記事に「 **`link rel="canonical"` を付与** しつつ **移転先の記事へリダイレクト** するJavaScript」を埋め込む。  
その後以下のスクリプトを記事へ貼り付ける。

```html
<p>本記事は移転しました。</p>
<p>約3秒後にリダイレクトします。</p>
<p>リダイレクトしない場合は<a href="[リダイレクト先のURL]">ここ</a>をクリックしてください。</p>
<script type="text/javascript" language="javascript">
<!--
  // 新url
  var url = "[リダイレクト先のURL]";

  // link rel="canonical" の変更
  // 「はてなブログ」ではheadタグの最初のlinkタグに以下がある
  // <link rel="canonical" href="xxx"/>
  var link = document.getElementsByTagName("link")[0];
  link.href = url;

  // リダイレクト
  setTimeout("redirect()", 3000);　// 3 sec
  function redirect(){
    location.href = url;
  }
-->
</script>
```

ただし、上記ではブログトップページなどで複数の記事が表示された際にもリダイレクトされてしまう。  
「はてなブログ」では個別の記事ではURLに「entry」という文字列が含まれる。  
これを利用して下記のスクリプトのように正規表現にマッチングさせて個別の記事でのみ実行されるようにする。

```html
<p>本記事は移転しました。</p>
<p>約3秒後にリダイレクトします。</p>
<p>リダイレクトしない場合は<a href="[リダイレクト先のURL]">ここ</a>をクリックしてください。</p>
<script type="text/javascript" language="javascript">
<!--
  // 現在のurl
  var href = window.location.href;
  // 記事ページのurl正規表現
  var re = new RegExp(".*entry.*");
  if (re.test(href)) {
    // 新url
    var url = "[リダイレクト先のURL]";
    // link rel="canonical" の変更
    // 「はてなブログ」ではheadタグの最初のlinkタグに以下がある
    // <link rel="canonical" href="xxx"/>
    var link = document.getElementsByTagName("link")[0];
    link.href = url;

    // リダイレクト
    setTimeout("redirect()", 3000);　// 3 sec
    function redirect(){
      location.href = url;
    }
  }
-->
</script>
```

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>