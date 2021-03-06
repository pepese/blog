---
title:       "Pandas入門 DataFrame編"
URL:         "python-pandas-dataframe"
subtitle:    ""
description: ""
keyword:     ""
date:        2018-02-04
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- pandas
- dataframe
categories:
- tech
---

Python ライブラリである Pandas の DataFrame についてまとめる。

- [公式ドキュメント](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.html)

<!--more-->

# 基本操作

## DataFrame の作成

```python
df = pd.DataFrame([["Taro", 10], ["Jiro", 20], ["Goro", None]], columns=["name", "age"])

    age  name
0  Taro  10.0
1  Jiro  20.0
2  Goro   NaN
```

リストや NumPy の ndarray をそのまま DataFrame にできる。  
以降のプログラム例では、上記の DataFrame を操作する例とする。

## データのロード

`read_csv()` と `read_table()` はデフォルトの区切り文字が違うだけで中身は同じ。  
`read_csv()` はカンマ、 `read_table()` はタブ。

```python
df = pd.read_csv("data.csv")
df = pd.read_table("data.tsv")
```

`sep` オプションを使用することで区切り文字を指定できる。

```python
df = pd.read_csv("data.csv", sep = "¥t")
```

`dtype` オプションを指定することで、読み込み時のデータ型を指定できる。

```python
df = pd.read_csv("data.csv", dtype = {"name": "object", "age": "object"}) # 列を指定して文字列で読み込み
df = pd.read_csv("data.csv", dtype = {0: "object", 1: "object"}) # カラム番号でも可能
df = pd.read_csv("data.csv", dtype = "object") # 全ての列を文字列で読み込み
```

なお、 DataFrame ではデフォルトで欠損値を `NaN` で扱う。

## 変換

Series を DataFrame へ変換する。

```python
df = pd.DataFrame(se)
```

## 検索

`[]` 内に `True` または `False` を返す式を指定する。  
検索でヒットしたセルにそのまま値を代入できる。

```
# "age" 列の値が 10 より大きい行を取得
df[df.age > 10]

   name   age
1  Jiro  20.0
```

```python
# 値が 0 より大きい値のみを取得
df[df > 0]

   name   age
0  Taro   NaN
1  Jiro  20.0
2  Goro   NaN
```

```python
# 検索でヒットしたセルに値を代入
df.age[df.age > 10] = 100

   name    age
0  Taro   10.0
1  Jiro  100.0
2  Goro    NaN
# SettingWithCopyWarning が出るが値は代入されている
# Warning が出ないようにするには後述の loc を使う
```

後述の `isnull()` や `isin()` メソッドを使った検索も可能。

## 削除

```python
# 行削除
df.drop(index = 0)

   name   age
1  Jiro  20.0
2  Goro   NaN
```

```python
# 列削除
df.drop(columns = "age", inplace = True)

   name
0  Taro
1  Jiro
2  Goro
```

`inplace = True` を指定するとオブジェクトが上書きされる。  
また、検索条件にひっかけることで行の削除が可能。

```python
# age列が10以下の行を削除 = age列が10より大きい列のみ抽出
df = df[df.age > 10]

   name   age
1  Jiro  20.0
```

## 追加

```python
df2 = pd.DataFrame([["Rokuro", 60]], columns=["name", "age"])
# 行追加
df = df.append(df2)

     name   age
0    Taro  10.0
1    Jiro  20.0
2    Goro   NaN
0  Rokuro  60.0
```

```python
se = pd.Series(["male", "female", "male"])
# 列追加
df["sex"] = se

   name   age     sex
0  Taro  10.0    male
1  Jiro  20.0  female
2  Goro   NaN    male
```

## 列取得

一列取得した場合は **Series** になる。

```python
df.name
# print(df["name"]) も同じ

0      Taro
1      Jiro
2      Goro
Name: name, dtype: object
```

```python
print(df[["name", "age"]])

   name   age
0  Taro  10.0
1  Jiro  20.0
2  Goro   NaN
```


## 検索

https://qiita.com/tanemaki/items/2ed05e258ef4c9e6caac

# 属性（Attributes）

