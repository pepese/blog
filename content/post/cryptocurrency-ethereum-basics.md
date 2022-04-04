---
title:       "Ethereum入門"
URL:         "cryptocurrency-ethereum-basics"
subtitle:    ""
description: "仮想通貨Ethereumについて紹介します。"
keyword:     "ethereum, ether, eth, 仮想通貨, 暗号資産"
date:        2022-04-04
author:      "ぺーぺーSE"
image:       ""
tags:
- cryptocurrency
- ethereum
categories:
- tech
---

仮想通貨 Ethereum について紹介する。  
なお、技術的な内容であり、値動きやチャートといったことには触れない。

<!--more-->

仮想通貨（暗号資産）については以下を参考に。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/cryptocurrency-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">仮想通貨（暗号資産）入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">仮想通貨（暗号資産）についてブロックチェーンも交えて紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# Ethereumとは？

Ethereum は、トランザクションに基づいて **状態** （ **ステート** ）とその遷移を管理するブロックチェーンと言える。  
Ethereum の分散コンピューターで、ネットワーク内の各ノードはバイトコード（スマートコントラクト）を実行し、結果の状態をブロックチェーンに保存する。  
このステートは、アプリケーションの状態を表すブロックチェーンの特性により、「ダウンタイム、検閲、詐欺、またはサードパーティの干渉の可能性なしにプログラムされたとおりに正確に実行されるアプリケーション」になる。  
Ethereum は、「トランザクションによって逐一ステートが変化する集合体・1 つのマシン ＝ **ワールドステートマシン** 」だと言える。

# 基本概念

## アカウント

Ethereumには、 **アカウント** と呼ばれるオブジェクトが既定されており、 20 Byte のアドレス（例：0x4c84913cc41f2aad9c24d82bfde598c91cdd33d3） により参照される。  
アカウントは主に次の４つのフィールドを持つ。

- nonce：そのアカウントが送信した累積トランザクション数
- ether balance：そのアカウントが所有するether量
- contract code：コントラクト・コード（EOAの場合は空）
- storage：そのアカウントが保持する任意のデータ

アカウントには ２ つのタイプが存在する。

- Externally Owned Account（EOA）
  - ユーザに作成・管理されるアカウント
  - ユーザの任意のタイミングでトランザクションを生成し、他EOAへのetherの送金、新しい Contract の生成やスマートコントラクトの実行を行う
- Contract
  - EOA からトランザクションを介して生成されるアカウント
  - Contract は一種の自動エージェントであり、EOA が発信するトランザクションをトリガにスマートコントラクトを実行する

## トランザクション

Ethereum では EOA から任意のタイミングでトランザクションを送信することで各アカウントの状態が変化する。  
EOA がトランザクションを生成して Ethereum ネットワーク上に送信し、採掘者（マイナー）は受信したトランザクションの正当性をチェックして問題なければ、そのトランザクションの情報とトランザクションの内容に基づいて変化した最新のアカウントの状態をブロックチェーンに保存する。  
トランザクションには主に以下の情報が含まれる。

- ether 送金額
- 相手先アドレス
- 送信アカウント署名
- 任意データ
  - トランザクションの相手先が Contract である場合に、スマートコントラクトのインプットとして利用
- STARTGAS 値
  - トランザクション手数料として支払うetherの量を規定する情報で、 Gas という単位で計算される
  - トランザクションで支払う最大の Gas 量
- GASPRICE 値
  - トランザクション手数料として支払うetherの量を規定する情報で、 Gas という単位で計算される
  - Gas と etherとの交換レート
  - 「STARTGAS 値 × GASPRICE 値」の量のetherが前払い手数料となる

## スマートコントラクト

スマートコントラクトとは、Ethereum プラットフォームのような分散環境で実行されるバイトコード。  
スマートコントラクトの開発は Solidity などのプログラミング言語とバイトコードへのコンパイラを使用して行う。  
スマートコントラクトの実行は、トランザクションを通じて行う。

## ether

Ethereum では、 **ether** という独自の内部通貨が規定されている。  
Ethereum 内で分散アプリケーションやスマートコントラクトを実行するための「燃料」としての役割も果たす。  
内部通貨 ether の単位は通貨名と同じで ether（ETH）だが、etherも利便性のために、より少額のetherに対して下記のように単位名が決められている。

- 1 ether = 1,000 finney
- 1 ether = 1,000,000 szabo
- 1 ether = 1,000,000,000,000,000,000 wei

## Gas

直感的には、取引手数料。  

スマートコントラクトはネットワーク内のあらゆるのノードで実行される。  
計算の実行にはコストがかかるため、計算の実行コストはバイトコードが実行する計算に直接関係している。  
バイトコードの実行は、後述の EVM で行われ、実行するために一定量の **Gas** がかかる。  
これは、計算を実行するコストに与えられた単なる抽象的なラベルで、ガスの「価格」は、ビットコインの取引手数料と同様に市場によって決定される。  
より高いガス価格を支払うと、ノードは利益のためにその取引を優先する。  
[Calculating Costs in Ethereum Contracts](https://hackernoon.com/ether-purchase-power-df40a38c5a2f)

## EVM

Ethereum は **EVM** （Ethereum Virtual Machine）と呼ばれるプログラム実行環境を持っており、バイトコード（スマートコントラクト）を実行する。  
Ethereum ネットワーク上のすべてのノードは、この EVM のコピーを保持する。  
バイトコードを実行すると、この EVM の状態が変化する。  

すべてのユーザは、ノードからコード実行要求をブロードキャストすることにより、コードの実行を要求できる。  
Ethereum ネットワークは、すべての Ethereum ノードとその通信の集合体といえる。

## ステート

Ethereumのステートは大きく **ワールドステート** （World State）と **アカウントステート** （Account State）の 2 つに分けられる。  

### ワールドステート

Ethereum アドレスから アカウントステートへのマッピング。

### アカウントステート

先に説明したアカウントオブジェクトのステートを持つ。

### トランザクションによる状態遷移

トランザクション実行時に EVM のインスタンスが作成され、その中でコントラクトのバイトコードを実行する。  
Ethereum への ステートの反映は EVM インスタンスでトランザクションを完遂して初めて行われ、途中で Gas 不足などによりトランザクションが中断した場合は Ethereum へのステート反映は行われることはない。

## トークン

Ethereum ではトークンを発行することができ、 ether と ERC 規格の 2 種類がある。

### ether（ETH）

ether（ETH）は「決済や送金の際に使用されるトークン」であり、また、取引所においては「仮想通貨イーサリアム」である。

### ECRトークン

ERC は「Ethereum Request for Comments」の頭文字からきており、イーサリアムによるトークンを発行するための規格のこと。  
ERC20・ERC223・ERC721・ERC948・ERC1155 など様々規格があり、中でももっとも多く使われているのが ERC20 。  
イーサリアム（ETH）や、イーサリアムクラシック（ETC）もERC20トークンのひとつ。  
他にも ERC20 規格を使って作成された暗号資産は多くが存在し、以下のようなものがある。

- テザー（USDT）
- チェーンリンク（LINK）
- バイナンスコイン（BNB）
- USDコイン（USDC）
- ダイ（DAI）

# 参考

- [イーサリアム開発者リソース](https://ethereum.org/ja/developers/)

# おすすめ書籍

<!-- amazon affiliate kindle ethereum --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B07NNFLQ4D&linkId=3115e2822dba130a5e9a10d1d262f003"></iframe>