# アフェリエイト

- Google Adsense
- Amazonアソシエイト：本
- アフェリエイト：A8、バリューコマース
- Udemy Affiliates Program

## Google Adsense

自動広告を貼り付けておく。

## 本

もしもアフェリエイト・Amazon公式・楽天公式のアフェリエイトを組み合わせて利用する。  
以下の手順で Amazon・楽天ハイブリットなリンクを作成する。

1. もしもアフェリエイトの「かんたんリンク」を利用する
2. かんたんリンク作成ページにて商品名を検索し、ボタン付画像広告を作成する
3. 上記に以下のボタンを追加する
    - 「Amazonで見る」オレンジ：公式のAmazonアソシエイトにて「テキスト」広告を作成してボタンのリンクに貼り付ける（Kndle優先）
    - 「楽天ブックスで見る」濃い赤：公式の楽天アフェリエイトにて「テキスト」広告を作成してボタンのリンクに貼り付ける
4. 「Amazonで見る」「楽天ブックスで見る」「楽天市場で見る」の順に並び替える
5. 作成したリンクのHTMLを本ページに保存し、コメントを付け加えた上で記事に貼り付ける
    - 各商品リンクには `<!-- ad link - amazon/rakuten books - kubernetes -->` のようなコメントをつけて検索可能にしておく。
6. 貼り付け箇所は、記事の最後に `# おすすめ書籍` 節をつけて貼り付ける。

### zsh

```
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
```

### Docker

```
<!-- ad link - amazon/rakuten books - docker -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Dockerコンテナ開発・環境構築の基本 （impress top gear　impress top gear） [ 市川　豊 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/1736\/9784295011736_1_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16742446\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3JfjoTm","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hakvUs","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16742446\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"FzcPh","s":"s"});
</script>
<div id="msmaflink-FzcPh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Kubernetes

```
<!-- ad link - amazon/rakuten books - kubernetes -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Kubernetes完全ガイド 第2版 （top gear） [ 青山真也 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/9795\/9784295009795.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16394303\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3NSTV5N","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hwqZUD","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16394303\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"qzkMG","s":"s"});
</script>
<div id="msmaflink-qzkMG">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Git

```
<!-- ad link - amazon/rakuten books - git -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"動かして学ぶ！Git入門 （NEXT ONE） [ 冨永 和人 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/0855\/9784798170855_1_28.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16833788\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3x9e0yQ","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMvEIy","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16833788\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"slVJL","s":"s"});
</script>
<div id="msmaflink-slVJL">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Go

```
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
```

### Node.js

```
<!-- ad link - amazon/rakuten books - node.js -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Node.js超入門 第3版 [ 掌田津耶乃 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/2433\/9784798062433.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16353060\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3Jf72uu","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/haXJAD","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16353060\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"vtzlh","s":"s"});
</script>
<div id="msmaflink-vtzlh">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Python

```
<!-- ad link - amazon/rakuten books - python -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"みんなのPython 第4版 [ 柴田 淳 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/9463\/9784797389463.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DMswgV","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMCjfu","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"rQIGU","s":"s"});
</script>
<div id="msmaflink-rQIGU">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Machine Learning

```
<!-- ad link - amazon/rakuten books - scikit-learn -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Pythonではじめる機械学習 scikit-learnで学ぶ特徴量エンジニアリングと機械学習の基礎 [ Andreas C. Muller ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/7980\/9784873117980.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/14919364\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DRmVpN","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hwLAoI","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/14919364\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"OawK3","s":"s"});
</script>
<div id="msmaflink-OawK3">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Java

```
<!-- ad link - amazon/rakuten books - java -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"新わかりやすいJava オブジェクト指向徹底解説 第2版 [ 川場隆 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/5007","p":["\/9784798065007_1_3.jpg","\/9784798065007_2.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16977843\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37mQD9Z","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hak9Ku","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16977843\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"Hu62r","s":"s"});
</script>
<div id="msmaflink-Hu62r">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### SpringBoot

```
<!-- ad link - amazon/rakuten books - springboot -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Javaによる高速Webアプリケーション開発のためのSpring Boot入門【電子書籍】[ WINGSプロジェクト 小林昌弘 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/rakutenkobo-ebooks\/cabinet\/4113\/2000008544113.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/rakutenkobo-ebooks\/9f4680988e103ee695a458e7f8bd8d00\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3Jx0qrk","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/haY69i","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/rakutenkobo-ebooks\/9f4680988e103ee695a458e7f8bd8d00\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"eWOl7","s":"s"});
</script>
<div id="msmaflink-eWOl7">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### AWS：EC2などの基本

