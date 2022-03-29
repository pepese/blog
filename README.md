# Blog

## メモ

### 常用コマンド

```bash
# 記事作成
$ hugo new draft/xxxx.md
$ hugo new post/xxxx.md
# 記事名（xxxx）は URL と同じにする

# ローカル実行
$ hugo server -D

# Github Pages 公開
$ rm -fR public # 初回のみ
$ git submodule add -f https://pepese@github.com/pepese/pepese.github.io.git public # 初回のみ
$ hugo
$ cd public
$ git add .
$ git commit -m update
$ git push origin master
```

### 整理

#### タグ

全て小文字にする。  
大分類、小分類の 2 個つけるようにする。

- 大分類
  - 小分類
- aws
  - "aws cli"
  - ec2
  - elasticsearch
  - iam
  - kibana
  - lambda
  - watcher
- プログラミング言語
  - フレームワーク
- hugo
- kubernetes
    - helm

#### カテゴリ

全て小文字にする。

- tech -> テックネタ

## 作成時メモ

### 初回作成

```bash
$ hugo new site blog
Congratulations! Your new Hugo site is created in /Users/xxxxx/workspace/github/pepese/blog.

Just a few more steps and you're ready to go:

1. Download a theme into the same-named folder.
   Choose a theme from https://themes.gohugo.io/ or
   create your own with the "hugo new theme <THEMENAME>" command.
2. Perhaps you want to add some content. You can add single files
   with "hugo new <SECTIONNAME>/<FILENAME>.<FORMAT>".
3. Start the built-in live server via "hugo server".

Visit https://gohugo.io/ for quickstart guide and full documentation.

$ cd blog

$ git init
Initialized empty Git repository in /path/to/repository/blog/.git/

$ git remote add origin https://github.com/pepese/blog.git
$ git config --local user.name pepese
$ touch .gitignore
$ echo /public > .gitignore
```

### テーマの設定

[Hugo Themes](https://themes.gohugo.io/) から好きなものを選ぶ。  
ここでは [これ](https://themes.gohugo.io/themes/hugo-theme-cleanwhite/) を選択。  
`git submodule add` で取得するところだが、今回は `git clone` で取得。

```bash
# $ git submodule add https://github.com/zhaohuabing/hugo-theme-cleanwhite themes/hugo-theme-cleanwhite
$ git clone https://github.com/zhaohuabing/hugo-theme-cleanwhite themes/hugo-theme-cleanwhite
$ cp -a themes/hugo-theme-cleanwhite/exampleSite/* .
$ hugo server
```

上記の作業の後、好みに合わせて `layouts/` 配下に修正した HTML を配置している。

### 設定ファイルの編集

`config.toml` を編集する。
設定はテーマによってクセがあるので、テーマの github をよく見ること。

### 記事の作成

以下のコマンドを実行すると `content/<CATEGORY>/<FILE>.<FORMAT>` に作成される。

```bash
# USAGE
$ hugo new <CATEGORY>/<FILE>.<FORMAT>

# USAGE / 意味
$ hugo new <content配下のディレクトリ>/タイトル.md

# 例
$ hugo new entry/my-first-post.md
```

上記の例だと `[baseURL]/entry/my-first-post/` が記事へのパスとなる。  
ファイル名がパスになるのがイヤな場合は、各記事で `slug` を設定すると変更できる。（ただし、 `<CATEGORY>` 部分のパスは変更できない。）  
今回の Themes の場合は、 `URL` を設定することでファイルをどこに配置しようともフルパスで URL を指定することができる。  
なお、 `<CATEGORY>` 部分は必須ではない。（無い場合は `content/` 直下にファイルが作成されるだけ）

`<CATEGORY>` は任意だが、ここでは以下を作成する。

- `draft`
  - ドラフト版・作成中の記事で、 `draft: true` を設定
  - 記事の作成が完了したら、ファイルを一つ上のディレクトリへ移動し、 `draft: true` に設定
- `post`
  - 公開用の記事置き場
  - `draft: true` の設定無し

### 記事のテンプレート

`hugo new` で記事を作成する場合、テンプレートとして以下が見つかった順で適用される。（ [参考](https://gohugo.io/content-management/archetypes/) ）

1. `archetypes/<CATEGORY>.md`
2. `archetypes/default.md`
3. `themes/my-theme/archetypes/<CATEGORY>.md`
4. `themes/my-theme/archetypes/default.md`

`archetypes/post.md` と `archetypes/draft.md` （`draft: false` あり）を作成しておく。

### sitemap.xml

デフォルトで作成される。

### robots.txt の作成

デフォで作成されてるが、自分で変種したい時は以下。

- config.toml の設定で enableRobotsTXT = true とする
- /layouts/robots.txt に雛形を準備する

```
User-agent: *

Sitemap : {{ $.Site.BaseURL }}sitemap.xml
```

### Google Adsenseの設置

ポイントは、 **取得したテーマを直接編集不要** ということ。  
Hugo では 同名のファイルであれば、 テーマのレイアウト（ `themes/[テーマ名]/layouts` ）より独自のレイアウト（ `layouts/` ）が優先される。  
そのため、ヘッダ、フッタ、サイドバーなどアドを差し込みたい部品をそれぞれ独自に作成する。

```bash
$ mkdir layouts/partials
$ touch layouts/partials/header.html
$ touch layouts/partials/footer.html
$ touch layouts/partials/sidebar.html
```

上記のファイルについて、利用しているテーマから同名のファイルの中身をコピペして好きな内容を書き加えればカスタマイズできる。
以下のようなアドを上記のファイルに適宜差し込む。

```html
<!-- ad start -->
<script async src="//pagead2.googlesyndication.com/pagead/js/adsbygoogle.js"></script>
<ins class="adsbygoogle"
     style="display:inline-block;width:728px;height:90px"
     data-ad-client="xxxx"
     data-ad-slot="xxxx"></ins>
<script>
(adsbygoogle = window.adsbygoogle || []).push({});
</script>
<!-- ad end -->
```

### Google Analyticsの設置

`config.toml` に以下を設定するだけ。

```toml
googleAnalytics = "UA-xxxxxxxx-x"
```

### OGPの設定

[OGP](http://ogp.me/) （Open Graph Protcol）は、FacebookやTwitterなどのSNSでシェアされた際に、そのページのタイトル・URL・概要・サムネイル画像を表示させる仕組みのこと。  
以下で OGP が有効になる。

```toml
[Params]
  opengraph = true
```

以下で画像を設定できる。

```toml
[Params]
  images = ["img/yaruwo.gif"]
```

なお、上記の設定方法は現在のテーマが利用している `_internal/` 以下のファイルを利用するもので標準の [Internal Template](https://github.com/gohugoio/hugo/tree/master/tpl/tplimpl/embedded/templates) の仕様に従っている。