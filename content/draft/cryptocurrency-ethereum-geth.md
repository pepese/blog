---
title:       "Geth入門"
URL:         "cryptocurrency-ethereum-geth"
subtitle:    ""
description: ""
keyword:     ""
date:        2022-04-04
author:      "ぺーぺーSE"
image:       ""
tags:
- cryptocurrency
- ethereum
categories:
- tech
draft:       true
---

Description

<!--more-->

# dApps

分散アプリは、「サーバー側」がイーサリアムネットワーク上に存在する1つ以上の有名なスマートコントラクトであるアプリ。  
[ethereum/dapp-bin](https://github.com/ethereum/dapp-bin)

## dApp クライアント

これらの分散アプリケーションには通常、ユーザーフレンドリーなフロントエンドが含まれます。これは、cliを使用して手動でコントラクトとの間でトランザクションを送信したり、ハッシュとオペコードを使用して手動でリクエストを作成したりしないためです。  
dAppクライアントは、プログラミングで通常この用語を使用するため、文字通り「クライアント」または「フロントエンド」です。ただし、このクライアントは何らかの方法でEthereumブロックチェーン（おそらく他のサービスに加えて）とインターフェースします。世界中のすべてをNodeJSに変換し終わっていないため、これらのクライアントはJavaScriptで記述されていることがよくあります。  
さらに深刻なことに、ほとんどのdAppクライアントは主にJavaScriptで書かれています。なぜなら、それはWebブラウザーで実行でき、誰もがすでにそれらのいずれかを持っているからです。また、既存のツールの優れた状態を利用できるため、Goで頻繁に記述されています。

## dApp ブラウザ

# GethによるEthereum環境の構築

- 参考？
  - https://www.nowhere.co.jp/blog/archives/20210903-30165.html

[Geth](http://github.com/ethereum/go-ethereum) は Go で実装された Ethereum のクライアント。  
以下のような動作が可能。

- etherの採掘
- etherの送金
- スマート・コントラクトの生成
- トランザクションの生成
- ブロックチェーンの確認

[公式Doc](https://geth.ethereum.org/docs/)

## インストール

```
$ brew install ethereum
$ which geth
/usr/local/bin/geth
```

## プライベート・ネットに接続

```
$ mkdir eth_private_net
$ vi eth_private_net/myGenesis.json
```

```
{
  "config": {
    "chainId": 15
  },
  "nonce": "0x0000000000000042",
  "timestamp": "0x0",
  "parentHash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "extraData": "",
  "gasLimit": "0x8000000",
  "difficulty": "0x4000",
  "mixhash": "0x0000000000000000000000000000000000000000000000000000000000000000",
  "coinbase": "0x3333333333333333333333333333333333333333",
  "alloc": {}
}
```

genesisブロックの初期化を行う。

```
$ geth --datadir ./eth_private_net init ./eth_private_net/myGenesis.json
INFO [06-02|20:15:17.591] Maximum peer count                       ETH=50 LES=0 total=50
INFO [06-02|20:15:17.630] Allocated cache and file handles         database=/Users/xxxxx/workspace/eth_private_net/geth/chaindata cache=16.00MiB handles=16
INFO [06-02|20:15:17.671] Writing custom genesis block 
INFO [06-02|20:15:17.671] Persisted trie from memory database      nodes=0 size=0.00B time="47.273µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [06-02|20:15:17.673] Successfully wrote genesis state         database=chaindata hash="7b2e8b…7e0432"
INFO [06-02|20:15:17.673] Allocated cache and file handles         database=/Users/xxxxx/workspace/eth_private_net/geth/lightchaindata cache=16.00MiB handles=16
INFO [06-02|20:15:17.697] Writing custom genesis block 
INFO [06-02|20:15:17.697] Persisted trie from memory database      nodes=0 size=0.00B time="41.042µs" gcnodes=0 gcsize=0.00B gctime=0s livenodes=1 livesize=0.00B
INFO [06-02|20:15:17.698] Successfully wrote genesis state         database=lightchaindata hash="7b2e8b…7e0432"
```

上記を実行すると `--datadir` で指定したディレクトリ以下にディレクトリが新しく作成されて、その中にgenesisブロックのブロックチェーン情報が保存される。

```
$ tree eth_private_net
eth_private_net
├── geth
│   ├── chaindata
│   │   ├── 000001.log
│   │   ├── CURRENT
│   │   ├── LOCK
│   │   ├── LOG
│   │   └── MANIFEST-000000
│   └── lightchaindata
│       ├── 000001.log
│       ├── CURRENT
│       ├── LOCK
│       ├── LOG
│       └── MANIFEST-000000
├── keystore
└── myGenesis.json
```

次のコマンドで Geth を起動する。

```
$ geth --networkid "15" --nodiscover --datadir "./eth_private_net" console 2>> ./eth_private_net/geth_err.log
Welcome to the Geth JavaScript console!

instance: Geth/v1.9.14-stable/darwin-amd64/go1.14.2
at block: 0 (Thu Jan 01 1970 09:00:00 GMT+0900 (JST))
 datadir: /path/to/eth_private_net
 modules: admin:1.0 debug:1.0 eth:1.0 ethash:1.0 miner:1.0 net:1.0 personal:1.0 rpc:1.0 txpool:1.0 web3:1.0

> 
```

オプションは以下の通り。

- `--datadir`
  - gethの動作時のブロックチェーンデータや各種ログの出力先を指定
  - genesisブロックの初期化で指定したディレクトリと同一のものを指定
- `--networkid "15"`
  - 任意の正の整数のIDを指定することで、ライブ・ネットとは異なるネットワークを立ち上げることが可能
  - genesisブロックの初期化で指定したchainidと同一の値を指定する必要あり
- `--nodiscover`
  - Gethはデフォルトで自動的に（同じネットワークID）のEthereumネットワークのノード（Peer）を探し接続を試みる
  - プライベート・ネットでは未知のノードとの接続を避けるため、このオプションで自動Peer探索機能を無効に
- `console`
  - ethには採掘やトランザクションの生成などを対話的に進めることができるコンソールが用意されている
  - consoleサブ・コマンドを指定することで、Gethの起動時に同時にコンソール立ち上げる
    - なお、consoleサブ・コマンドを付加せずに、Gethのプロセスをバックグラウンドで起動させておき、後からそのプロセスのコンソールを起動する事も可能
      - `$ geth --networkid "15" --nodiscover --datadir "./eth_private_net" 2>> ./eth_private_net/geth_err.log &` # バックグラウンドで起動
        - `$ ps -ef | grep geth` などでプロセスの存在を確認
      - `$ geth --datadir "./eth_private_net" attach ipc:./eth_private_net/geth.ipc` # attach

ブロックを参照して Genesis ブロックを確認する。

```
> eth.getBlock(0)
{
  difficulty: 16384,
  extraData: "0x",
  gasLimit: 134217728,
  gasUsed: 0,
  hash: "0x7b2e8be699df0d329cc74a99271ff7720e2875cd2c4dd0b419ec60d1fe7e0432",
  logsBloom: "0x00000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000000",
  miner: "0x3333333333333333333333333333333333333333",
  mixHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
  nonce: "0x0000000000000042",
  number: 0,
  parentHash: "0x0000000000000000000000000000000000000000000000000000000000000000",
  receiptsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  sha3Uncles: "0x1dcc4de8dec75d7aab85b567b6ccd41ad312451b948a7413f0a142fd40d49347",
  size: 507,
  stateRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  timestamp: 0,
  totalDifficulty: 16384,
  transactions: [],
  transactionsRoot: "0x56e81f171bcc55a6ff8345e692c0f86e5b48e01b996cadc001622fb5e363b421",
  uncles: []
}
```

## アカウントの作成

まずは現在のノードのアカウントを確認。

```
> eth.accounts
[]
```

EOAの作成は `personal.newAccount("password")` コマンドで行う。  
なお、 passowrd はpasswdの部分は作成するEOAのパスワード。

```
> personal.newAccount("password")
"0xb1f0d0b92e6d7c76ff4ecc485d8dc5f904f6c3b1"
> eth.accounts
["0xb1f0d0b92e6d7c76ff4ecc485d8dc5f904f6c3b1"]
> personal.listAccounts
["0xb1f0d0b92e6d7c76ff4ecc485d8dc5f904f6c3b1"]
```

続きは [ここ](https://book.ethereum-jp.net/first_use/mining_ether)

# メインネット（Mainnet）への接続

```
$ mkdir mainnet_data
$ geth --datadir "./mainnet_data" 2>> ./mainnet_data/e01.log &
```

## 接続状況の確認

Gethのコンソールを立上げ `net.peerCount` コマンドを実行すると、自分のノードが他のいくつのノードと接続されているかを表示することが出来る。

```
> net.peerCount
25
```

また、実際に接続されているノードの情報は `admin.peers` のコマンドで確認できる。

```
> admin.peers
[{
  Caps: 'eth/60, eth/61',
  ID: '99017abe7031b48a855b8e79fecb6c927cda88229354f21184d343941ae78ee261d0ccb9f9999f620f96bd729b2cb7c4e8cdf3218d71b016fe531ff439b81dcc',
  LocalAddress: '160.16.80.199:34057',
  Name: 'Geth/v1.0.2/linux/go1.4.2',
  RemoteAddress: '192.169.7.150:30303'
}, 
（中略）
{
  Caps: 'eth/60, eth/61',
  ID: '6f8c6cc4a878ed88e03c7a0ee01386e095fbc1bfd48352c7bec05142cc60795317b5b8c5e9afc9863a414541df4d5b1627a86418177857166171fd45497c75ab',
  LocalAddress: '160.16.80.199:41617',
  Name: 'Geth/v1.0.2/linux/go1.4.2',
  RemoteAddress: '83.77.31.183:30303'
} ]
```