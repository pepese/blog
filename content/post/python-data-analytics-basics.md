---
title:       "Pythonでデータ分析入門"
URL:         "python-data-analytics-basics"
subtitle:    ""
description: ""
keyword:     ""
date:        2018-02-04
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- pandas
- sql
categories:
- tech
---

Python でのデータ分析環境・アプローチについて自分向けにまとめる。

<!--more-->

# はじめに

ローカルマシンでアドホックに分析を行う場合、 Python/Pandas を基本で考える。  
しかし、Pandas はデータを全てメモリに展開して処理するため、メモリに乗り切らない規模のデータを扱う場合は他の仕組みが必要になる。  
以下、自分なりのデータサイズでのアプローチの違い。

- 小規模データ（ローカルマシンのメモリにデータが乗る）
    - Python、Pandas、Jupyter で処理
- 中規模データ（ローカルマシンのメモリにデータが乗らない）
    - サーバやローカルマシンにデータベース（ RDB ）を構築して分析する
    - RDB にクエリを発行して小規模データにしてから Python で処理する
- 大規模データ（数十 GB 以上のデータ）
    - Hadoop、Apache Impala など、ガッツリした環境を構築する
    - クエリを発行して中規模データ、小規模データに変換してからアドホックな分析を行う

ここではローカルマシン上で実施する以下分析環境について記載する。

- 小規模データ分析
    - Python、Pandas、Jupyter を用いた分析
- 中規模データ分析
    - SQLite を用いた分析

# 小規模データ分析

ローカルマシンのメモリが小さく、別途メモリサイズの大きいサーバを用意して Jupyter Notebook を起動して外部からアクセスしたい場合は以下。

Jupyter Notebook で外部から接続を許可する方法について。  
サーバにて以下のコマンドで Jupyter Notebook を起動する。

```
$ jupyter notebook --ip=* --no-browser
```

表示されたパス（例えば以下）の localhost 部分をサーバの IP に書き換えてブラウザからアクセスする。

```
http://localhost:8888/?token=5a497cc7665e9fa60f20097e166c13c904958e0fe2e08cbb
```

基本操作参考：  
https://qiita.com/tanemaki/items/2ed05e258ef4c9e6caac