```
<!-- ad link - amazon/rakuten books - aws basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"AWSではじめるインフラ構築入門 安全で堅牢な本番環境のつくり方 [ 中垣 健志 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3437\/9784798163437.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3j7fBNl","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/h6WEYh","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16586730\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"e0IUt","s":"s"});
</script>
<div id="msmaflink-e0IUt">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### AWS：Lambda

```
<!-- ad link - amazon/rakuten books - aws lambda -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"AWS Lambda実践ガイド 第2版 （impress top gear） [ 大澤 文孝 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/3303","p":["\/9784295013303_1_4.jpg","\/9784295013303_2.jpg","\/9784295013303_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16987486\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3J83xpr","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hM1BhW","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16987486\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"Dlcy4","s":"s"});
</script>
<div id="msmaflink-Dlcy4">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Google Cloud：基本

```
<!-- ad link - amazon/rakuten books - google cloud basics -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"図解即戦力　Google Cloudのしくみと技術がこれ1冊でしっかりわかる教科書 [ 株式会社grasys ／ Google Cloud 西岡典生、田丸司 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3017\/9784297123017.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37iaebm","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMkNFF","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16805152\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"ju6Y9","s":"s"});
</script>
<div id="msmaflink-ju6Y9">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Terraform:AWS

```
<!-- ad link - amazon/rakuten books - terraform aws -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"【POD】実践Terraform　AWSにおけるシステム設計とベストプラクティス AWSにおけるシステム設計とベストプラクティス （技術の泉シリーズ（NextPublishing）） [ 野村 友規 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/8136\/9784844378136.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16058180\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/37mOivB","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hawlg8","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16058180\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"kSp0Z","s":"s"});
</script>
<div id="msmaflink-kSp0Z">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### Terraform:Google Cloud

無い、、、

### Visual Studio Code

```
<!-- ad link - amazon/rakuten books - vscode -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Visual Studio Code完全入門 Webクリエイター\u0026エンジニアの作業がはかどる新世代エディターの操り方 [ リブロワークス ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"\/@0_mall\/book\/cabinet\/3457","p":["\/9784295013457_1_2.jpg","\/9784295013457_2.jpg","\/9784295013457_3.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/17018592\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3x7jQ3u","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/h54zvN","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/17018592\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"UC6Nd","s":"s"});
</script>
<div id="msmaflink-UC6Nd">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### 仮想通貨：ブロックチェーン

```
<!-- ad link - amazon/rakuten books - blockchain -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"図解即戦力　ブロックチェーンのしくみと開発がこれ1冊でしっかりわかる教科書 [ コンセンサス・ベイス株式会社 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/6362\/9784297106362.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/15994333\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3r5bwh8","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hawwGl","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/15994333\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"6lU7D","s":"s"});
</script>
<div id="msmaflink-6lU7D">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### 仮想通貨：Ethereum

```
<!-- ad link - amazon/rakuten books - ethereum -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Ethereum＋Solidity入門 Web3．0を切り拓くブロックチェーンの思想と技術 （impress　top　gear） [ クリス・ダネン ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/5735\/9784295005735.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/15827901\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3LHBVcz","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hw25SG","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/15827901\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"a2sXk","s":"s"});
</script>
<div id="msmaflink-a2sXk">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

### アジャイル：スクラム

```
<!-- ad link - amazon/rakuten books - agile scrum -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"SCRUM BOOT CAMP THE BOOK【増補改訂版】 スクラムチームではじめるアジャイル開発 [ 西村 直人 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/3680\/9784798163680.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16284702\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"id":1,"u_tx":"Amazonで見る","u_bc":"#ff9900","u_url":"https:\/\/amzn.to\/3jESqtU","a_id":0,"p_id":0,"pl_id":0,"pc_id":0,"s_n":"custom_1","u_so":0},{"u_tx":"楽天ブックスで見る","u_bc":"#bf0000","u_url":"https:\/\/a.r10.to\/hMMehy","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":2,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16284702\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"TULoG","s":"s"});
</script>
<div id="msmaflink-TULoG">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->
```

## アフェリエイト：A8、バリューコマース

- テキストリンクを記事の中に入れる
- 広告タグが改行して困ったときは img タグに `style="display:inline;"` を足す

### プログラミングスクール

- 待ち
  - tech boost: A8

### 転職サイト

- 待ち
  - リクルートダイレクトスカウト: A8
- 落ちたリスト
  - マイナビIT AGENT: A8
  - doda: value commerce
  - biz reach: valude commerce

### エンジニア採用（転職側ではなく採用側）

ない

## Udemy Affiliates Program

ちょっと難易度高そうなので、アクセス数が上がってきたら再検討。