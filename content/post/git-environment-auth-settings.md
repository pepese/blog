---
title:       "Gitローカル環境のアカウント・認証情報設定"
URL:         "git-environment-auth-settings"
subtitle:    ""
description: "Git は分散型バージョン管理システムの定番です。この記事では、Git ローカル環境のアカウント・認証情報の設定方法について記載します。"
keyword:     "Git, 認証"
date:        2022-04-09
author:      "ぺーぺーSE"
image:       ""
tags:
- git
categories:
- tech
---

Git は分散型バージョン管理システムの定番です。  
この記事では、Git ローカル環境のアカウント・認証情報の設定方法について記載します。

<!--more-->

Git 入門内容については以下の記事を参照してください。

<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/git-basics" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">Git入門 | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">Gitの環境構築およびGithubの設定・使い方を絡めて説明。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

# アカウント

Git のローカル環境に対してアカウント情報の設定を行います。  
なお、複数アカウント存在する場合は、 `git config --local` でクローンしたリポジトリ毎の設定を忘れないようにしましょう。

## グローバル

`git` コマンドに対するグローバルな設定です。  
ローカル設定がある場合はそちらが優先されます。

```bash
# 設定
$ git config --global user.name <githubアカウント>
$ git config --global user.email <メアド>
$ git config --global http.sslverify false

# 確認
% git config --global --list
```

## ローカル

クローンしたリポジトリ単位で有効な設定です。  
設定されていない項目はグローバルもしくはデフォルトが有効になります。

```bash
# 設定
$ git config --local user.name <githubアカウント>
$ git config --local user.email <メアド>
$ git config --local http.sslverify false

# 確認
% git config --local --list
```

# 認証

git コマンドを打つ度に認証情報を聞かれるのは煩わしいです。  
ここでは、都度 ID/Password を入力しなくて済む認証情報の設定方法について記載します。  
なお、 プロトコルは `https` 前提とします。  
以下の方法があります。

- osxkeychain（Macの場合）  
- Git-Credential-Manager-for-Windows（Windowsの場合）  
- .netrc（Linuxの場合）

## osxkeychain

`credential helper` の `osxkeychain helper` を利用する方法です。  
`git credential-osxkeychain` コマンドで使えるかを確認できます。

```bash
$ git credential-osxkeychain
usage: git credential-osxkeychain <get|store|erase>
```

上記のように表示されれば利用できます。  
ヘルパーの設定を以下のように行います。

```bash
$ git config --global credential.helper osxkeychain
```

上記は `git clone` するときの `https://<username>@<FQDN>/~` 単位で認証情報を記憶してくれるため、 `git clone` する際は以下のようにアカウント情報を付与しましょう。

```bash
# 凡例
$ git clone https://<username>@<FQDN>/<repository-username>/<repository-name>.git

# Github の場合
$ git clone https://<username>@github.com/<repository-username>/<repository-name>.git
```

Mac では認証情報は「キーチェーンアクセス」に保存されます。

## Git-Credential-Manager-for-Windows

Windows の場合は `Git-Credential-Manager-for-Windows` を利用します。

```cmd
> choco install git-credential-manager-for-windows
```

以下の設定も確認・実施しておきましょう。

```cmd
# 確認
> git config --global --list
# credential.helper=manager っていうのがあればOK、なければ以下実施してください

# 設定
> git config --global credential.helper manager
```

初回パスワード入力時に Internet Explorer などと同じ場所にアクセストークンが保存され、以後はパスワード入力が不要になります。

## .netrc

パスワードを素で記述するためセキュリティ的には心配ですが、一応記載しておきます。

```bash
$ cat >> ~/.netrc <<EOF
machine github.com
login <githubアカウント>
password <パスワード>
EOF
$ chmod 600 ~/.netrc
```

# Github の認証

Github は 2021 年 8 月 13 日より Basic 認証による Git アクセスが廃止となっています。（ [参考](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) ）  
そのため、個人アクセストークンの設定が必要です。（ [参考](https://github.blog/2020-12-15-token-authentication-requirements-for-git-operations/) ）

## 個人アクセストークン

個人アクセストークンは以下の方法で取得・使用してください。

1. Web で [Github](https://github.com) にログインします
2. [設定画面](https://github.com/settings/tokens)を開きます
3. `Generate new token` をクリックします
4. noteにトークンのわかりやすい名前を記入します
5. 権限を設定 （ `repo` だけ選択しとけば大丈夫です）
6. [Generate token] をクリックします
7. トークンをコピーします （ページを離れると参照不可になるので注意してください）
8. これまでのパスワードの代わりに使用します

# おすすめ書籍

<!-- ad link - amazon/rakuten books - git -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"動かして学ぶ！Git入門 （NEXT ONE） [ 冨永 和人 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/0855\/9784798170855_1_28.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/16833788\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3x9e0yQ","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMvEIy","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/16833788\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"slVJL","s":"s"});
</script>
<div id="msmaflink-slVJL">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>