---
title:       "Pythonで機械学習 SVMで2クラス分類問題編"
URL:         "python-ml-svm-2class"
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
- svm
categories:
- tech
---

ここでは、 **scikit-learn** の **SVM** モジュールを使用して **2クラス分類問題** を解いてみる。

<!--more-->

データセットは、以下で紹介している **digits データセット** を使用する。

- [Pythonで機械学習 データセット編](https://blog.pepese.com/python-ml-datasets/)

後半で実施している精度評価についての詳細は以下を参照。

- [Pythonで機械学習 精度評価編](https://blog.pepese.com/python-ml-metrics-scores/)

# パッケージの導入

このブログにある Python コードを実行するためのパッケージをインストールする。

```bash
$ pip install jupyter scikit-learn matplotlib scipy
```

実行環境は Jupyter Notebook を想定。実行方法は `$ jupyter notebook` 。  
**matplotlib** の使い方は以下を参照。

- [matplotlib入門](http://blog.pepese.com/python-matplotlib-basics)

# SVM の実装

```python
import numpy as np
from sklearn import datasets
from sklearn import svm
from sklearn import metrics
# digits データセットのロード
digits = datasets.load_digits()
# digits データセットの中から 3 と 8 の位置を取得
# これを後に学習用データと評価用データに分割して使用する
flag_3_8 = (digits.target == 3) + (digits.target == 8)
# 3 と 8 の画像データを取得（一次元ベクトル）
data = digits.data[flag_3_8]
# 上記は下記のように 8x8 のimageを2次元ベクトルから 1次元ベクトルへ変換したものでもよい
# data = images.reshape(digits.images[flag_3_8].shape[0], -1)
# 3 と 8 の数字データを取得
labels = digits.target[flag_3_8]
# 以降、ホールドアウト検証にて正答率を算出する
# データセットの数を取得
num_of_samples = len(flag_3_8[flag_3_8])
# 学習データサイズの取得
training_data_size = int(num_of_samples * 3 / 5)
# SVMモデルの作成
classifier = svm.SVC(C=1.0, gamma=0.001)
# 学習用データをSVMへ適用
# データセットの前から 3/5 を学習用データとして使用
classifier.fit(data[:training_data_size], labels[:training_data_size])
# 正解データの取得
# データセットの後ろから 2/5 を評価用データとして使用
expected = labels[training_data_size:]
# 学習済み SVM へデータをインプットして結果を取得
predicted = classifier.predict(data[training_data_size:])
# 結果の出力
# SVMの出力と正解データを比較
print('Accuracy:\n', metrics.accuracy_score(expected, predicted)) # 0.937062937063
```

scikit-learn の SVM には、 LinearSVC 、 LinearSVR 、 NuSVC 、 NuSVR 、 OneClassSVM 、 SVC 、 SVR があり、整理すると以下のようになる。

- 分類問題に使用する SVM （Support Vector Classification）
    - SVC （C-Support Vector Classification）
        - 標準的なソフトマージン(エラーを許容する)SVM
        - [API Doc](http://scikit-learn.org/stable/modules/generated/sklearn.svm.SVC.html)
    - LinearSVC （Linear Support Vector Classification）
        - カーネルが線形カーネルの場合に特化したSVM
        - [API Doc](http://scikit-learn.org/stable/modules/generated/sklearn.svm.LinearSVC.html)
    - NuSVC （Nu-Support Vector Classification）
        - エラーを許容する表現が異なるSVM
        - [API Doc](http://scikit-learn.org/stable/modules/generated/sklearn.svm.NuSVC.html)
- 回帰問題に使用する SVM （Support Vector Regression）[参考](http://d.hatena.ne.jp/saket/20130212/1360656405)
    - SVR
    - LinearSVR
    - NuSVR
- 異常検知に使用する SVM
    - OneClassSVM

# 精度の評価

k=5 の k-分割交差検証で、以下を算出する。

- 混同行列（Confusion Matrix）
- 正答率（Accuracy）
- 適合率（Precision）
- 再現率（Recall）
- F値（F-measure）

```python
import numpy as np
from sklearn import datasets
from sklearn import svm
from sklearn import metrics
digits = datasets.load_digits()
flag_3_8 = (digits.target == 3) + (digits.target == 8)
data = digits.data[flag_3_8]
labels = digits.target[flag_3_8]
# ここまではさきほどと同じ
# 交差検証用モジュールのロード
import sklearn.model_selection
# 交差検証の実施
# train は学習用データ、testは評価用データ
for train, test in sklearn.model_selection.KFold(n_splits=5).split(data, labels):
  # 学習用データ
  train_data = data[train]
  train_labels = labels[train]
  # 評価用データ
  test_data = data[test]
  test_labels = labels[test]
  # SVMモデルの作成
  classifier = svm.SVC(C=1.0, gamma=0.001)
  # 学習用データをSVMへ適用
  classifier.fit(train_data, train_labels)
  # 正解データの取得
  expected = test_labels
  # 学習済み SVM へデータをインプットして結果を取得
  predicted = classifier.predict(test_data)
  # ここまではさきほどと同じ
  # 結果の出力
  # SVMの出力と正解データを比較
  print('Confusion Matrix:\n', metrics.confusion_matrix(expected, predicted))
  print('Accuracy:\n', metrics.accuracy_score(expected, predicted))
  print('Precision:\n', metrics.precision_score(expected, predicted, pos_label=3))
  print('Recall:\n', metrics.recall_score(expected, predicted, pos_label=3))
  print('F-measure:\n', metrics.f1_score(expected, predicted, pos_label=3), '\n')
```

ここでは、数字の「3」か数字の「8」かを分類する問題であったので、 `pos_label=3` は Positive を数字の「3」に指定したことになる。（ Negative は数字の「8」）

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