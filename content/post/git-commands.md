---
title:       "Gitコマンド整理"
URL:         "git-commands"
subtitle:    ""
description: "Gitの理解に必要な概念とコマンドの使い方について。"
keyword:     "Git"
date:        2022-03-27
author:      "ぺーぺーSE"
image:       ""
tags:
- git
categories:
- tech
---

Gitコマンドの整理と必要な概念について記載する。

<!--more-->

# 環境設定

- `git init`
    - ローカルリポジトリの作成
- `git config`
    - Gitクライアントの設定
    - `--global` で全体に反映される設定、 `--local` で個別プロジェクトに反映される設定
    - `--global` と `--local` でどう項目で設定値が異なる場合、 `--local` が優先される
- `git remote`
    - `git init` した後にリモートリポジトリの設定を行う

# Gitの保存領域

Gitの以下の保存領域がある。

- **ワーキングディレクトリ** / **ワークツリー**
    - 作業スペース。編集中のファイルがある。
- **ステージングエリア** / **インデックス**
    - コミットする内容をた保存するスペース。
- **ローカルリポジトリ**
    - コミットするとステージングエリアの内容が保存される。
    - 開発者のローカルPCに作成される領域。
- **リモートリポジトリ**
    - ローカル以外のすべてのリモートリポジトリ。
    - githubなどが代表例。

# 変更を保存する

ファイルの変更は、 **ワーキングディレクトリ** -> **ステージングエリア** -> **ローカルリポジトリ** -> **リモートリポジトリ** と段階的に保存することができる。  
以下のコマンドで実行できる。

- `git add <file_name>` / `git add <file_name> <file_name> ...` / `git add --all`
    - 所謂、 **ステージング**
    - **ワーキングディレクトリ** の変更を **ステージングエリア** へ保存する
    - `--all` オプションでワーキングディレクトリの全ての変更をステージング
- `git commit`
    - 所謂、 **コミット**
    - **ステージングエリア** の変更を **ローカルリポジトリ** へ保存する
    - コミット時にはコミットメッセージを記載する必要がある（ `-m` オプションで指定可能）
    - この際、 **コミットID** が払い出される
- `git push`
    - **ローカルリポジトリ** の変更を **リモートリポジトリ** に保存する

## コミットID

コミットを実行した際、そのコミットの変更に対して40桁の一意なIDが払い出される。  
これが **コミットID** 。（以降、 `<commit_id>` とも表現）  
このコミットIDには以下のようなエイリアスが用意されている。

- `HEAD`
    - 最新のコミットID
- `HEAD^`
    - `HEAD` の1つ前のコミットID
- `HEAD^^`
    - `HEAD` の2つ前のコミットID

## 保存対象からファイルを除外する

- `git rm`
    - **ワーキングディレクトリ** にあるファイルと **ステージングエリア** のファイルを削除する

# 変更の状況を確認する

- `git status`
    - **ワーキングディレクトリ** と **ステージングエリア** の変更状況・差分を確認することができる
    - 以下のような状態がある
        - `Untracked files:`
            - **ステージングエリア** にも **ローカルリポジトリ** にもこれまで存在していない **ワーキングディレクトリ** に存在するファイル
        - `Changes not staged for commit:`
            - これまで **ステージングエリア** 、 **ローカルリポジトリ** に存在していたファイル
            - `modified:` は変更が加えられたがステージングされていないファイル
            - `deleted:` は削除されたがステージングされていないファイル
        - `Changes to be committed:`
            - **ステーイング** されているが、 **ローカルリポジトリ** に反映されていないファイル
            - `modified:` は変更が加えられたがコミットされていないファイル
            - `deleted:` は削除されたがコミットされていないファイル
- `git diff`
    - **ステージングエリア** と **ローカルリポジトリ** を比較することができる。  
    - ファイルの中身の差分まで確認することができる。
- `git diff <branch_name> <branch_name>`
    - ブランチ間の差分を確認することができる
    - `git fetch` してマージしていない状態で `git diff main origin/main` を実行すると、「現在のローカルリポジトリのmain」と「リモートリポジトリの最新のmain」の状態を比較することができる
- `git log`
    - コミットによるローカルリポジトリの変更履歴を表示する
    - コミットID、編集者、変更内容などが表示される

# 変更を戻す

- `git clean`
    - **ステージング** していない **ワーキングディレクトリ** の変更を戻す
    - **ワーキングディレクトリ** を **ステージングエリア** の状態と同じにする
- `git commit --amend`
    - 最新のコミットをやり直す
    - 最新のコミットを削除して、現在のステージングエリアの状態で際コミットする
    - 「コミットメッセージを間違えた」「コミットにファイルを含め忘れた」際などに使用
        - ステージングエリアの状態をやり直したい状態にした後、実行する
