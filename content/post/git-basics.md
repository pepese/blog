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

<!-- amazon affiliate kindle git --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=B09DPKYHK9&linkId=7f63835d3ad6c9cb3c701a2dfdc826a1"></iframe>