- Jupyter Notebook の拡張
    - [extensionを追加してもっと快適なJupyter環境を構築する](https://qiita.com/sasaki77/items/30a19d2be7d94116b237)
    - [Jupyter Notebookの拡張機能を使ってみる](http://cartman0.hatenablog.com/entry/2016/03/28/170319)
    - [Jupyter Notebookの次世代版「JupyterLab」を紹介する](http://www.monthly-hack.com/entry/2016/07/15/152726)

Pandas を用いたデータ分析を行う際のメモ。  
以下について記載する。

- ライブラリのロード
- ライブラリの設定
- データのロード

## ライブラリのロード

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt
import seaborn as sns
%matplotlib inline
```

## ライブラリの設定

```python
dir(pd.options)
#dir(pd.options.display)
```

## データのロード

```python
# データのロード
df = pd.read_csv("train.csv")
```

RDBMS の機能を使用して、データをダンプした場合はデータ末に以下のような文字列が含まれる場合があるから注意が必要。

```

(1855 行処理されました)

```

また、一行目のカラム定義がじゃまな場合もある。  
その際は、 `head -n n FILE > newFILE` や `tail -n n FILE > newFILE` コマンドなどで必要なデータ部分のみを切り出したファイルを作成する。

## データの確認

```python
df.describe()
df.info()

# 欠損の確認
print(len(df.index) - df["PassengerId"].count())

# ユニークな値の数
print(df["PassengerId"].value_counts().count())

# 変数間の相関
plt.figure(figsize=(8, 6)) #heatmap size
sns.heatmap(df.corr(), annot=True, cmap='plasma', linewidths=.5) # annot:値を表示するかどうか linewidths: しきり線

# ある範囲のデータを取得
print(len(df.loc[df["Age"] <= 10 ].index))
print(len(df.loc[(df["Age"] > 10) & (df["Age"] <= 20)].index))
```

## 前処理

### データの整形

Pandas の文字列メソッドで置換や空白削除などの処理を行うことができる。  
Pandas では、 `pandas.DataFrame` の列（つまり、 `pandas.Series` ）に対して一括で処理を行うために、 `pandas.Series` に文字列メソッドが準備されている。

- 置換
    - str.replace()
- 空白削除
    - str.strip()
    - str.lstrip()
    - str.rstrip()
- 大文字小文字変換
    - str.lower()
    - str.upper()
    - str.capitalize()
    - str.title()

```python
# 全ての列を文字列として読み込み、空白を削除する処理
df = pd.read_csv("data.csv", dtype = "object")
for _col in range(len(df.colomns)):
  df.iloc[:, _col] = df.iloc[:, _col].str.strip()
```

### ダミー変数の作成

```python
pd.get_dummies(df["Sex"], prefix = "Sex", drop_first = True)
```

# 中規模データ分析

ローカルマシンに RDB を構築して分析する。

## データ型


テーブル定義が存在しない場合、データ（CSVやTSV形式を前提）を分析して、テーブル定義を作成する。  
RDB でデータ型を判定する場合、ざっくり以下がわかればいい。

- データ型
    - 数値
        - 整数/int 4 バイト符号付き整数
        - 整数/smallint 2 バイト符号付き整数
        - 整数/tinyint 1 バイト符号無し整数
        - 浮動小数点/float 8 バイト浮動小数点
        - 金額/money 8 バイト
    - 文字列
        - 固定長/char(n) 255バイトまでの固定長の文字列
        - 可変長/varchar(n) 255バイトまでの可変長の文字列
        - text 2K 以上の文字列
    - 日付
        - date
            - フォーマット : 'YYYY-MM-DD'
            - 範囲 : '1000-01-01' から '9999-12-31'
        - datetime
            - フォーマット : 'YYYY-MM-DD HH:MM:SS'
            - 範囲 : '1000-01-01 00:00:00' から '9999-12-31 23:59:59'
        - timestamp
            - フォーマット : 'YYYY-MM-DD HH:MM:SS'
            - 範囲 : '1970-01-01 00:00:01' から '2037-12-31 23:59:59'
        - time
            - フォーマット : 'HH:MM:SS'
            - 範囲 : '-838:59:59' から '838:59:59'
- その他
    - NULL 許容
        - null
        - not null

ここでは、 int 、 float 、 char(n) 、 varchar(n) 、 (not) null を判定する。

```python
def is_float(str):
    """
    str が float か否か判定
    """
    try:
        float(str)
        return not(float(str) == int(str))
    except ValueError:
        return False

def determine_type(str):
    """
    str の型を判定
    """
    if is_float(str):
        return "float"
    elif str.isdigit():
        return "int"
    else:
        return "str"

def choice_type(type_list):
    """
    type_list から正しい型を選択
    """
    if len(type_list) == 1: # 型が 1 つ含まれる場合
        return type_list[0]
    elif "str" in type_list: # 型が複数含まれ、 1 つでも str がある場合
        return "str"
    elif "int" in type_list: # str を含まない型が複数あり、 1 つでも int がある場合
        return "int"
    else:                             # str も int も含まれない場合
        return "float"

def determine_db_type(_type, len_list):
    """
    RDB テーブルのデータ型を判定
    """
    if(_type == "str"): # 型が str の場合、 char か varchar か判定
        if len(len_list) == 1:
            return "char({})".format(len_list[0])
        else:
            len_list.sort()
            return "varchar({})".format(str(len_list[-1]))
    else:
        return _type

# RDB テーブルのデータ型を判定
df= pd.read_csv("KKKOKUAKU.tsv", sep="\t", dtype="object")
for _col in range(len(df.columns)):
    type_list = []
    len_list = []
    contains_null = False # NaN を含むか
    for _index in range(len(df.index)):
        value = str(df.iat[_index, _col])
        if value is not None: # None 、空でない場合
            if value is not "":
                _type = determine_type(value) # 値の型を判定
                _len = len(value)                           # 値の長さを取得
                if not (_type in type_list):
                    type_list.append(_type)
                if not(_len in len_list):
                    len_list.append(_len)
        else:
            contains_null = True
    _type = choice_type(type_list) # 値の型からカラムの型を判定
    _type = determine_db_type(_type, len_list) # 値の長さから型の長さを判定しつつ、カラムの型判定
    print("{} {} {} ,".format(df.columns[_col], _type, str("null" if contains_null else "not null")))
```

## SQLite

ここでは SQLite を利用する。

- [SQLite3](https://sqlite.org/index.html)
- [DB Browser for SQLite](http://sqlitebrowser.org/)
    - SQLite の Viewer

インストール（ Mac ）は以下。

```
$ brew install sqlite
$ sqlite3 --version
3.19.3 2017-06-27 16:48:08 2b0954060fe10d6de6d479287dd88890f1bef6cc1beca11bc6cdb79f72e2377b
$ brew cask install db-browser-for-sqlite
```

`/Applications/DB Browser for SQLite.app` が作成される。  
まずは `sqlite3` コマンドでデータベース・テーブルを作成してデータをインポート。（ここからは Mac 以外も同様）

```
$ sqlite3 test.db
sqlite> .show
(省略)
colseparator: "|" # 確認
(省略)
    filename: test.db
sqlite> .separator \t # セパレータの変更
sqlite> .show
(省略)
colseparator: "\t" # 確認
(省略)
sqlite> .read create_table.sql # テーブル作成クエリ発行
sqlite> .import insert_data.tsv TABLE # データインポート
```

SQL ファイルの実行は以下でも可能。

```
$ sqlite3 test.db < create_table.sql
```

なお、理想的にはテーブル定義を行なった後にインデックスを作成 `create index` して、データをインポートする。

クエリの出力結果をファイル出力する場合は以下。

```
sqlite> .show
(省略)
      output: stdout
(省略)
sqlite> .output /sqlite/data/output.txt # 出力先を標準出力からファイルへ変更
```

`output` をファイルへ変更したら `.show` の結果もファイルに出力されることに注意。  
select 出力のフォーマットも `colseparator` の設定が適用される。

データの閲覧に `DB Browser for SQLite` を利用する。

# おすすめ書籍

<!-- ad link - amazon/rakuten books - python -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"みんなのPython 第4版 [ 柴田 淳 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/9463\/9784797389463.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DMswgV","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hMCjfu","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/14583659\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"rQIGU","s":"s"});
</script>
<div id="msmaflink-rQIGU">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>