|属性|説明|
|:---|:---|
|T|転置行列|
|at|後述|
|axes|行ラベルと列ラベルの情報|
|blocks|辞書形式の内部属性|
|columns|列ラベル|
|dtypes|オブジェクトのデータタイプ|
|empty|中身が完全に空の時、Trueを返す|
|ftypes|オブジェクトの ftypes を Series 形式で返却|
|iat|後述|
|iloc|後述|
|index|行ラベル|
|is_copy|不明|
|ix|非推奨|
|loc|後述|
|ndim|何次元配列か返却|
|shape|行と列のサイズ|
|size|行列の要素数（行サイズは `len(df.index)` 、列サイズは `len(df.columns)` ）|
|style|Stylerオブジェクトを返却|
|values|numpy.ndarray 形式で値を取得|

## `at` 、 `iat` 、 `loc` 、 `iloc`

- 単独の値（スカラー）にアクセスするのが `at` と `iat`
- 単独の値（スカラー）および複数の値（ベクトル）にアクセスするのが `loc` と `iloc`
- 行ラベル（行名）、列ラベル（列名）で位置を指定するのが `at` と `loc`
- 行番号、列番号で位置を指定するのが `iat` と `iloc`
- 処理速度は `at` と `iat` のほうが `loc` と `iloc` よりも高速

なお、 `DataFrame.get_value()` 、 `DataFrame.ix[]` もあるが、それぞれバージョン `v0.21.0` `v0.20.0` から非推奨（Deprecated）になっている。これから新しく書くコードには `at` , `iat` , `loc` , `iloc` を使うほうがいいだろう。

```python
# USAGE
df.at[行条件, 列条件]
df.iat[行条件, 列条件]
df.loc[行条件, 列条件]
df.iloc[行条件, 列条件]
```

`at` は行ラベルと列ラベルで位置を指定する。

```python
df.at[1, "age"]
# 20.0
# numpy.float64

df.at[2, "name"]
# 'Goro'
# str

df.at[1, "age"] = 60 # 代入も可能
```

`iat` は行番号と列番号で位置を指定する。行番号・列番号は `0` はじまり。

```python
df.iat[0, 0]
# 'Taro'

df.iat[2, 1]
# nan
```

`loc` はスライス `x:y` やリスト `[x, y]` でデータの範囲・位置を指定する。参照される値は pandas.Series または pandas.DataFrame になる。  
列の指定を省略すると、列全体の参照になる。  
列全体を参照したい場合は、行の指定を `:` （全体のスライス）にする。  
また、行の指定には `df.age>10` のような **検索条件** を指定することもできる。  
`loc` で取得したセルに対して **値を代入** することができる。

```python
df.loc[0:1, "name":"age"]

df.loc[[0, 2], ["name", "age"]]

df.loc[0:2, "age"] = [20, 30, 40] # 代入

df.loc[df.age>10, "age"] = 20 # 検索結果が複数行でも代入可能
```

# 代表的なメソッド

|メソッド|説明|
|:---|:---|
| `apply(関数・Lambda)` |各列に任意の処理を適用|
| `astype({辞書型})` |型変換|
| `replace("置換対象文字", "置換文字")` |文字の置換|
| `rename(columns={辞書型}, inplace=True)` |カラム名の変更|
| `isin(リスト)` |セルの値がリストに含まれていれば True 、そうでなければ False の DataFrame を返却する|
| `isnull()` |セルが None であれば True 、そうでなければ False の DataFrame を返却する|
| `drop_duplicates()` |重複を削除|
| `pivot_table(index=[], columns=[],values=[, aggfunc='sum')` |ピボットテーブル|
| `sort_values(by=)` |ソート|

# SQL ライクな操作

- [公式ドキュメント](https://pandas.pydata.org/pandas-docs/stable/comparison_with_sql.html)

## `wherer`

条件式が成り立つものをとってきて，それ以外はNaNで埋める。

- [公式ドキュメント](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.where.html)

## `groupby`

`groupby` でカラムを指定し、様々な集計関数を適用することができる。

|項目|説明|
|:---|:---|
| `df.groupby('a').mean()` |平均|
| `df.groupby('a').max()` |最大値|
| `df.groupby('a').min()` |最小値|
| `df.groupby('a').count()` |出現回数|
| `df.groupby('a').sum()` |合計値|
| `df.groupby('a').std()` |標準偏差|
| `df.groupby('a').sum()` |合計値|

集計のキーをインデックスとして扱いたくない場合はオプション `as_index=False` を指定する。

- [公式ドキュメント](https://pandas.pydata.org/pandas-docs/stable/api.html#groupby)

## `merge`

いわゆる JOIN 。

- [公式ドキュメント](https://pandas.pydata.org/pandas-docs/stable/generated/pandas.DataFrame.merge.html#pandas.DataFrame.merge)

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