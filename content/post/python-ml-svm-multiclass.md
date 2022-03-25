---
title:       "Pythonで機械学習 SVMで多クラス分類問題編"
URL:         "python-ml-svm-multiclass"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-07-06
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

ここでは、 **scikit-learn** の **SVM** モジュールを使用して **多クラス分類問題** を解いてみる。

<!--more-->

SVMを使用した2クラス分類問題は以下。

- [Pythonで機械学習 SVMで2クラス分類問題編](https://blog.pepese.com/python-ml-dl-svm-2class/)

データセットは、以下で紹介している **digits データセット** を使用する。

- [Pythonで機械学習 データセット編](https://blog.pepese.com/python-ml-dl-datasets/)

後半で実施している精度評価についての詳細は以下を参照。

- [Pythonで機械学習 精度評価編](https://blog.pepese.com/python-ml-dl-metrics-scores/)

# パッケージの導入

このブログにある Python コードを実行するためのパッケージをインストールする。

```sh
$ pip install jupyter scikit-learn matplotlib scipy
```

実行環境は Jupyter Notebook を想定。実行方法は `jupyter notebook` 。  
**matplotlib** の使い方は以下を参照。

- [matplotlib入門](http://blog.pepese.com/python-matplotlib-basics)

# SVM の実装

```python
import numpy as np
from sklearn import datasets
from sklearn import svm
from sklearn import multiclass
from sklearn import metrics
# digits データセットのロード
digits = datasets.load_digits()
# 画像データを取得（一次元ベクトル）
data = digits.data
# 数字データを取得
labels = digits.target
# データセットの数を取得
num_of_samples = labels.shape[0]
# 学習データサイズの取得
training_data_size = int(num_of_samples * 3 / 5)
# 2クラス分類問題を解くSVMモデルの作成
estimator = svm.SVC(C=1.0, gamma=0.001)
# 2クラス分類問題を解くSVMモデルを他クラス問題へ対応
classifier = multiclass.OneVsRestClassifier(estimator)
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
print('Accuracy:\n', metrics.accuracy_score(expected, predicted))
```

[scikit-learnによる多クラスSVM](http://qiita.com/sotetsuk/items/3a5718bb1f945a383ceb)

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
from sklearn import multiclass
from sklearn import model_selection
from sklearn import metrics
# digits データセットのロード
digits = datasets.load_digits()
# 画像データを取得（一次元ベクトル）
data = digits.data
# 数字データを取得
labels = digits.target
# データセットの数を取得
# num_of_samples = labels.shape[0]
# 学習データサイズの取得
# training_data_size = int(num_of_samples * 3 / 5)

for train, test in model_selection.KFold(n_splits=5).split(data, labels):
  train_data = data[train]
  train_labels = labels[train]
  test_data = data[test]
  test_labels = labels[test]
  estimator = svm.SVC(C=1.0, gamma=0.001)
  classifier = multiclass.OneVsRestClassifier(estimator)
  classifier.fit(train_data, train_labels)
  expected = test_labels
  predicted = classifier.predict(test_data)
  print('Confusion Matrix:\n', metrics.confusion_matrix(expected, predicted))
  print('Accuracy:\n', metrics.accuracy_score(expected, predicted))
  print('Precision:\n', metrics.precision_score(expected, predicted, average="macro"))
  print('Recall:\n', metrics.recall_score(expected, predicted, average="macro"))
  print('F-measure:\n', metrics.f1_score(expected, predicted, average="macro"), '\n')
```

# おすすめ書籍

<!-- amazon affiliate kindle python --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=479738946X&linkId=6a85a5dd8a1e65d5de970b2542faceb0"></iframe>

<!-- amazon affiliate book scikit-learn --->
<iframe sandbox="allow-popups allow-scripts allow-modals allow-forms allow-same-origin" style="width:120px;height:240px;" marginwidth="0" marginheight="0" scrolling="no" frameborder="0" src="//rcm-fe.amazon-adsystem.com/e/cm?lt1=_blank&bc1=000000&IS2=1&bg1=FFFFFF&fc1=000000&lc1=0000FF&t=tanakakns-22&language=ja_JP&o=9&p=8&l=as4&m=amazon&f=ifr&ref=as_ss_li_til&asins=4873117984&linkId=587c0f7956fc7ccb395c17fc18b32e7c"></iframe>