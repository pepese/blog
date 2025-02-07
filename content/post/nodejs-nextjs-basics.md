---
title:       "Next.js入門"
URL:         "nodejs-nextjs-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2024-12-26
author:      "ぺーぺーSE"
image:       ""
tags:
- next.js
- react
- typescript
- javascript
- node.js
categories:
- tech
---

[Next.js](https://nextjs.org/) についてまとめる。

<!--more-->

# Next.js とは？

Next.js は React の機能を拡張したフレームワークです。  
以下のような特徴があります。

- **SSR(Server Side Rendering)** 表示が速い
  - ページを表示する前にサーバー側で HTML を作成
  - SPA(Single Page Application) とは異なり SEO にも強い
- **SSG(Static Site Generation)** で事前に HTML を作成
  - 静的ページがビルド時に生成され、各リクエストに再利用される
  - 更新頻度の低いページの表示が高速になる
- シンプルなファイルシステムベースのルーティング（ **Pages/App Router** ）
  - Pages Router であれば `page/` 、App Router であれば `app/` ディレクトリ配下の構成がそのまま URL パスに対応
  - React ではページルーティング（ページ遷移）を実装するために追加のライブラリが必要だが Next.js では不要
- CSS を簡単に書ける
  - **CSS Modules** ：ローカルスコープの CSS クラスを作成して、名前の競合を回避し、保守性も高い
  - **Tailwind CSS** ：ユーティリティクラスを作成して迅速なカスタムデザインを可能にするユーティリティファーストの CSS フレームワーク
  - 上記に加えて、既存の CSS や Sass などのフレームワークを利用することも可能
- バックエンドとの連携が容易
  - サーバとのデータのやり取りを簡単に記載可能
  - クライアント・サーバのコードを 1 つのプロジェクトで管理可能

## ドキュメント

- [公式](https://nextjs.org/docs)
- [日本語](https://nextjs-ja-translation-docs.vercel.app/docs/getting-started)（※バージョンが古いので注意）

# 環境構築

`npx` を利用すると、以下のように必要なライブラリを自動取得してプロジェクトを構築します。

```bash
$ npx create-next-app@latest
Need to install the following packages:
create-next-app@15.1.2
Ok to proceed? (y) y
```

以下のようにいくつか質問されるので答えていく。

```bash
✔ What is your project named? … next-sample
✔ Would you like to use TypeScript? … No / [Yes]
✔ Would you like to use ESLint? … No / [Yes]
✔ Would you like to use Tailwind CSS? … [No] / Yes
✔ Would you like your code inside a `src/` directory? … No / [Yes]
✔ Would you like to use App Router? (recommended) … No / [Yes]
✔ Would you like to use Turbopack for `next dev`? … No / [Yes]
✔ Would you like to customize the import alias (`@/*` by default)? … [No] / Yes
```

筆者は `[]` で囲った方を選択しました。  
デフォルトでは以下のコマンドが用意されています。

- `npm run dev`：開発用実行
- `npm run build`：本番用ビルド（ `.next` ディレクトリに出力される）
- `npm run start`：本番用実行
- `npm run lint`：リントの実行

その他、 Prettier などの設定を [こちら](https://zenn.dev/siakas/articles/05481bdefacd13) のような記事を参考に設定します。

# 主要機能

本記事では Next.js が有する主要な以下の機能を紹介します。  
全ての機能を紹介するわけではありませんので、詳細は公式ドキュメントをご参照ください。

- Router
- Link コンポーネント
- Styling（CSS）
- データフェッチ

## Router

ルーティング方式には [**Pages Router**](https://nextjs.org/docs/pages) と [**App Router**](https://nextjs.org/docs/app) があります。  
App Router は、Next.jsのバージョン13.4で安定版となりました。  
恩恵も多いので基本的には App Router を利用することになります。

### Pages Router

Pages Router では、`/pages` ディレクトリ配下のファイル構造がURLの構造になります。  
`components/` は慣例としてこの名称で特に決まりはありませんが、文字通りコンポーネントを配置するディレクトリとなります。

```bash
pages/
├── _app.tsx          # グローバルレイアウト (Client Component)
├── index.tsx         # ルート: / (Client Component)
├── about.tsx         # ルート: /about (Client Component)
├── products/
│   ├── index.tsx     # ルート: /products (Client Component)
│   └── [id].tsx      # ルート: /products/[id] (Client Component)
└── api/
    └── products.tsx  # API route: /api/products

components/
├── Header.tsx        # コンポーネント例：共通ヘッダー (Client Component)
└── Footer.tsx        # コンポーネント例：共通フッター (Client Component)
```

`Client Component` と記載しているものは **CSR(Client Side Rendering)** となります。  
Pages Router ではデフォルトで CSR となり、SSRには `getServerSideProps` 、 SSG には `getStaticProps` などを利用します。  
共通レイアウト（全てのルートに適用されるレイアウト）は `pages/_app.tsx` 、個別レイアウトは `Layout` コンポーネントを作成してページ毎に使用したい `Layout` コンポーネントを適用することで行います。

### App Router

App Router では、`/app` ディレクトリ配下のディレクトリ構造がURLの構造になります。  
`page.tsx` には、そのページが読み込まれたときに最初に表示される内容を書きます。

```bash
app/
├── layout.tsx                # ルートレイアウト (Server Component)
├── page.tsx                  # ルート: / (Server Component)
├── about/
│   └── page.tsx              # ルート: /about (Server Component)
├── products/
│   ├── page.tsx              # ルート: /products (Server Component)
│   ├── [id]/
│   │   └── page.tsx          # ルート: /products/[id] (Server Component)
│   └── client-components/
│       ├── ProductList.tsx   # コンポーネント例：製品リスト (Client Component)
│       └── ProductFilter.tsx # コンポーネント例：製品フィルタ (Client Component)
└── api/
    └── products/
        └── route.tsx         # API Route: /api/products

components/
├── Header.tsx                # コンポーネント例：共通ヘッダー (Server Component)
└── Footer.tsx                # コンポーネント例：共通フッター (Server Component)
```

`Server Component` と記載しているものは **SSR** となります。  
App Router ではデフォルトで SSR となり（ `"use server"` とつけてもよい）、CSRには `"use client"` 、 SSG には `generateStaticParams` などを利用します。  
共通レイアウト（全てのルートに適用されるレイアウト）は `app/layout.tsx` 、個別レイアウトは `app/+/layout.tsx` （各パスに対応したディレクトリの `layout.tsx` ）で行います。

### 共通：Dynamic Routes

**Dynamic Routes** は、角括弧 `[]` を使って定義します。  
例えば、

- Pages Router であれば
  - `pages/products/[id].tsx` というファイルは `/products/1` 、 `/products/2`
- App Router であれば
  - `app/products/[id]/page.tsx` というファイルは `/products/1` 、 `/products/2`

などが対応します。

## Link コンポーネント

`next/link` モジュールから提供されている [`Link` コンポーネント](https://nextjs.org/docs/pages/api-reference/components/link)です。  
これは `a` タグの代わりになるもので、 Next.js では基本的に `Link` を使うことになります。  
`Link` を利用することで、バックグラウンドでページをプリフェッチしたり、不要なアセットダウンロードしないなどのメリットが得られます。  
`a` タグの代わりに利用するのではなく、以下のように `a` タグなどにネストして利用します。

```typescript
<Link href="/">
    <a>Home</a>
</Link>
```

## Styling（CSS）

ここでは **CSS Modules** のみについて記載します。  
Next.js では、 `[name].module.css` という命名規則に則ることで、 CSS Modules がサポートされます。  
こちらを利用することで、 `[name]` 部分が異なっていれば、 CSS のクラス名が同一であっても異なるスタイルとしてロードされます。  
なお、クラスセレクタは利用できますが、その他の ID セレクタや属性セレクタなどは利用できません。  
`page.module.css` ファイルにて `.page` クラスが定義されている場合、コンポーネントでの利用は以下のようになります。

```typescript
import styles from "./page.module.css";

export default function Foo() {
  return (
    <p className={styles.page}>foo</p>
  );
}
```

`import` にて `styles` という変数を利用していますが、これは任意の変数名でかまいません。  
また、 TSX/JSX 内でスタイルを適用させる場合、 `className` 属性を利用する必要があります。（ `class` ではないことに注意 ）

## データフェッチ

ToDo

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

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>