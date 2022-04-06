---
title:       "Git入門"
URL:         "git-basics"
subtitle:    ""
description: "Gitの環境構築およびGithubの設定・使い方を絡めて説明。"
keyword:     "git, github"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- git
- github
categories:
- tech
---

Git 環境構築からGithubの使い方なども含めたメモ。

<!--more-->

# 環境構築

## GitHubのアカウントを作成

下記でGitHubのアカウントを作成。

- https://github.com/

## Gitクライアント環境の作成

### Windowsへのインストール

- GitクライアントアプリをWindowsPCへインストール
    - http://www.git-scm.com/downloads

### Macへのインストール

以下を参照。

- [Macで開発環境を作る](https://blog.pepese.com/mac-dev-environment/)

### Gitクライアント設定

- ユーザ名とメールアドレスを登録（ **コミットログに残るユーザ名なので必ず設定する！** ）
    - `git config --global user.name "hoge"`
    - `git config --global user.email "hoge@email.sample.com"`
- プロキシがある環境の場合は下記を叩く
    - `git config --global http.proxy http://[FQDN]:[PORT]`
    - `git config --global https.proxy http://proxy.example.com:8080`
- プロキシがある環境の場合は下記を叩く（ID、Passwordが必要な場合）
    - `git config --global http.proxy http://[ID]:[Password]@[FQDN]:[PORT]`
    - `git config --global https.proxy http://[ID]:[Password]@[FQDN]:[PORT]`
- プロキシがある環境の場合は下記を叩く
    - `git config --global url."https://".insteadOf git://`
    - これでgitスキームを使えるようになる
- Windowsのgitではデフォルトで改行コードがCR+LFへ変換されてしまうのでオフにする
    - `git config --global core.autocrlf false`
- 設定が反映されていることを確認 `git config --global -l`

グローバル設定ではなく、単一Gitプロジェクトにのみ反映させたい場合は、 `--local` オプションに変更する。

```
core.symlinks=false
core.autocrlf=true
color.diff=auto
color.status=auto
color.branch=auto
color.interactive=true
pack.packsizelimit=2g
help.format=html
http.sslcainfo=/bin/curl-ca-bundle.crt
sendemail.smtpserver=/bin/msmtp.exe
diff.astextplain.textconv=astextplain
rebase.autosquash=true
http.proxy=http://[ID]:[Password]@proxy.example.com:8080
https.proxy=http://[ID]:[Password]@proxy.example.com:8080
url.https://.insteadof=git://
```

# 一通りの使い方

## リモートリポジトリを作って、ローカルリポジトリにコピーして編集して更新するパターン

1. リモートリポジトリをローカルリポジトリへコピー（clone）
    - GitHub用のローカルリポジトリ領域を作成してカレントを移動
        - `$ cd path/to/workspace`
    - リモートリポジトリ（GitHub）をローカルリポジトリへコピー（初回のみ）
        - `$ git clone https://github.com/pepese/Sample.git`
        - `Sample` ディレクトリと `Sample/.git` が作成される（空の場合でも最低限）
1. ローカルリポジトリへファイルを登録（add/commit）
    - コピーしたローカルリポジトリへ移動
        - `$ cd Sample`
    - ファイルを作成
        - `$ touch sample.txt`
        - このままだとファイルを作っただけでまだローカルリポジトリへは反映されていない
    - ステージング領域へ反映
        - `$ git add sample.txt`
        - 全ての変更ファイルをaddしたい場合は `$ git add --all`
    - ローカルリポジトリへコミット
        - `$ git commit`
        - Vimが起動してコミットコメントを求められるので適当に編集
        - コメント付きでコミットする場合は下記
            - `$ git commit -m "コミットコメント"`
1. ローカルリポジトリの内容をリモートリポジトリへ反映（push）
    - ローカルリポジトリの内容をリモートリポジトリ（ GitHub の main ブランチ）へ反映
        - `$ git push origin main`
    - 確認
        - 下記を見ると「sample.txt」が追加されているのがわかる
            - https://github.com/pepese/Sample
        - 「sample.txt」のリンクを押すと下記のURIへ飛び、中身が確認できる（今回は空）
            - https://github.com/pepese/Sample/blob/main/sample.txt
1. ファイルを更新する
    - ファイル（ `sample.txt` ）をエディタで更新
    - ステージング領域へ反映
        - `$ git add sample.txt`
    - ローカルリポジトリへコミット
        - `$ git commit`
    - ローカルリポジトリの内容をリモートリポジトリ（GitHub）へ反映
        - `$ git push origin main`
        - GitHubアカウントのIDとPasswordを求められるので入力
    - 確認
        - 下記を見ると「sample.txt」があることがわかる
            - https://github.com/pepese/Sample
        - 「sample.txt」のリンクを押すと下記のURIへ飛び、中身が確認できる
            - https://github.com/pepese/Sample/blob/main/sample.txt

## ローカルリポジトリを作ってリモートリポジトリへ反映するパターン

リモートリポジトリが存在している前提で。

- `$ cd path/to/workspace`
- `$ mkdir Sample2`
- `$ cd Sample2`
- `$ git init`
- `$ git remote add origin https://github.com/pepese/Sample2.git`
    - リポジトリの参照先（origin）の設定
    - もちろんGithub上にあらかじめリポジトリを作成しておく必要がある
- `$ touch sample2.txt`
- `$ git add sample2.txt`
- `$ git commit -m "first commit"`
- `$ git push origin main`

## ブランチの作成からPull Request完了まで

```bash
$ git branch <branchname>
```

`<branchname>` を指定せずに実行すると現在のブランチ（\*がついてる）が確認できる。  
また、ブランチの切り替えは以下。

```bash
$ git checkout <branchname>
```

一連の流れは以下。

```bash
$ git branch issue1

$ git branch
  issues1
* main

$ git checkout issue1

$ git branch
* issues1
  main
```

Pull Requestマージ後の作業。

```bash
$ git checkout main
$ git branch -d issue1      #ローカルブランチの削除
$ git push origin :issue1   #リモートブランチの削除
# もしくは git push --delete origin issue1
```

## ファイルの変更を戻す

以下でコミットログのハッシュ値を取得。

```bash
$ git log [ファイルパス]
```

以下でコミットのハッシュ値を指定して戻す。

```bash
$ git checkout [コミット番号] [ファイルパス]
```

## 特定のコミットをマージする

以下でリモートリポジトリの変更をローカルリポジトリへ持ってくる。（ワーキングディレクトリの変更は行われない）

```bash
$ git fetch
```

コミットログのハッシュ値を指定してマージする。

```bash
$ git cherry-pick [ハッシュ値]
```

## ブランチをマージする

issue1ブランチにmainをマージしたい場合。

```bash
$ git checkout issue1
$ git merge main
```

## ファイル、ディレクトリをgitの管理対象から外す

### ファイルをgitの管理対象から外して且削除

```bash
$ git rm [削除したいファイル]
```

### ファイルをgitの管理対象から外すがファイルを削除しない

```bash
$ git rm —-cached [削除したいファイル]
```

### ディレクトリをgitの管理対象から外して且削除

```bash
$ git rm -r [削除したいディレクトリ]
```

もうちょっと知りたい人は以下を参照。

- [Gitコマンド整理](https://blog.pepese.com/git-commands/)

# その他 Tips

## git submodule

`git submodule` は、外部の git リポジトリを、自分の git リポジトリのサブディレクトリとして登録し、特定の commit を参照する仕組み。

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