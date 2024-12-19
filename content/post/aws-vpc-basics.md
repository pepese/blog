---
title:       "Amazon VPC入門"
URL:         "aws-vpc-basics"
subtitle:    ""
description: "Amazon VPC は、 AWS が提供する仮想ネットワークサービスです。この記事では、Amazon VPC をざっくり理解できる概要と作成例について記載します。"
keyword:     "AWS, VPC"
date:        2024-12-19
author:      "ぺーぺーSE"
image:       ""
tags:
- aws
- vpc
categories:
- tech
---

Amazon VPC は、 AWS が提供する仮想ネットワークサービスです。  
この記事では、Amazon VPC をざっくり理解できる概要と作成例について記載します。

<!--more-->

# VPC

VPC は AWS アカウント内に作成する仮想的なネットワークです。  
会社や自宅のLANみたいなものだと思って差し支えありません。  
作成にあたり、以下の注意点があります。

- ネットマスクは Classless Inter-Domain Routing (CIDR) ブロック「**/16**」～「**/28**」の範囲です
   - 基本は「**/16**」で大丈夫です
- 一度作成したVPCはサイズ変更不可能です

IP アドレスの範囲指定に迷ったときは、 [RFC 1918](http://www.faqs.org/rfcs/rfc1918.html) に準拠することをお勧めします。

- `10.0.0.0` - `10.255.255.255` : 10/8 prefix
- `172.16.0.0` - `172.31.255.255` : 172.16/12 prefix
- `192.168.0.0` - `192.168.255.255` : 192.168/16 prefix

## テナンシー

1 つの物理サーバを1ユーザで専有することを**シングルテナンシー**、複数ユーザで共有することを**マルチテナンシー**といいます。  
VPC内に作成されるインスタンスを専用テナンシーインスタンスにするには「ハードウェア専有」を、VPC内に作成されるインスタンスを作成時に指定されたテナンシー属性を使用するには「デフォルト」を選択します。  
基本的には「デフォルト」で大丈夫です。  

シングルテナンシーで作成した **Dedicated Host** は以下の事ができるようになります。

- 「ライセンス持ち込み(Bring Your Own Licenses)」
  - サーバ単位の既存のライセンスであるWindows Server, SQL Server, SUSE Linux Enterprise Serverやその他のエンタープライズシステムや製品を、クラウドに持ち込めます
  - Dedicated Hostのソケット数や物理コア数を確認することができますので、実際のハードウェアに適するソフトウェアライセンスを取得・使用できます
- 「コンプライアンスや規制への準拠」
  - Dedicated Hostを配置することで、ユーザが完全に専有するハードウェア上で、アプリケーションを実行することができます
- 「利用の追跡」
  - AWS Configを使うことで 、各Dedicated Host上にあるインスタンスの開始・停止といった利用履歴を追跡することができます
  - このデータは、ライセンスメトリクスの利用量との確認に使えます
- 「インスタンス配置の制御」
  - 各Dedicated Host上のEC2インスタンスの配置を、細かい粒度で制御できます

# サブネット

VPC 内に作成する仮想的なサブネットです。  
1 つのサブネットは 1 つの **アベイラビリティゾーン**（AZ）に所属します。  
作成にあたり、以下の注意点があります。

- サブネットで指定したIPアドレスの範囲のうち、最初の4つと最後の1つは使用できません
  - 10.0.0.0/24 のサブネットを例にすると、予約されているアドレスは以下の 5 つで、利用可能なIPアドレスは 251 個です
    - 10.0.0.0（ネットワークアドレス）
    - 10.0.0.1（VPC ルーター）
    - 10.0.0.2（DNS へのマッピング用）
    - 10.0.0.3（将来の利用のためにAWSで予約）
    - 10.0.0.255 （ネットワークブロードキャストアドレス）
- サブネットのネットマスクにCIDR「**/27**」以上をつけましょう
  - ELB を設置するサブネットには、サブネット全体で **8個** 以上の IP アドレスの余りが必要です
  - サブネット内に ELB 以外にも EC2 インスタンスなどを配置することができますが、無計画にEC2を稼働させると IP が不足して ELB のスケールアウトができなくなります
- 1 つのVPCに作成できるサブネットは 200 個
- すべてのサブネットは仮想敵なルータ（ **ルートテーブル** と **ネットワークACL** ）が設定されている必要があります

# ルートテーブル

サブネットに設定する仮想的なルーターです。  
作成にあたり、以下の注意点があります。

- ルートテーブルは個々のサブネットに 1 つずつ設定できます
- 1 つのルートデーブルを複数サブネットに適用できますが、 1 つのサブネットに複数ルートテーブル設定できません
- 宛先アドレス（ Destination ）とターゲットとなるゲートウェイ・ネクストホップ（ Target ）を設定します

デフォルトのルートテーブルに **メインルートテーブル**（vpc内からvpc内へのルーティング）  
「Destination：10.0.0.0/16(vpc)」 -「Target：local」  
が設定されています。  
送信先が「10.0.0.0/16(vpc)」のリクエストをルータがキャッチした場合「local」へルーティングする設定です。  
VPC内の該当サブネットからインターネットへ接続したい場合、  
「Destination：0.0.0.0/0」-「Target：インターネットゲートウェイ」  
を設定する必要があります。  
作成にあたり、以下の注意点があります。

- ルートテーブルでは上の定義が優先されます
- VPC/サブネットからインターネットにアクセスするためには **パブリックサブネット** である必要があります

## **プライベートサブネット**と**パブリックサブネット**  

設定項目としてこれらが VPC に備わっているのではなく、そのサブネットのルートテーブルの設定によってこれが決まります。  
ルートテーブルで「 0.0.0.0/0 」がインターネットゲートウェイに流れるような設定になっているのが「パブリックサブネット」、そうではないのが「プライベートサブネット」です。  
プライベートサブネットがインターネットにアクセスするには後述の NAT ゲートウェイへルーティングする必要があります。

# セキュリティ

VPCのセキュリティ設定として **ネットワークACL** と **セキュリティグループ** があります。

## ネットワークACL

ネットワークアクセスコントロールリスト（ **ネットワークACL** ）は、サブネットの仮想ファイアウォールとして機能し、インバウンドトラフィックとアウトバウンドトラフィックを **ブラックリスト** 式で制御します。  
ネットワークACLがサブネットのインバウンド・アウトバウンドのトラフィックを制御するのに対し、セキュリティグループはインスタンス単位のインバウンド・アウトバウンドのトラフィックを制御します。  
**ステートレス Firewall** なので **戻りのトラフィックも考慮する必要があります** 。

## セキュリティグループ

**セキュリティグループ**は、インスタンスの仮想ファイアウォールとして機能し、インバウンドトラフィックとアウトバウンドトラフィックを **ホワイトリスト** 式で制御します。  
VPC 内でインスタンスを起動した場合、そのインスタンスは最大 **5** つのセキュリティグループを割り当てることができます。  
セキュリティグループは、サブネットレベルでなくインスタンスレベルで動作します。  
このため、 サブネット内のインスタンス毎に異なるセキュリティグループのセットに割り当てることができます。  
起動時に特定のグループを指定しないと、インスタンスは VPC のデフォルトのセキュリティグループに自動的に割り当てられます。  
**ステートフル Firewall** なので 戻りのトラフィックは考慮する必要はありません。

## VPC Flow logs

VPC Flow logs は以下の機能を提供します。

- ネットワークトラフィックをキャプチャし、 CloudWatch Logs へ Publish する機能
- セキュリティグループとネットワーク ACL のルールで accept/reject されたトラフィックログを取得
- ネットワークインターフェースを送信元 / 送信先とするトラフィックが対象
- キャプチャウインドウと呼ばれる時間枠（ 約 10 分）で収集・プロセッシング・保存
- RDS 、 RedShift 、 ElastiCache 、 WorkSpaces のネットワークインターフェースのトラフィックも取得可能
- Flow logs データは Amazon CloudWatch Logs と Amazon S3 に保存・閲覧・取得可能
- VPC Flow logs は **GuardDuty** による脅威の検知と通知が可能

# ゲートウェイ

## インターネットゲートウェイ（ **IGW** ）

VPC からインターネットに接続するためのゲートウェイです。  
似た単語に「カスタマーゲートウェイ」「仮想プライベートゲートウェイ」がありますが、これらは VPN 接続するとき以外は考慮する必要はありません。

## NATゲートウェイ

サブネット内に作成する NAT の仕組みです。 EIP を設定する必要があります。
「サブネット内」というところがポイントで、マルチ AZ で冗長化されていないので、サブネット毎に作成する必要があります。  
IPv4 の場合は NATゲートウェイ、 IPv6 の場合は[Egress-Only インターネットゲートウェイ](https://docs.aws.amazon.com/ja_jp/vpc/latest/userguide/egress-only-internet-gateway.html)を利用します。

# DNS / NTP

## Amazon Provided DNS

VPC で用いることのできる DNS サーバです。  
デフォルトでは VPC 内から名前解決を行う時はこのサーバを用いる。自身で構築する必要はありません。  
VPC のインターネットゲートウェイを介して通信する必要があるインスタンスで利用できる DNS で、VPC が CIDR ブロック `10.0.0.0/16` ネットワークの場合 DNS サーバーの位置は `10.0.0.2` となります。（もしくは `169.254.169.253` ）
VPC 内のインスタンスからのみ参照可能で、 VPN や専用線経由では参照できません。  
以下の設定が可能です。

- Enable DNS resolution
  - 基本に Yes
  - No にすると DNS 機能が無効になります
- Enable DNS hostname
  - True にすると DNS 名が割り当てられます

## DHCPオプションセット

VPCで使われる DHCP サービスの定義を行うものです。  
独自のDNS・NTP・NetBIOSサーバを構築・設定したい場合に使用します。  
以下の設定が可能です。  

- Name Tag
  - 定義を識別する名前
- Domain Name
  - DHCPで配布するDNSドメイン名
  - デフォルトだとリージョンが判別可能なものが付けられる
  - Windowsの場合DNSサフィックスとして設定され、Linuxの場合resolv.confのsearchに設定される
- Domain name servers
  - DHCPで配布するDNSサーバのIPアドレスを指定する、最大4つ
  - Windowsの場合参照先DNSサーバとして設定され、Linuxの場合resolv.confのnameserverに設定される
- NTP servers
  - DHCPで配布するNTPサーバのIPアドレスを指定する、最大4つ
- NetBIOS name servers
  - DHCPで配布するNetBIOSネームサーバ(WINSサーバ)のIPアドレスを指定する、最大4つ
- NetBIOS node type
  - DHCPで配布するNetBIOSのノードタイプを指定する
  - 1、2、4、8から指定する

## Amazon Time Sync Service

VPC 内で稼働する全てのインスタンスから利用できる NTP サーバです。（ `169.254.169.123` ）  
自身で構築する必要はありません。

# Elastic IP (EIP)

ネットワークインターフェースサービスである ENI の IP は以下のようになっています。

- プライベート IP
  - VPC でインスタンスを起動すると、サブネットのアドレス範囲内のプライマリプライベートIPアドレスがインスタンスのデフォルトのネットワークインターフェイスに割り当てられます
- パブリック IP
    - すべてのサブネットに、そのサブネット内で起動されるインスタンスがパブリックIPアドレスを受け取るかどうかを決定する属性があり、デフォルトサブネットでは trueに、その他のサブネットでは false に設定されています
    - パブリック IP アドレスはデフォルトのネットワークインターフェイス（eth0）に割り当てられます
    - パブリック IP アドレスとインスタンスとの関連付けを解除すると、そのアドレスは解放されて使用できなくなります

固定のパブリック IP アドレスが必要な場合は、 **EIP** アドレスを使用します。

## **Elastic Network Interface (ENI)**

ENIは、VPC のインスタンスにアタッチできる仮想ネットワークインターフェイスです。  
ENIには以下の属性を含めることができます。

- プライマリプライベート IP アドレス
- 1 つ以上のセカンダリプライベート IP アドレス
- プライベート IP アドレス毎に 1 つの Elastic IP アドレス
- 1 つのパブリック IP アドレス。
  - eth0 のElastic Network Interfaceについては、インスタンスを起動したときに自動的に割り当てることができます
  - ただし、既存のネットワークインターフェイスを使用するのでなく、eth0のElastic Network Interfaceを作成する場合のみです
- 1 つ以上のセキュリティグループ
- MAC アドレス
- 送信元/送信先チェックフラグ
- 説明

# VPCエンドポイント

**VPCエンドポイント** は、VPC 内のインスタンスからインターネットを介することなく、また、インターネットゲートウェイ・NAT デバイス・VPN 接続・AWS Direct Connect 接続を利用することなく直接 AWS サービスや他の VPC へ接続する機能を提供します。  
VPCエンドポイントは、接続元のポイントを指し、 **インターフェースエンドポイント** 、 **Gateway Load Balancerエンドポイント** 、 **ゲートウェイエンドポイント** の3種類あります。  
また、接続先 VPC に設置するポイントを **VPCエンドポイントサービス** といい、VPCエンドポイントとVPCエンドポイントサービスのペアを組み合わせて **AWS PrivateLink** と呼びます。つまり、ゲートウェイエンドポイントは VPC と接続しないので AWS PrivateLink とは呼ばないことになります。  
なお、インターフェースエンドポイントは、 他VPCのVPCエンドポイントサービス以外に、AWS サービスが公開しているエンドポイントサービスに接続することができます。（例えば、CloudWatch Logsは `com.amazonaws.ap-northeast-1.logs` というサービス名で公開されています。）  
AWS Transit Gateway と比較して、AWS PrivateLink を利用して VPC 間を接続した場合、IPやサブネットの重複を気にしなくて良いというメリットがあります。（IPやサブネットが重複しても接続可能） 

- インターフェースエンドポイント
  - Amazon API Gateway
  - Amazon CloudWatch
  - Amazon CloudWatch Events
  - Amazon CloudWatch Logs
  - AWS CodeBuild
  - Amazon EC2 API
  - Elastic Load Balancing API
  - AWS Key Management Service
  - Amazon Kinesis Data Streams
  - Amazon SageMaker ランタイム
  - AWS Secrets Manager
  - AWS Service Catalog
  - Amazon SNS
  - AWS Systems Manager
  - 他の AWS アカウントによってホストされるエンドポイントサービス
  - サポートされる AWS Marketplace パートナーサービス
- Gateway Load Balancerエンドポイント
  - Gateway Load Balancer（GLB、GWLB）
- ゲートウェイエンドポイント
  - Amazon S3
  - DynamoDB

# ピアリング接続

VPC ピア接続は、プライベート IP アドレスを使用して 2 つの VPC 間でトラフィックをルーティングすることを可能にするネットワーク接続です。  
どちらの VPC のインスタンスも、同じネットワーク内に存在しているかのように、相互に通信できます。  
VPC ピア接続は、自分のVPC間、または、1つのリージョン内の他のAWS アカウントにある VPC との間に作成できます。

# VPN接続

VPN 接続の設定には「仮想プライベートゲートウェイ」と「カスタマーゲートウェイ」の 2 つを使用します。  
ここでは「 VPN 接続 」と記載していますが、AWS では [Site-to-Site VPN](https://docs.aws.amazon.com/ja_jp/vpn/latest/s2svpn/VPC_VPN.html) という言葉で表現されます。

## 仮想プライベートゲートウェイ（ **VGW** ）

VPN 接続において Amazon VPC 側に配置するゲートウェイです。  
複数のカスタマーゲートウェイからの　VPN　接続を受け付けることが可能です。（「仮想プライベートゲートウェイ：カスタマーゲートウェイ」＝「１：N」）  
仮想プライベートゲートウェイとカスタマーゲートウェイはセットで作成する必要があります。  

## カスタマーゲートウェイ

VPN 接続においてのユーザ側（自社のデータセンタなど）の定義を行う仮想的な VPN 装置です。  
仮想プライベートゲートウェイとカスタマーゲートウェイはセットで作成する必要があります。

# 作成例

筆者なりの VPC 設計方針です。  
[RFC1918](https://www.nic.ad.jp/ja/translation/rfc/1918.html)に沿います。  
パブリックサブネット 2 つ、プライベートサブネット 2 つの構成で作ってみます。

## （１）VPCの作成

「10.0.0.0/16」、「10.1.0.0/16」、「10.2.0.0/16」、、、  
のように作成します。  
CIDR ブロックは最大の `/16` を確保します。

|設定項目    |設定値     |
|:----------:|:---------:|
|ネームタグ  |sample-vpc |
|IPv4 CIDRブロック|10.0.0.0/16|
|IPv6 CIDRブロック|なし|
|テナンシー  |デフォルト |

この時作成した VPC に対応する

- メインルートテーブル が 1 つ
- デフォルト ネットワーク ACL が 1 つ
- DHCP オプションセット

が自動で作成されます。

## （２）サブネットの作成

VPCが「10.0.0.0/16」の場合、  
「10.0.0.0/24」、「10.0.1.0/24」、「10.0.2.0/24」、、、  
のように作成します。  
VPCが「10.1.0.0/16」の場合、  
「10.1.0.0/24」、「10.1.1.0/24」、「10.1.2.0/24」、、、  
のように作成します。  
ELB を用いる場合など、基本的に同一のサービスで 2 つのサブネットを作成し、異なる AZ を設定します。  
CIDR フロックを `/24` に設定した理由は以下です。

- `/24` のサブネット数が 251 個
- VPC あたりのサブネット数がデフォルト 200 個
  - ちょっと上限緩和で対応できる
- 8 の倍数でカウントアップしやすい

以下のように作成します。

|設定項目                |設定値            |
|:------------------:|:----------------:|
|ネームタグ            |sample-subnet-public0|
|VPC                 |sample-vpc        |
|アベイラビリティーゾーン|ap-northeast-1a   |
|IPv4 CIDRブロック    |10.0.0.0/24       |

|設定項目                |設定値            |
|:----------------------:|:----------------:|
|ネームタグ              |sample-subnet-public1|
|VPC                     |sample-vpc        |
|アベイラビリティーゾーン|ap-northeast-1c   |
|CIDRブロック            |10.0.1.0/24       |

|設定項目                |設定値            |
|:----------------------:|:----------------:|
|ネームタグ              |sample-subnet-private0|
|VPC                     |sample-vpc        |
|アベイラビリティーゾーン|ap-northeast-1a   |
|CIDRブロック            |10.0.2.0/24       |

|設定項目                |設定値            |
|:----------------------:|:----------------:|
|ネームタグ              |sample-subnet-private1|
|VPC                     |sample-vpc        |
|アベイラビリティーゾーン|ap-northeast-1c   |
|CIDRブロック            |10.0.3.0/24       |

作成したサブネットには VPC の

- メインルートテーブル
- デフォルト ネットワーク ACL

が自動で付与されています。

## （３）ゲートウェイの作成とアタッチ

インターネットゲートウェイの作成は以下です。

|設定項目  |設定値        |
|:------:|:------------:|
|ネームタグ|sample-igw|

「インターネットゲートウェイ」→「VPCにアタッチ」から `sample-igw` を `sample-vpc` にアタッチします。  
また、 NAT ゲートウェイの作成は以下です。

|設定項目  |設定値        |
|:------:|:------------:|
|ネームタグ|sample-ngw|

EIP を作成して `sample-subnet-private0` へアタッチします。  
なお、 NAT ゲートウェイはサブネット内でのみしか冗長化されないので、 AZ 障害に備えて `sample-subnet-private1` へもう一つ NAT ゲートウェイを作ってもよいです。

## （４）ルートテーブルの更新

基本的に、同一VPC間、およびVPCからインターネットへのルーティングを設定します。

メインルートテーブルを放置して新規にルートテーブルを作成します。  
`sample-rt-public` ルートテーブルを作成し、以下のように定義します。

|Destination|Target    |
|:---------:|:--------:|
|10.0.0.0/16|local     |
|0.0.0.0/0  |sample-igw|

ルートテーブルを上記のように設定したサブネットは「パブリックサブネット」となります。  
これを先ほど作成した `sample-subnet-public0` `sample-subnet-public1` サブネットに関連付けします。  

- 対象サブネットの -> 「ルートテーブル」タブ -> 「ルートテーブルの関連付けの編集」ボタン -> `sample-rt-public` を選択

`sample-rt-private` ルートテーブルを作成し、以下のように定義します。️

＝＝＝＝＝＝＝＝＝ここ以降要修正＝＝＝＝＝＝＝＝＝＝＝

|Destination|Target    |
|:---------:|:--------:|
|10.0.0.0/16|local     |
|0.0.0.0/0  |sample-ngw|

これを先ほど作成した `sample-subnet-private0` `sample-subnet-private1` サブネットに関連付けします。  

- 対象サブネットの -> 「ルートテーブル」タブ -> 「ルートテーブルの関連付けの編集」ボタン -> `sample-rt-private` を選択

## （５）セキュリティ設定

設定簡易化のためネットワークACLは使用せず（全てのトラフィックを通す）、セキュリティグループを使用します。  
ネットワークACLを使用する場合は、サブネット間で通信が発生しないところをブラックリストとして登録する用途として利用します。  
セキュリティグループは下記を作成します。  

|Security Group|In-Bound               |Out-Bound|
|:------------:|:---------------------:|:-------:|
|default-sg    |tcp/udp/icmp全通し      |全通し   |
|ssh-sg        |22のみ通す              |全通し   |
|public-web-sg |80と443のみ通す          |全通し   |
|app-sg        |public-web-sgからは通す  |全通し   |
|db-sg         |app-sgからは通す         |全通し   |

基本的な通信用の `default-sg` と SSH 用の `ssh-sg` を作成します。  
その他、サービスの入り口（ `public-web-sg` ）、アプリ層（ `app-sg` )、DB層（ `db-sg` ）を作成します。

### ネットワークACLの更新

Nameタグに `sample-vpc-default-acl` とつけます。  

### セキュリティグループの作成
  
以下を作成します。

|ネームタグ---|グループ名    |説明 |VPC       |
|:-----------:|:------------:|:---:|:--------:|
|default-sg   |default-sg    |任意 |sample-vpc|
|ssh-sg       |ssh-sg        |任意 |sample-vpc|
|public-web-sg|public-web-sg |任意 |sample-vpc|
|app-sg       |app-sg        |任意 |sample-vpc|
|db-sg        |db-sg         |任意 |sample-vpc|

それぞれのセキュリティグループに対して下記を設定します。

■インバウンドルール

|Security Group|タイプ              |プロトコル|ポート|送信元            |
|:------------:|:------------------:|:--------:|:----:|:----------------:|
|default-sg    |すべてのTCP         |TCP (6)   |すべて|0.0.0.0/0         |
|              |すべてのUDP         |UDP (17)  |すべて|0.0.0.0/0         |
|              |すべてのICMP        |ICMP (1)  |すべて|0.0.0.0/0         |
|ssh-sg        |SSH (22)            |TCP (6)   |22    |自社・自宅のGIP/32|
|public-web-sg |HTTP (80)           |TCP (6)   |80    |0.0.0.0/0         |
|              |HTTPS (443)         |TCP (6)   |443   |0.0.0.0/0         |
|app-sg        |すべてのトラフィック|すべて    |すべて|public-web-sg     |
|db-sg         |すべてのトラフィック|すべて    |すべて|app-sg            |
  
■アウトバウンドルール

|Security Group|タイプ              |プロトコル|ポート|送信元   |
|:------------:|:------------------:|:--------:|:----:|:-------:|
|default-sg    |すべてのトラフィック|すべて    |すべて|0.0.0.0/0|
|ssh-sg        |すべてのトラフィック|すべて    |すべて|0.0.0.0/0|
|public-web-sg |すべてのトラフィック|すべて    |すべて|0.0.0.0/0|
|app-sg        |すべてのトラフィック|すべて    |すべて|0.0.0.0/0|
|db-sg         |すべてのトラフィック|すべて    |すべて|0.0.0.0/0|

# おすすめ書籍

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

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>