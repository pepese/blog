---
title:       "Pythonで機械学習 精度評価編"
URL:         "python-ml-metrics-score"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-08-07
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- "machine learning"
- scikit-learn
categories:
- tech
---

ここでは、機械学習で作成したモデルの精度評価・向上方法を記載する。

- 学習用データと評価用データの使い方
- 精度の指標
- 精度の向上

<!--more-->

ここで述べる手法は **scikit-learn** に実装されている。  
なお実装例については以下記事の後半で紹介している。

- [Pythonで機械学習 SVMで2クラス分類問題編](https://blog.pepese.com/python-ml-dl-svm-2class/)
- [Pythonで機械学習 SVMで多クラス分類問題編](https://blog.pepese.com/python-ml-dl-svm-multiclass/)

# 学習用データと評価用データの使い方

教師あり学習前提ではあるが、正解付けしたデータは学習用データと評価用データに分割して以下のような方法でモデルを評価する。

- ホールドアウト検証
    - データセットを学習用データと評価用データへ2分割する方法
    - 割合は任意
- k-分割交差検証
    - データセットを k 分割して、そのうち 1 個を評価用データ、その他の k-1 個を学習用データとして使用する方法
    - 評価用データを変更しながら k 回繰り返した結果の平均を精度に用いることが多い
    - scikit-learn 0.18 までは `sklearn.cross_validation.KFold` モジュールだったが DeprecationWarning となっており、0.20 からは `sklearn.model_selection.KFold` を使用する
        - [参考](http://segafreder.hatenablog.com/entry/2016/10/18/163925)

# 精度の指標

精度の指標には以下がある。

- 正答率（Accuracy）
    - 全体の事象の中で正解がどれだけあったかの割合
- 適合率（Precision）
    - モデルが Positive と判断した中で、本当に Positive なものの割合
- 再現率（Recall）
    - 本当に Positive なものの中で、モデルが Positive と判断できたものの割合
- F値（F-measure）
    - 適合率と再現率の調和平均
    - 適合率の再現理を総合的に見る際に使用

上記は以下の **混同行列** （ **Confusion Matrix** ）から算出することができる。

<table>
<tr>
<td></td><td></td><td colspan="2">予測</td>
</tr>
<tr>
<td></td><td></td><td>Positive</td><td>Negative</td>
</tr>
<tr>
<td rowspan="2">実際</td><td>Positive</td><td>True Positive (TP)</td><td>False Negative (FN)</td>
</tr>
<tr>
<td>Negative</td><td>False Positive (FP)</td><td>True Negative (TN)</td>
</tr>
</table>

- TP・・・Positive という予測が True （正解）だった数
- FP・・・Positive という予測が False （不正解）だった数
- TN・・・Negative という予測が True （正解）だった数
- FN・・・Negative という予測が False （不正解）だった数

上記を用いてそれぞれの指標値は以下のように算出できる。

$$ 正答率（Accuracy）=\frac{TP+TN}{TP+FP+FN+TN} $$

$$ 適合率（Precision）=\frac{TP}{TP+FP} $$

$$ 再現率（Recall）=\frac{TP}{TP+FN} $$

$$ F値（F-measure）=\frac{ 2 \times 適合率 \times 再現率 }{ 適合率 + 再現率 } $$

これらの使用値と混同行列は scikit-learn の metrics モジュールを用いて、以下のように算出できる。

- 正答率（Accuracy）
    - `metrics.accuracy_score(expected, predicted)`
- 適合率（Precision）
    - `metrics.precision_score(expected, predicted, pos_label=3)`
- 再現率（Recall）
    - `metrics.recall_score(expected, predicted, pos_label=3)`
- F値（F-measure）
    - `metrics.f1_score(expected, predicted, pos_label=3)`
- 混同行列（Confusion Matrix）
    - `metrics.confusion_matrix(expected, predicted)`

上記の `pos_label` は **Positive Label** のこと。  
2クラス分類問題などでは片方が `Positive` 、もう片方が `Negative` となるため、片方のラベルを Positive として設定してあげることになる。  
繰り返しになるが、実装例については以下記事の後半で紹介している。

- [Pythonで機械学習 SVMで2クラス分類問題編](https://blog.pepese.com/python-ml-dl-svm-2class/)
- [Pythonで機械学習 SVMで多クラス分類問題編](https://blog.pepese.com/python-ml-dl-svm-multiclass/)

# 精度の向上

精度向上の方法にはいくつかあるが、以下のような方法が考えられる。

- パラメータチューニング
- アンサンブル学習

## パラメータチューニング

パイパーパラメータの最適値を選ぶには以下の手法があり、うち **グリッドサーチ** は scikit-learn で実装されている。[参考](http://sucrose.hatenablog.com/entry/2013/05/25/133021)

- グリッドサーチ
    - 昔からある手法で、格子状の空間で最適なパラメータを探索する方法です
    - 格子の範囲を総当りするため、膨大な計算時間がかかるという課題がある
- ランダムサーチ
    - 無作為にパラメータを抽出して探索する
    - グリッドサーチよりも計算時間が短くて済むというメリットがある
- ベイズ最適化 (Bayesian Optimization)
    - 最初は無作為にパラメータを抽出して探索するが、その結果を使って次に探索する点を確率的に選ぶ方法
    - ディープラーニングを含む機械学習の手法で、比較的良いハイパーパラメータを探索できることが知られている
    - 特に、低次元のデータで大域的な解を求めたい場合や、グリッドサーチなどで時間がかかっている場合に有効な手法

## アンサンブル学習

**アンサンブル学習** とは、いくつかの性能の低い分類器（弱仮説器）を組み合わせて、性能の高い 1 つの分類器を作る方法。  
弱仮説器のアルゴリズムは決まっておらず、適宜選定する必要がある。  
アンサンブル学習のイメージは「多数決」であり、以下の 2 つに分類できる。

- バギング
    - 学習データを抜けや重複を許して複数個のグループに分割し、学習データのグループ毎に弱仮説器を生成する方法
    - 分類時は、各弱仮説器の出力した分類結果の多数決をとる
- ブースティング
    - 複数の弱仮説器を用意し、重み付きの多数決で分類を実現する方法
    - 難易度の高い学習データを正しく分類できる弱仮説器の半径結果を重視されるように重みを更新していく
    - ある弱仮説器が間違ったデータを難易度の高いデータとし、「難易度の高いデータの抽出」と「難易度の高いデータに特化した弱仮説器の重みの計算」を反復して弱仮説器の重みを決定する

**Random Forest** はバギング、 **AdaBoost** はブースティングの例である。

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