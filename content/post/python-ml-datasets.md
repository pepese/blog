---
title:       "Pythonで機械学習 データセット編"
URL:         "python-ml-datasets"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-07-05
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- "machine learning"
- scikit-learn
categories:
- tech
---

機械学習で利用するデータセットを紹介する。

<!--more-->

# パッケージの導入

このブログにある Python コードを実行するためのパッケージをインストールする。

```bash
pip install jupyter scikit-learn matplotlib scipy
```

実行環境は Jupyter Notebook を想定。実行方法は `jupyter notebook` 。  
**matplotlib** の使い方は以下を参照。

- [matplotlib入門](http://blog.pepese.com/python-matplotlib-basics)

# digits データセット

digits データセットは手書き数字のデータセットで、8x8 ピクセルのモノクロ画像 1,797 枚。

```python
import matplotlib.pyplot as plt
from sklearn import datasets
# digits データセットをロード
digits = datasets.load_digits()
type(digits) # sklearn.datasets.base.Bunch
dir(digits) # ['DESCR', 'data', 'images', 'target', 'target_names']
type(digits.DESCR) # データセットの説明 # str
type(digits.data) # numpy.ndarray
type(digits.data) # numpy.ndarray
print(digits.data.shape) # (1797, 64) # 8x8ではなく8x8=64の形式の1次元ベクトルデータ
type(digits.images) # numpy.ndarray
print(digits.images.shape) # (1797, 8, 8) # 8x8の行列データ
print(digits.data == digits.images.reshape(digits.images.shape[0], -1)) # True ..., True
type(digits.target) # 番号の種類のID　# numpy.ndarray
print(digits.target.shape) # 1,797
type(digits.target_names) # 番号の種類のIDに対応する番号 # numpy.ndarray
print(digits.target_names) # [0 1 2 3 4 5 6 7 8 9]
# 画像データを matplotlib で表示
# 1 番目のキャンバスを作成
plt.figure(1)
# digits データセットから先頭 10 枚を取得
# label にはその画像の数字、imgには画像が入る
for label, img in zip(digits.target[:10], digits.images[:10]):
  # キャンバスを 2 行 5 列に分割した label+1 番目のグラフ
  plt.subplot(2, 5, label + 1)
  # 軸の設定を off
  plt.axis('off')
  # 画像をグレースケールで subplot した箇所に貼り付け
  plt.imshow(img, cmap=plt.cm.gray_r, interpolation='nearest')
  # 画像のタイトルを label （その数字）で設定
  plt.title('Digit: {0}'.format(label))
# キャンバスの描画
plt.show()
```

<img src="/img/digits_datasets.png"  alt="digits データセット">

# iris データセット

iris データセットは植物の「あやめ」に関するデータセット。  
「萼片（がくへん）の長さ」「萼片の幅」「花びらの長さ」「花びらの幅」に関する特長量と、アヤメの種類（0:setosa、1:versicolor、2:virginica）の値を持つ 50 個のデータセットである。

```python
import numpy as np
import matplotlib.pyplot as plt
from sklearn import datasets
# iris データセットをロード
iris = datasets.load_iris()
type(iris) # sklearn.datasets.base.Bunch
dir(iris) # ['DESCR', 'data', 'feature_names', 'target', 'target_names']
type(iris.DESCR) # データセットの説明 # str
type(iris.data) # アヤメに関する特長量 # numpy.ndarray
type(iris.feature_names) # 特長良のカラム名 # list
print(iris.feature_names) # ['sepal length (cm)', 'sepal width (cm)', 'petal length (cm)', 'petal width (cm)']
type(iris.target) # アヤメの種類のID # numpy.ndarray
type(iris.target_names) # アヤメの種類のIDに対応する名前 # numpy.ndarray
print(iris.target_names) # ['setosa', 'versicolor', 'virginica']
# 画像を matplotlib で表示
# 1 番目のキャンバスを作成
plt.figure(1)
features = iris.data
target = iris.target
target_names = iris.target_names
labels = target_names[target]
#
setosa_petal_length = features[labels == 'setosa', 2]
setosa_petal_width = features[labels == 'setosa', 3]
setosa = np.c_[setosa_petal_length, setosa_petal_width]
versicolor_petal_length = features[labels == 'versicolor', 2]
versicolor_petal_width = features[labels == 'versicolor', 3]
versicolor = np.c_[versicolor_petal_length, versicolor_petal_width]
virginica_petal_length = features[labels == 'virginica', 2]
virginica_petal_width = features[labels == 'virginica', 3]
virginica = np.c_[virginica_petal_length, virginica_petal_width]
#
plt.scatter(setosa[:, 0], setosa[:, 1], color='red')
plt.scatter(versicolor[:, 0], versicolor[:, 1], color='blue')
plt.scatter(virginica[:, 0], virginica[:, 1], color='green')
#
plt.show()
```

<img src="/img/iris_datasets.png"  alt="iris データセット">

# オリジナルデータを CSV で扱う

CSV形式のデータを取り扱う場合、基本的に以下の4通りある。

1. テキストファイル読み込む
2. Python csv モジュールを使う
3. NumPy モジュールを使う
4. pandas モジュールを使う

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

<!-- ad link - amazon/rakuten books - scikit-learn -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Pythonではじめる機械学習 scikit-learnで学ぶ特徴量エンジニアリングと機械学習の基礎 [ Andreas C. Muller ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/book\/cabinet\/7980\/9784873117980.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/book\/14919364\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#fc9823","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3DRmVpN","s_n":"custom_3","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/hwLAoI","s_n":"custom_4","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":4},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/book\/14919364\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"OawK3","s":"s"});
</script>
<div id="msmaflink-OawK3">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>