- `git reset <commit_id>`
    - 指定したコミットID以降のコミットをなかったことにする
    - 後述の `git revert` は打消し処理も履歴として残る（コミットID以降のコミットをなかったことにしない）が、 `git reset` はコミットID以降の履歴を削除してしまう
    - `<commit_id>` を省略した場合は `HEAD` を指定したことになる
    - 例： `git reset --hard HEAD^` ：現在のブランチのHEADを１つ前の状態・位置にし、 `HEAD^` 以降のコミットは削除されワーキングディレクトリの状態も戻る
    - 例： `git reset <commit id>` ：現在のブランチの位置を `<commit id>` まで戻しつつステージングエリアもそのときの状態に戻す、ワーキングディレクトリはそのまま、 `--hard` をつけるとワーキングディレクトリも戻る

resetには「 **soft** 」、「 **mixed** 」（デフォルト）、「 **hard** 」の３つのモードがありそれぞれ下表のようになる。

|モード（オプション）|HEADの位置|ステージングエリア|ワーキングディレクトリ|
|:---:|:---:|:---:|:---:|
| `--soft`  |変更する|変更しない|変更しない|
| `--mixed` |変更する|変更する  |変更しない|
| `--hard`  |変更する|変更する  |変更する|

- `git reset <file>`
    - ステージングエリアに追加されたファイルの変更をステージングエリアから削除する、ワーキングディレクトリはそのまま（モードは `--mixed` なので）
    - あるステージングエリアのファイルの状態をローカルリポジトリの `HEAD` と同じ状態にする、という意味
- `git revert <commit id>` **※ reset とは違うよ！！**
    - 指定コミットで加えられた変更を打ち消し、元に戻す新しいをコミットを生成・適用する
    - `git reset` はコミット履歴を削除するが、 `git revert` は「指定のコミットIDで加えられた変更を元に戻すための新たなコミット」を実行するので、これまでのコミット履歴は残り、且つ、「指定のコミットIDの変更を打ち消すための新たなコミット」が実行される
- `git checkout <commit id> [<file_name>]`
    - **ワーキングディレクトリ** が指定した **ローカルリポジトリ** のコミット時の状態と同じになる
    - **ステージングエリア** の保存状態が削除されることに注意
    - `git checkout -- .` で **ワーキングティレクトリ** の状態が **HEAD** の状態になる

# リモートリポジトリから変更データ・情報を取得する

- `git clone`
    - リモートリポジトリからローカルリポジトリを複製
    - `git clone --mirror`
        - リモートリポジトリのミラーリングを行う、バックアップに使う
- `git fetch`
    - **リモートリポジトリ** から最新の変更を **ローカルリポジトリ** に取り込む
    - ただし、ローカルリポジトリのブランチには取り込まれておらず、名前の無いブランチとして存在する
    - マージして初めてローカルリポジトリのブランチにリモートリポジトリの変更が追加される
- `git merge <repository>/<branch_name>`
    - `git fetch` で取得したリモートリポジトリの変更（ `<repository>/<branch_name>` ）をローカルリポジトリの現在のブランチへ反映する
- `git pull`
    - リモートリポジトリから変更を取得する（マージまで行う）
    - 「 `git fetch` + `git merge origin/main` 」に同じ

## *main* と *origin/main* の違い

*main* はローカルリポジトリのmainブランチを指し、 *origin/main* はリモートリポジトリのmainブランチを指す。  
ただし、 *origin/main* はリモートリポジトリと結びついているブランチであって、ローカルに存在する。  
`git fetch` することによって、ローカルの *origin/main* が更新される。  
つまり、 `git fetch` した後に `git merge origin/main` すると、「ローカルの *main* ブランチに最新状態に更新した *origin/main* の変更をマージする」ということになる。

## cherry-pick

現在何らかのブランチで作業していて、「あのブランチのあのコミットだけほしい。。。」と思ったとする。  
こんなとき **cherry-pick** という方法がある。  
リモートリポジトリから情報を取得し、欲しいコミットのコミットIDを指定して現在のブランチに取り込む。

```bash
$ git fetch
$ git cherry-pick <commit_id>
```

# ブランチ操作

- `git branch`
    - ブランチの一覧を表示する
    - 現在のブランチには「 **\*** 」がつく
- `git branch <branch_name>`
    - ブランチを作成する
    - 現在のブランチの現在のリビジョンで作成される
- `git branch -d <branch_name>`
    - ローカルのブランチを削除する
- `git push -d origin <branch_name>`
    - リモートのブランチを削除する
- `git checkout <branch_name>`
    - 現在のブランチを指定したブランチに切り替える
    - ワーキングディレクトリも指定したブランチの状態になる
- `git branch <branch_name>/<revision>`
    - 現在のブランチを指定したブランチとリビジョンの状態に切り替える
    - ワーキングディレクトリも指定した状態になる
- `git checkout -b <branch_name>`
    - 現在のブランチを指定したブランチに切り替える
    - ブランチが存在しない場合は新規作成される
    - ただし、 **現在のブランチのワーキングディレクトリの状態が、移動先ブランチのワーキングディレクトリに反映** される
        - コード修正し始めたけど、新しいブランチ作りたくなったときなどに便利

