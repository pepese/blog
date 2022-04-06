---
title:       "形態素解析システムMeCab入門"
URL:         "python-mecab-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2018-02-04
author:      "ぺーぺーSE"
image:       ""
tags:
- mecab
- python
- node.js
categories:
- tech
---

[公式](http://taku910.github.io/mecab/)

- 環境構築
- 使い方
- Node.js から Mecabを使う
- Python3 から MeCab を使う

<!--more-->

# 環境構築

## インストール

```bash
$ brew install mecab
$ mecab --version
mecab of 0.996
```

## 辞書の追加

このままでは辞書がないので追加する。  
辞書の場所は `/usr/local/lib/mecab/dic/` 配下。

### mecab-ipadic-neologd のインストール

```bash
$ git clone --depth 1 https://github.com/neologd/mecab-ipadic-neologd.git
$ cd mecab-ipadic-neologd/
$ ./bin/install-mecab-ipadic-neologd -n -p /usr/local/lib/mecab/dic/neologd
```

最後に本当にインストールするか聞かれるから `yes` or `no` をタイプする。  
なお、筆者の環境では足りなかった以下のコマンドを追加した。

```bash
$ brew install xz
```

### デフォルトの辞書を変更する

`mecab -d /usr/local/lib/mecab/dic/neologd/` でも辞書を指定して実行できるが、めんどくさいのでデフォルトを変更する。  
`/usr/local/etc/mecabrc` の以下を変更する。

```
dicdir =  /usr/local/lib/mecab/dic/ipadic
# 以下のように変更
dicdir =  /usr/local/lib/mecab/dic/neologd
```

# 使い方

インタラクティブモードは以下。（そのままコマンドを打つ）

```bash
$ mecab
おはよう
おはよう	感動詞,*,*,*,*,*,おはよう,オハヨウ,オハヨー
EOS
```

第一引数にファイルを指定することもでき、 `-o` オプションで出力も可能。

```
$ mecab [入力ファイル] -o [出力ファイル]
```

## 形態素の見方

```
表層形\t品詞,品詞細分類1,品詞細分類2,品詞細分類3,活用型,活用形,原形,読み,発音
```

「表層形」は文章からそのまま抜き出した形。  
なお MeCab の品詞体系は [IPA品詞体系](http://www.unixuser.org/~euske/doc/postag/#chasen)というものが使われている。（茶筅と同じ）

## 未知語推定

MeCabには未知語を推定する機能があり、デフォルトでは有効。  
未知語を抽出したい場合は `-x` （ `--unk-feature` ）で未知語の表示形式を指定して実行する。

```bash
$ mecab -x "undef"
にゃほにゃほたまくろー
に      助詞,格助詞,一般,*,*,*,に,ニ,ニ
ゃほにゃほたまくろ      undef
ー      undef
EOS
```

## ユーザ辞書の追加

自分で単語を登録したいときの方法。  
csv ファイルに 1 行 1 単語で以下の形式で作成する。

```
表層形,左文脈ID,右文脈ID,コスト,品詞,品詞細分類1,品詞細分類2,品詞細分類3,活用形,活用型,原形,読み,発音
```

例えば以下。

```
にゃほにゃほたまくろー,1223,1223,6058,名詞,固有名詞,人名,名,*,*,ニャホニャホタマクロー,ニャホニャホタマクロー,ニャホニャホタマクロー
```

- 左文脈ID
    - その単語を左から見たときの内部状態ID
    - `/usr/local/lib/mecab/dic/neologd/left-id.def` から該当する ID を選択
    - 空にしておくと mecab-dict-index が自動的に ID を付与する
- 右文脈ID
    - その単語を右から見たときの内部状態ID
    - `/usr/local/lib/mecab/dic/neologd/right-id.def` から該当する ID を選択
    - 空にしておくと, mecab-dict-index が自動的に ID を付与する
- コスト
    - その単語がどれだけ出現しやすいかを示す
    - 小さいほど, 出現しやすいという意味
    - 似たような単語と 同じスコアを割り振り, その単位で切り出せない場合は, 徐々に小さくしていけばいい

以下のコマンドを実行する。

```bash
$ /usr/local/Cellar/mecab/0.996/libexec/mecab/mecab-dict-index -d /usr/local/lib/mecab/dic/neologd -u user-dic.dic -f utf8 -t utf8 user-dic.csv
```

- -d DIR
    - システム辞書があるディレクトリ
- -u FILE
    - FILE というユーザファイルを作成
- -f charset
    - CSVの文字コード
- -t charset
    - バイナリ辞書の文字コード

`/usr/local/lib/mecab/dic/user-dic/` ディレクトリを作成し、上記のコマンドで作成した `user-dic.dic` を配置。  
`/usr/local/etc/mecabrc` に以下を追記。

```
userdic = /usr/local/lib/mecab/dic/user-dic/user-dic.dic
```

[参考](https://taku910.github.io/mecab/dic.html)

## 分かち書き

```bash
$ mecab -Owakati input.txt > output_wakati.txt
```

# Node.js から Mecabを使う

上記の手順で MeCab をセットアップしていることが前提。

```bash
$ yarn add mecab-async
```

```javascript
let MeCab = new require('mecab-async');
let mecab = new MeCab();
let input = process.argv[2];

mecab.parse(input,(err,result)=>{
  if(err){
  }
  console.log(result);
});
```

```bash
$ node app.js "にゃほにゃほたまくろー"
[ [ 'にゃほにゃほたまくろー',
    '名詞',
    '固有名詞',
    '人名',
    '名',
    '*',
    '*',
    'ニャホニャホタマクロー',
    'ニャホニャホタマクロー',
    'ニャホニャホタマクロー' ] ]
```


# Python3 から MeCab を使う

```bash
$ pip install mecab-python3
```

```python
import MeCab
mc = MeCab.Tagger("")
print (mc.parse("形態素解析システムMeCab入門"))
```

`MeCab.Tagger` の引数に `mecab` コマンドの引数を指定することができる。  
また、 parse の結果は文字列になっている。

# おすすめ書籍

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

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>