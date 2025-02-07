---
title:       "React入門"
URL:         "nodejs-react-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2024-12-27
author:      "ぺーぺーSE"
image:       ""
tags:
- react
- typescript
- javascript
- node.js
categories:
- tech
---

[React](https://ja.react.dev/) についてまとめる。

<!--more-->

# React とは？

React とは以下の特徴を持つ UI ライブラリです。

- 宣言的な View
  - アプリケーションの各状態に対応するシンプルな View を設計するだけで、データの変更を検知し、関連するコンポーネントだけを効率的に更新、描画します
- コンポーネントベース
  - UI を再利用可能なコンポーネントという形でモジュール化します
- 様々なエコシステム
  - 例えば、 React は React Native といった様々なプラットフォームでアプリケーションを実装できるテクノロジのベースとなります


# 環境構築

Vite を利用して以下のようにプロジェクトを構築できます。  
なお、 create-react-app は 2023 年春頃からメンテナンスが止まっていますので、今後は Vite を利用しましょう。

- [Vite入門](https://blog.pepese.com/nodejs-vite-basics/)

```bash
$ npm create vite@latest react-sample -- --template react-ts
> npx
> create-vite react-sample --template react-ts

Scaffolding project in /Users/xxxx/workspace/react-sample...

Done. Now run:

  cd react-sample
  npm install
  npm run dev
$ cd react-sample
$ npm install
```

# 主要機能

本記事では React が有する主要な以下の機能を紹介します。  
全ての機能を紹介するわけではありませんので、詳細は公式ドキュメントをご参照ください。

- コンポーネント
- Props
- ステートとライフサイクル
- カスタムフック

## コンポーネント

**コンポーネント** とは、React の画面表示に利用する部品のことで、表示に必要なデータや処理などを 1 つにまとめたものです。  
コンポーネントには以下の 2 種類があります。

- クラスコンポーネント
- 関数コンポーネント

2 種類あるのですが、これらは歴史的な背景で誕生した部分があり、どちらかを状況によって選択するというよりかは、今後主流になると思われる **関数コンポーネント** を本記事では扱います。（以降、関数コンポーネントを単にコンポーネントと呼びます。）  
コンポーネントは TSX/JSX を用いて、以下のように記述します。（以下は TSX）

```tsx
export default function Hoge() {
    /* 何かしらの処理 */
    return(
        /* View：HTMLタグや子コンポーネント */
        <>
            <p>Hello, World!</p>
            <Fuge />
        </>
    )
}
```

関数名はキャメルケース（最初を大文字）で記載します。  
`export` することで、他のコンポーネントから `<Hoge />` タグとして利用することができます。  
`export` は、 `export` でも `export default` でもどちらでもかまいませんが、慣例として、 1 度しか呼び出されない親コンポーネントは `export default` 、 子コンポーネントとして複数呼び出される場合は `export` とするとよいでしょう。  
`return` で返す View は必ず 1 つのタグで閉じられている必要があります。  
困ったときは `<dev>...</dev>` タグやあるいは **フラグメント** `<>...</>` で閉じましょう。  
フラグメントは `<React.Fragment>...</>` の省略形で、描画時に HTML タグとして扱われず出力されません。

## Props

Props は Properties の略で、コンポーネントの引数、コンポーネントをタグで使った場合の属性になります。  
例えば、 `Fuge` コンポーネントのタグを以下のように記載したとします。

```tsx
<Fuge name="fuge" age=20 />
```

そして、 `Fuge` コンポーネントに引数を設定すると、属性値を取得することができます。

```tsx
export function Fuge(props) {
    console.log(props.name) // -> hoge
    console.log(props.age)  // -> 20
    return(
        ...
    )
}
```

`props` という変数名は慣例によるものなので、任意の変数名にすることができます。  
TypeScript なので、型定義はした方がいいですね

```tsx
export function Fuge(props: { name: string, age: number }) {
    console.log(props.name) // -> hoge
    console.log(props.age)  // -> 20
    return(
        ...
    )
}
```

属性に設定できる値の型には以下のようなものがあります。

- 文字列： `name="fuge"`
- 数値： `age={20}`
- 配列： `array={[1,2,3]}`
- オブジェクト： `obj={{foo: "foo", bar: 1}}`
- 真偽値： `boo={true}` 、 `boo={false}`
  - なお、 `true` の場合は変数名だけで代入を省略できる： `boo`
- TSX/JSX： `comp={<div>hoge</div>}`
  - `React.Component` 型としてわたされる
- 関数： `func={() => alert("hoge")}`

また、 Props では、属性の他に、タグの入れ子部分を取得することができ、これを **children** といいます。

```tsx
<Fuge name="fuge" age=20> チルドレン </Fuge>
```

```tsx
export function Fuge(props: { name: string, age: number, children: React.ReactNode }) {
    console.log(props.name) // -> hoge
    console.log(props.age)  // -> 20
    return(
        ...
    )
}
```

children の変数名は `children` となっており、 string 型でも取得できますが、入れ子にするタグは単純な文字列ではありませんので、通常は `React.ReactNode` 型になります。

## ライフサイクル

ライフサイクルを説明するにあたって [こちら]() のリポジトリの画像をおかります。

![React Hooks Lifecycle](https://raw.githubusercontent.com/Wavez/react-hooks-lifecycle/master/screenshot.jpg)

ライフサイクルの流れは以下の通りです。

- 縦に「レンダーフェーズ（Render phase）」、「コミットフェーズ（Commit phase）」、「クリーンナップフェーズ（Cleanup phase）」があります
- 「レンダーフェーズ（Render phase）」では横に、「マウント（Mounting）」「更新（Updating）」があります
- 「レンダー（Render）」は「Reactがコンポーネントを呼び出されて（再）レンダリングされる」ことを指しますので、「マウント（Mounting）」「更新（Updating）」にて実行されます
- 「レンダー（Render）」がトリガーされるタイミングが以下の 2 種類です
  - アプリが開始してコンポーネントの初回のレンダーが行われるとき（これを「マウント（Mounting）」といいます）
  - コンポーネントのステート（ state ）が更新されるとき
    - なお、親コンポーネントのステート（ state ）が更新し、 Props 変化が発生した際、子コンポーネントのステート（ state ）も更新したとみなされます
- 「レンダーフェーズ（Render phase）」ではデータは作成・更新されますが、画面に描画はされません
- 「コミットフェーズ（Commit phase）」で実際に画面描画が行われます
- 「コミットフェーズ（Commit phase）」で画面描画が完了した後に、`useEffect()` ・ `useLayoutEffect()` が実行されます
- 「更新（Updating）」の場合は、`useEffect()` ・ `useLayoutEffect()` が **実行されまる前に以前のステート（ state ）の値で**　クリーナップが実行されます（「クリーンナップフェーズ（Cleanup phase）」）
- なお、「クリーンナップフェーズ（Cleanup phase）」ですが、上記の図にはありませんが、アンマウント（Unounting）、つまり、コンポーネントが破棄される際にも実行されます

初回の「マウント（Mounting）」はいいとして、ステート（ state ）が更新されるとき、つまり「更新（Updating）」が実行されるのはいつになるのでしょうか？  
そして、そもそもステート（ state ）とは何なのでしょうか？

### [`useState()`](https://ja.react.dev/reference/react/useState)

ステート（ state ）とは、 `useState()` で定義された state 変数を指し、これが変化した際に「更新（Updating）」が発生します。

```bash
const [state, setState] = useState(initialState)
```

- `state`
  - state 変数、これがステート（ state ）
- `setState`
 - state 変数を別の値に更新し、「更新（Updating）」をトリガする set 関数
 - 引数内に state 変数を更新する処理を行う関数を定義する
 - 慣例として、 state 変数名が `something` だった場合 `setSomething` と命名する
- `initialState`
  - state 変数の初期値
  - どんな型の値でも可能
  - 関数を渡した場合、その関数は必ず return する必要がある
- `useState()`
  - `useState()` 関数は以下の 2 つの値を持つ配列を返します
    - 現在のステート（ state ）、 `initialState` と同じ値
    - state 変数を別の値に更新し、「更新（Updating）」をトリガする set 関数

`useState()` を利用する場合、以下のルールを満たす必要があります。

- コンポーネント内、または、カスタムフック内で呼び出す
- 関数のトップレベルから呼び出す（ `if` 文などの中で呼び出さない）

以下にボタンをクリックするするとインクリメントするコンポーネントの例を記載します。

```tsx
export default function Increment() {
  const [count, setCount] = useState(0); // 初期値 0 のステート変数 count、set 関数 setState() を定義
  const increment = () => setCount((count) => count + 1); // ボタンが押されたがステートを変更する（ + 1）

  return(
    <button onClick={increment}>{count}</button>
  )
}
```

### [`useEffect()`](https://ja.react.dev/reference/react/useEffect)

ライフサイクルの様々なタイミングで実行される処理を記載するなかなかややこしい機能です。  
以下のように引数に関数を定義して利用します。

```tsx
useEffect(() => {
  // レンダーフェーズ（Render phase）（マウント（Mounting）、更新（Updating））に実行される処理
  return() => {
    // クリーンナップフェーズ（Cleanup phase）あるいは、アンマウント（Unounting）に実行される処理
  }
}, 第2引数);
```

こちらも `useState()` 同様、以下のルールを満たす必要があります。

- コンポーネント内、または、カスタムフック内で呼び出す
- 関数のトップレベルから呼び出す（ `if` 文などの中で呼び出さない）

`useEffect()` は、第2引数に指定されるものによって挙動が以下の①〜③のように異なりますので注意が必要です。

```tsx
// ①第2引数を省略した場合
useEffect(() => {
  // レンダーフェーズ（Render phase）（マウント（Mounting）、更新（Updating））の度に実行される
  return() => {
    // クリーンナップフェーズ（Cleanup phase）、あるいは、アンマウント（Unounting）の度に実行される
  }
});

// ②第2引数を空の配列にした場合
useEffect(() => {
  // 初回のマウント（Mounting）時、 1 回のみ実行される
  return() => {
    // 最終のアンマウント（Mounting）時、 1 回のみ実行される
  }
}, []);

// ③第2引数に state 変数を代入した場合（配列なので、いくつ入れても大丈夫）
useEffect(() => {
  // 初回マウント（Mounting）時、および、第2引数の state 変数が更新（Updating）される度に実行される
  return() => {
    // 第2引数の state 変数が更新（Updating）されたクリーンナップフェーズ（Cleanup phase）、あるいは、アンマウント（Unounting）に実行される処理
  }
}, [count]);
```

余談ですが、動作確認を実施している中で初回に 2 回レンダリングされて不思議に思っている型がいらっしゃいましたら、StrictModeをオフにしてみてください。  
React v18 より、StrictModeでは初回レンダリングが 2 回実行される仕様となったようです。

## カスタムフック

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