---
title:       "Ethereum入門"
URL:         "cryptocurrency-ethereum-basics"
subtitle:    ""
description: "Ethereumは代表的な仮想通貨の1つです。この記事では、Ethereumの値動きやチャートなどではなく、エンジニア向けの技術的な内容について記載します。"
keyword:     "Ethereum, ether, eth, 仮想通貨, 暗号資産"
date:        2022-04-04
author:      "ぺーぺーSE"
image:       ""
tags:
- cryptocurrency
- ethereum
categories:
- tech
---

Ethereumは代表的な仮想通貨の1つです。  
この記事では、Ethereumの値動きやチャートなどではなく、エンジニア向けの技術的な内容について記載します。

<!--more-->

仮想通貨（暗号資産）については以下を参考にしてください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/cryptocurrency-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">仮想通貨（暗号資産）入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">仮想通貨（暗号資産）についてブロックチェーンも交えて紹介します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# Ethereumとは？

Ethereum は、トランザクションに基づいて **状態** （ **ステート** ）とその遷移を管理するブロックチェーンと言えます。  
Ethereum の分散コンピューターで、ネットワーク内の各ノードはバイトコード（スマートコントラクト）を実行し、結果の状態をブロックチェーンに保存します。  
このステートは、アプリケーションの状態を表すブロックチェーンの特性により、「ダウンタイム、検閲、詐欺、またはサードパーティの干渉の可能性なしにプログラムされたとおりに正確に実行されるアプリケーション」になります。  
Ethereum は、「トランザクションによって逐一ステートが変化する集合体・1 つのマシン ＝ **ワールドステートマシン** 」だと言えます。

# 基本概念

## アカウント

Ethereumには、 **アカウント** と呼ばれるオブジェクトが既定されており、 20 Byte のアドレス（例：0x4c84913cc41f2aad9c24d82bfde598c91cdd33d3） により参照されます。  
アカウントは主に次の４つのフィールドを持ちます。

- nonce：そのアカウントが送信した累積トランザクション数
- ether balance：そのアカウントが所有するether量
- contract code：コントラクト・コード（EOAの場合は空）
- storage：そのアカウントが保持する任意のデータ

アカウントには ２ つのタイプが存在します。

- Externally Owned Account（EOA）
  - ユーザに作成・管理されるアカウント
  - ユーザの任意のタイミングでトランザクションを生成し、他EOAへのetherの送金、新しい Contract の生成やスマートコントラクトの実行を行います
- Contract
  - EOA からトランザクションを介して生成されるアカウント
  - Contract は一種の自動エージェントであり、EOA が発信するトランザクションをトリガにスマートコントラクトを実行します

## トランザクション

Ethereum では EOA から任意のタイミングでトランザクションを送信することで各アカウントの状態が変化します。  
EOA がトランザクションを生成して Ethereum ネットワーク上に送信し、採掘者（マイナー）は受信したトランザクションの正当性をチェックして問題なければ、そのトランザクションの情報とトランザクションの内容に基づいて変化した最新のアカウントの状態をブロックチェーンに保存します。  
トランザクションには主に以下の情報が含まれます。

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

スマートコントラクトとは、Ethereum プラットフォームのような分散環境で実行されるバイトコードです。  
スマートコントラクトの開発は Solidity などのプログラミング言語とバイトコードへのコンパイラを使用して行います。  
スマートコントラクトの実行は、トランザクションを通じて行われます。

## ether

Ethereum では、 **ether** という独自の内部通貨が規定されています。  
Ethereum 内で分散アプリケーションやスマートコントラクトを実行するための「燃料」としての役割も果たします。  
内部通貨 ether の単位は通貨名と同じで ether（ETH）だが、etherも利便性のために、より少額のetherに対して下記のように単位名が決められています。

- 1 ether = 1,000 finney
- 1 ether = 1,000,000 szabo
- 1 ether = 1,000,000,000,000,000,000 wei

## Gas

直感的には、取引手数料です。  

スマートコントラクトはネットワーク内のあらゆるのノードで実行されます。  
計算の実行にはコストがかかるため、計算の実行コストはバイトコードが実行する計算に直接関係しています。  
バイトコードの実行は、後述の EVM で行われ、実行するために一定量の **Gas** がかかります。  
これは、計算を実行するコストに与えられた単なる抽象的なラベルで、ガスの「価格」は、ビットコインの取引手数料と同様に市場によって決定されます。  
より高いガス価格を支払うと、ノードは利益のためにその取引を優先します。  
[Calculating Costs in Ethereum Contracts](https://hackernoon.com/ether-purchase-power-df40a38c5a2f)

## EVM

Ethereum は **EVM** （Ethereum Virtual Machine）と呼ばれるプログラム実行環境を持っており、バイトコード（スマートコントラクト）を実行します。  
Ethereum ネットワーク上のすべてのノードは、この EVM のコピーを保持します。  
バイトコードを実行すると、この EVM の状態が変化します。  

すべてのユーザは、ノードからコード実行要求をブロードキャストすることにより、コードの実行を要求できます。  
Ethereum ネットワークは、すべての Ethereum ノードとその通信の集合体といえます。

## ステート

Ethereumのステートは大きく **ワールドステート** （World State）と **アカウントステート** （Account State）の 2 つに分けられます。  

### ワールドステート

Ethereum アドレスから アカウントステートへのマッピングです。

### アカウントステート

先に説明したアカウントオブジェクトのステートを持ちます。

### トランザクションによる状態遷移

トランザクション実行時に EVM のインスタンスが作成され、その中でコントラクトのバイトコードを実行します。  
Ethereum への ステートの反映は EVM インスタンスでトランザクションを完遂して初めて行われ、途中で Gas 不足などによりトランザクションが中断した場合は Ethereum へのステート反映は行われることはありません。

## トークン

Ethereum ではトークンを発行することができ、 ether と ERC 規格の 2 種類があります。

### ether（ETH）

ether（ETH）は「決済や送金の際に使用されるトークン」であり、また、取引所においては「仮想通貨イーサリアム」です。

### ECRトークン

ERC は「Ethereum Request for Comments」の頭文字からきており、イーサリアムによるトークンを発行するための規格のことです。  
ERC20・ERC223・ERC721・ERC948・ERC1155 など様々規格があり、中でももっとも多く使われているのが ERC20 です。  
イーサリアム（ETH）や、イーサリアムクラシック（ETC）もERC20トークンのひとつです。  
他にも ERC20 規格を使って作成された暗号資産は多くが存在し、以下のようなものがあります。

- テザー（USDT）
- チェーンリンク（LINK）
- バイナンスコイン（BNB）
- USDコイン（USDC）
- ダイ（DAI）

# 参考

- [イーサリアム開発者リソース](https://ethereum.org/ja/developers/)

# おすすめ書籍

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

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>