# ブランチ間の変更の取り込み

ブランチ間の変更の取り込みには、 **マージ** と **リベース** がある。  
どちらを使うかは悩ましい。。。

## マージ

```bash
$ git merge <branch_name>
```

2つのブランチを合流・結合させることを **マージ** という。

### マージの種類

マージには **Fast-forwardマージ** 、**non Fast-forwardマージ** 、 **Recursiveマージ** などがある。

#### Fast-forwardマージ

mainブランチの開発に対してバグが見つかり、このmainブランチのHEADからbugfixブランチを作成して修正用のコミットを実行したとする。  
さらにこの時、bugfixブランチにはコミットを実施したが、mainブランチにはコミットを実行していなかったとする。  
この状況で `git checkout main` の後 `git merge bugfix` を実行すると「mainブランチのHEAD」は単に「bugfixブランチのHEAD」に移動する。mainブランチとbugfixブランチは枝分かれしておらず、コミットログは1本でbugfixブランチが存在した歴史が残らない。  
これを **Fast-forwardマージ** という。  
デフォルトではこのマージ方法となっており、オプションで明記する場合は `--ff` 。

#### non Fast-forwardマージ

上記と同じ状況で、mainブランチに対して「bugfixブランヂに加えられた変更」のコミットを追加する方法を **non Fast-forwardマージ** といい `git merge --no-ff bugfix` を実行することで実現される。  
mainブランチとbugfixブランチは枝分かれしており、コミットログもまた分岐していて、bugfixブランチが存在した歴史が残る。  
マージはこちらが推奨される。オプションで指定せず、デフォルトを変更する方法は以下。

```bash
$ git config --global --add merge.ff false
$ git config --global --add pull.ff only # pullの時は --no-ff を除外
```

#### Recursiveマージ

mainブランチの開発に対してバグが見つかり、このmainブランチのHEADからbugfixブランチを作成して修正用のコミットを実行したとする。  
さらにこの時、mainブランチには別のコミットが実行されていたとする。  
この状況で `git checkout main` の後 `git merge bugfix` を実行すると、mainブランチに対して「bugfixブランチに加えられた変更」が追加され、mainブランチのHEADはマージで加えられた変更の状態まで移動する。  
mainブランチとbugfixブランチは枝分かれして、合流する。  
これを **Recursiveマージ** という。

## リベース

```bash
$ git rebase <branch_name>
```

「2つのブランチを合流・結合させること」をマージというのに対し、「他方のブランチの変更を、一方のブランチの最新のコミットへ移動して変更分を取り込んで、コミットの履歴を一本化すること」を **リベース** という。  
マージは「ブランチが枝分かれして合流」するが、リベースは「ブランチが枝分かれせず一本化」される。

# その他の操作

- `git tag`
    - タグを作成する
- `git blame <file_name>`
    - 対象ファイルを行単位で誰がいつ修正したのか表示
- `git stash`
    - コミットしていない変更を一時的に保存してブランチを切り替える

# 直前に戻したい

直前に戻したい系だけ、まとめる。

## ワーキングディレクトリを直前に戻したい

- `git clean`
    - **ワーキングディレクトリ** が **ステージングエリア** の状態に戻る
- `git checkout -- .`
    - **ワーキングディレクトリ** が `HEAD` の状態に戻る

## ステージングエリアを直前に戻したい

- `git reset HEAD`
    - **ステージングエリア** が `HEAD` の状態に戻る
- `git reset --hard HEAD`
    - **ステージングエリア** / **ワーキングディレクトリ** が `HEAD` の状態に戻る

## ローカルリポジトリを直前に戻したい

- `git reset --soft HEAD~`
    - **ローカルリポジトリ** だけ `HEAD` の1つ前の状態に戻る
- `git reset HEAD~` / `git reset --mixed HEAD~`
    - **ローカルリポジトリ** / **ステージングエリア** が `HEAD` の1つ前の状態に戻る
- `git reset --hard HEAD~`
    - **ローカルリポジトリ** / **ステージングエリア** / **ワーキングディレクトリ** が `HEAD` の1つ前の状態に戻る
- `HEAD` の代わりに `origin/<branch_name>` にするとリモートリポジトリの最新の状態になる

## リモートリポジトリを直前に戻したい

- `git revert <commit id>`
    - 指定の `<commit id>` の状態へ変更する新規コミットを打つ

# `^` と `~`

`^` と `~` はちょっと違う。

- `HEAD^` と `HEAD~`
    - `HEAD` の1つ前
- `HEAD^^^` と `HEAD~~~` と `HEAD~3`
    - `HEAD` の3つ前
    - `HEAD^3` は違うので注意！
- `@`
    - git v1.8.5 から `HEAD` のエイリアス

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