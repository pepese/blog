---
title:       "Pythonで機械学習 K Meansでクラスタリング編"
URL:         "python-ml-k-means-clustering"
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
- k-means
categories:
- tech
---

ここでは、 **scikit-learn** で **k-means** を実行してみる。

<!--more-->

データセットは、以下で紹介している **iris データセット** を使用する。

- [Pythonで機械学習 データセット編](https://blog.pepese.com/python-ml-dl-datasets/)

# パッケージの導入

このブログにある Python コードを実行するためのパッケージをインストールする。

```bash
pip install jupyter scikit-learn matplotlib scipy
```

実行環境は Jupyter Notebook を想定。実行方法は `jupyter notebook` 。  
**matplotlib** の使い方は以下を参照。

- [matplotlib入門](http://blog.pepese.com/python-matplotlib-basics)

# k-means の実装

```python
import matplotlib.pyplot as plt
from sklearn import cluster
from sklearn import datasets
# iris データセットをロード
iris = datasets.load_iris()
data = iris['data']
# k-means モデルの作成
# クラスタ数は 3 を指定
model = cluster.KMeans(n_clusters=3)
model.fit(data)
# クラスタリング結果ラベルの取得
labels = model.labels_
# 以降、結果の描画
# 1 番目のキャンバスを作成
plt.figure(1)
# ラベル 0 の描画
ldata = data[labels == 0]
plt.scatter(ldata[:, 2], ldata[:, 3], color='green')
# ラベル 1 の描画
ldata = data[labels == 1]
plt.scatter(ldata[:, 2], ldata[:, 3], color='red')
# ラベル 2 の描画
ldata = data[labels == 2]
plt.scatter(ldata[:, 2], ldata[:, 3], color='blue')
# x軸、y軸の設定
plt.xlabel(iris['feature_names'][2])
plt.ylabel(iris['feature_names'][3])
plt.show()
```

<img src="../../images/k-means_result.png"  alt="iris データセット">

model の各項目は以下の通り。

- `cluster_centers_`
    - クラスタの中心座標
- `labels_`
    - 各点に付与されたラベル、つまり、クラスタリングの結果
- `inertia_`
    - 各点からそれぞれが属するクラスタの中心までの距離の挿話

# その他のクラスタリング手法

## 階層的凝集型クラスタリング

```python
model = cluster.AgglomerativeClustering(n_clusters=3, linkage='ward')
```

## 非階層的クラスタリング Affinity propagation

```python
model = cluster.AffinityPropagation()
```

# おすすめ書籍

<!-- amazon affiliate kindle python --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=479738946X&linkId=6a85a5dd8a1e65d5de970b2542faceb0"></iframe>

<!-- amazon affiliate book scikit-learn --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4873117984&linkId=587c0f7956fc7ccb395c17fc18b32e7c"></iframe>

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>