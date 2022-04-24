---
title:       "macOSにおけるzshの設定"
URL:         "zsh-settings"
subtitle:    ""
description: "Mac OS Catalinaから標準シェルがzshになっています。この記事では、zshの設定ファイルについて説明します。"
keyword:     "zsh, 設定ファイル, Mac, macOS"
date:        2022-04-24
author:      "ぺーぺーSE"
image:       ""
tags:
- zsh
- mac
categories:
- tech
---

Mac OS Catalinaから標準シェルがzshになっています。  
この記事では、Macにおけるzshの設定について説明します。

<!--more-->

# 初期設定

Catalina にアップデートすると、コマンドライン/ターミナルを開いた際に以下が表示されるようになります。

```bash
The default interactive shell is now zsh.
To update your account to use zsh, please run `chsh -s /bin/zsh`.
For more details, please visit https://support.apple.com/kb/HT208050.
```

上記に従って、以下を実行しましょう。

```bash
$ chsh -s /bin/zsh
Changing shell for <user>.
Password for <user>:
```

# シェルの種類

ここで、シェルの種類の復習をします。

1. ログインシェル
    - コマンドライン/ターミナルを起動したとき、サーバにログインしたときに立ち上がるシェルのことです
    - 対話形式で入力します
2. インタラクティブシェル
    - コマンドラインで `bash` や `zsh` と入力した際に起動するシェルのことです
    - 対話形式で入力します
3. シェルスクリプト
    - シェルで記述されたファイルを作成して実行することです

# 設定ファイル

設定ファイルについて説明します。

- `.zshenv`
  - ログインシェル、インタラクティブシェル、シェルスクリプト、どれでも常に必要な設定を定義します
  - `zsh` が起動して、必ず最初に読み込まれる設定ファイルになります
  - 影響範囲が大きいので記述は必要最低限にしましょう
- `.zprofile`
  - ログインシェルに必要な設定だけを記述します
  - `.zshenv` の次に読み込まれる設定ファイルです
  - `zsh` ログイン時の一回だけ読み込まれます
- `.zshrc`
  - ログインシェル、インタラクティブシェルの場合に読み込まれます
- `.zlogin`
  - ログインシェルに必要な設定だけを記述します
  - ログインシェルの最後に読み込まれます
  - 役割は `.zprofile` とほぼ一緒ですが、理由がない限り使用は推奨しません
- `.zlogout`
  - `exit` コマンドで `zsh` を抜けたときに読み込まれます

# まとめ

ご自身で Homebrew などの諸々のツール・プログラミング実行環境などをインストールした際は `.zshrc` に環境変数やパス、イニシャルシェルなどを設定するようにしましょう。

# おすすめ書籍

<!-- ad link - amazon/rakuten books - mac zsh -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"[新版 zsh\u0026bash対応]macOS×コマンド入門 ──ターミナルとコマンドライン、基本の力 (WEB+DB PRESS plusシリーズ)","b":"技術評論社","t":"","d":"https:\/\/m.media-amazon.com","c_p":"\/images\/I","p":["\/51hwh4PB1hL._SL500_.jpg","\/51-uuQ6lTUL._SL500_.jpg","\/41qFVBhBKrL._SL500_.jpg","\/51oK-l1FPsL._SL500_.jpg","\/41Jj-y+inwL._SL500_.jpg","\/41LLHR1Kf6L._SL500_.jpg","\/41dGWglrlAL._SL500_.jpg","\/41gLkscLieL._SL500_.jpg","\/41GhARtSo+L._SL500_.jpg","\/417qzq9MnJL._SL500_.jpg","\/41p5s+lYMAL._SL500_.jpg","\/41wsw04vtkL._SL500_.jpg","\/41VV4BN3uPL._SL500_.jpg","\/41krWBUggNL._SL500_.jpg","\/51-q2nduhTL._SL500_.jpg","\/41JZhdl-gRL._SL500_.jpg","\/41X6t0CwutL._SL500_.jpg","\/41Mixf8-kCL._SL500_.jpg","\/41l7KkFLTVL._SL500_.jpg","\/410khyLy9TL._SL500_.jpg","\/51ASZpJ8ybL._SL500_.jpg","\/41Pa9ZfeoCL._SL500_.jpg","\/51L74iZjuSL._SL500_.jpg","\/41LQ9SbgBDL._SL500_.jpg","\/414e7qbHHLL._SL500_.jpg","\/417y6onaEwL._SL500_.jpg","\/41o4FKqVRXL._SL500_.jpg","\/41AJGlsw8qL._SL500_.jpg"],"u":{"u":"https:\/\/www.amazon.co.jp\/dp\/4297112256","t":"amazon","r_v":""},"v":"2.1","b_l":[{"id":1,"u_tx":"Amazonで見る","u_bc":"#f79256","u_url":"https:\/\/www.amazon.co.jp\/dp\/4297112256","a_id":3351917,"p_id":170,"pl_id":27060,"pc_id":185,"s_n":"amazon","u_so":0},{"u_tx":"楽天ブックスで見る","u_bc":"#bf0000","u_url":"https:\/\/a.r10.to\/hud0pb","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":2,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/search.rakuten.co.jp\/search\/mall\/%5B%E6%96%B0%E7%89%88%20zsh%26bash%E5%AF%BE%E5%BF%9C%5DmacOS%C3%97%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E5%85%A5%E9%96%80%20%E2%94%80%E2%94%80%E3%82%BF%E3%83%BC%E3%83%9F%E3%83%8A%E3%83%AB%E3%81%A8%E3%82%B3%E3%83%9E%E3%83%B3%E3%83%89%E3%83%A9%E3%82%A4%E3%83%B3%E3%80%81%E5%9F%BA%E6%9C%AC%E3%81%AE%E5%8A%9B%20(WEB%2BDB%20PRESS%20plus%E3%82%B7%E3%83%AA%E3%83%BC%E3%82%BA)\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"AiUUE","s":"s"});
</script>
<div id="msmaflink-AiUUE">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>