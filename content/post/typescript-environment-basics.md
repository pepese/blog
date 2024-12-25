---
title:       "TypeScript環境入門"
URL:         "typescript-environment-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2024-12-25
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

文法については触れない。また、前提の環境構築（ Node.js、npm ）は以下を参照。

- [Node.js・npm入門](https://blog.pepese.com/nodejs-npm-basics/)

# 環境構築

```bash
$ node -v
v23.5.0
$ npm install -g npm
$ npm -v
11.0.0
$ npm install -g typescript
$ tsc -v
Version 5.7.2
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

## TypeScript プロジェクトの作成

まずは、プロジェクトのルートディレクトリで以下を実行すると `package.json` が作成されます。

```bash
$ npm init
This utility will walk you through creating a package.json file.
It only covers the most common items, and tries to guess sensible defaults.

See `npm help init` for definitive documentation on these fields
and exactly what they do.

Use `npm install <pkg>` afterwards to install a package and
save it as a dependency in the package.json file.

Press ^C at any time to quit.
package name: (typescript-sample)
version: (1.0.0)
description:
entry point: (index.js)
test command:
git repository:
keywords:
author:
license: (ISC)
type: (commonjs)
About to write to /Users/tanakakns/workspace/temp/typescript-sample/package.json:

{
  "name": "typescript-sample",
  "version": "1.0.0",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "",
  "license": "ISC",
  "type": "commonjs",
  "description": ""
}


Is this OK? (yes) yes
```

これで Node.js プロジェクトが作成されたので、以下を実行して TypeScript プロジェクトにします。

```
$ tsc --init

Created a new tsconfig.json with:
                                                                             TS
  target: es2016
  module: commonjs
  strict: true
  esModuleInterop: true
  skipLibCheck: true
  forceConsistentCasingInFileNames: true


You can learn more at https://aka.ms/tsconfig
```

`tsconfig.json` が作成されます。

# 設定ファイル

TypeScriptの設定ファイルは `tsconfig.json` 。  
`tsc` コマンドのオプションで様々設定できるが、設定ファイルがあればオプションを省略できる。  


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