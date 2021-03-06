---
title:       "TypeScript環境入門"
URL:         "typescript-environment-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-05-14
author:      "ぺーぺーSE"
image:       ""
tags:
- typescript
- javascript
- node.js
categories:
- tech
---

**TypeScript** について環境構築からトランスパイス、設定ファイルなどをまとめる。

<!--more-->

文法については触れない。また、前提の環境構築（ `npm` など）は以下。

- https://blog.pepese.com/mac-dev-environment/

# 環境構築

```bash
$ npm install -g typescript@latest
$ ndenv rehash$ tsc -v
Version 2.3.2
```

これで `tsc` コマンドを使って TypeScript の `.ts` ファイルをコンパイルできるようになる。

## 実行してみる

以下の `greeter.ts` ファイルを作成する。

```typescript
function greeter(person) {
    return "Hello, " + person;
}

var user = "pepese";

console.log(greeter(user));
```

以下のコマンドでコンパイル〜実行まで。

```bash
$ tsc greeter.ts
$ node greeter.js
Hello, pepese
```

# 設定ファイル

TypeScriptの設定ファイルは `tsconfig.json` 。  
`tsc` コマンドのオプションで様々設定できるが、設定ファイルがあれば省略できる。  


簡単な設定ファイルを作成してみて実行を確認にしてみる。  
カレントディレクトリに以下の **tsconfig.json** を作成して `$ tsc` を実行しみる。

```javascript
{
  "compilerOptions": {
    "baseUrl": "",
    "outDir": "dist",
    "sourceMap": true
  }
}
```

`dist` ディレクトリが作成され、その配下に `greeter.js` と `greeter.js.map` が作成されていることがわかる。

## **tsconfig.json** のパラメータ

```javascript
{
  "compilerOptions": {
  },
  "include": [
    "src/**/*"
  ],
  "exclude": [
    "node_modules",
    "**/*.spec.ts"
  ]
}
```

- compilerOptions
    - [コンパイラ・オプション](https://www.typescriptlang.org/docs/handbook/compiler-options.html)を指定する
    - 省略するとデフォルト値になる
- include / exclude
    - glob風のファイルパターンでコンパイル対象を指定できる
        - `*` は0文字以上の文字にマッチ(ディレクトリ区切りを除く)
        - `?` は1文字以上の文字にマッチ(ディレクトリ区切りを除く)
        - `**/` はサブディレクトリに再帰的にマッチ
    - デフォルトで、`.ts` 、 `.tsx` 、`.d.ts` ファイルが対象で、 `compilerOptions` の `alloJs` パラメータががtrueの場合は `.js` と `.jsx` も対象になる

## compilerOptions のパラメータ

`compilerOptions` 配下の代表的なパラメータは以下。

|パラメータ|説明|
|:---|:---|
|outDir|コンパイルしたコードの出力先。|
|baseUrl|コンパイル対象のファイルのベースディレクトリ。|
|sourceMap| `.map` ファイルを作成するか否か。|
|declaration| `.d.ts` ファイルを作成するか否か。|
|moduleResolution|モジュール参照の解決方法を指定。 `classic` （デフォルト）か `node` 。[参考：モジュール解決](http://js.studio-kingdom.com/typescript/handbook/module_resolution)|
|target|出力するECMAScriptのバージョンを指定。|
|lib|コンパイルに含めるライブラリを指定。 `ES5` 、 `ES6` 、 `ES2015` 、 `ES7` 、 `ES2016` 、 `ES2017` 、 `DOM` などを指定可能。|

`typeRoots` 、 `types` については後述。  

[参考１](http://js.studio-kingdom.com/typescript/project_configuration/compiler_options)、[参考２](https://www.typescriptlang.org/docs/handbook/compiler-options.html)

### 型定義ファイルの指定

型定義ファイルの指定には、 `compilerOptions` 配下の `typeRoots` 、 `types` で設定する。  
デフォルトでは、 `node_modules/@types/` 配下の `.d.ts` ファイルが読み込まれる。  
`typesRoots` が指定されている場合は、それに含まれるパッケージのみが対象となる。（つまり、 `/node_modules/@types/` 配下は読まれない。）  
`types` が指定された場合、一覧に指定されたパッケージのみが含まれる。（ `@types` パッケージの自動インクルードが無効になる）  
以下の場合は、 `node_modules/@types/node` 、 `node_modules/@types/lodash` 、 `node_modules/@types/express` のみが含まれる。

```javascript
{
   "compilerOptions": {
       "types" : ["node", "lodash", "express"]
   }
}
```

[型定義ファイルの取得と使用方法](http://js.studio-kingdom.com/typescript/declaration_files/consumption)

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>