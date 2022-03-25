---
title:       "matplotlib入門"
URL:         "python-matplotlib-basics"
subtitle:    ""
description: ""
date:        2016-09-18
author:      "ぺーぺーSE"
image:       ""
tags:
- python
- "machine learning"
categories:
- tech
---

matplotlibはMATLABライクにグラフを描画できるライブラリ。  
PyPlot『matplotlib.pyplot』モジュールの簡単な関数呼び出しでグラフを描画できる。

<!--more-->

# 基本

```python
import matplotlib.pyplot as plt
plt.plot([1,2,3,4])
plt.ylabel('some numbers')
plt.show()
```
<img src='http://matplotlib.org/pyplots/pyplot_simple.png' />

**matplotlib.pyplot.plot**関数で文字通りプロットすることができる。  
これで、「(x, y) = (1, 1), (2, 2), (3, 3), (4, 4)」のx, yそれぞれの地域で直線で繋いだグラフになる。

```python
import matplotlib.pyplot as plt
plt.plot([1,2,3,4], [1,4,9,16], 'ro')
plt.axis([0, 6, 0, 20])
plt.show()
```

<img src='http://matplotlib.org/pyplots/pyplot_formatstr.png' />

**matplotlib.pyplot.axis**関数でグラフを描画する値域を設定することができる。「**axes**」は英単語axの複数形で「**軸**」の意。  
これで、「(x, y) = (1, 1), (2, 4), (3, 9), (4, 16)」の値域「0<=x<=6, 0<=y<=20」で点をプロットしたグラフになる。  
また、plot関数の「**ro**」は、赤色（**r**)の点（**o**）を意味し、グラフの表現を色・形様々変更することができる。

```python
import numpy as np
import matplotlib.pyplot as plt

# NumPyで地域を定義
# 浮動小数点で0～5まで0.2刻み
t = np.arange(0., 5., 0.2)

# 3種類のグラフを同時に描画
plt.plot(t, t, 'r--', t, t**2, 'bs', t, t**3, 'g^')
plt.show()
```

<img src='http://matplotlib.org/pyplots/pyplot_three.png' />

NumPyの「**arange**」でxを表現したらyを式で表現することもできる。  
また、グラフを複数重ねることもできる。

# グラフの分割

```python
import numpy as np
import matplotlib.pyplot as plt

def f(t):
    return np.exp(-t) * np.cos(2*np.pi*t)

t1 = np.arange(0.0, 5.0, 0.1)
t2 = np.arange(0.0, 5.0, 0.02)

plt.figure(1)
plt.subplot(211)
plt.plot(t1, f(t1), 'bo', t2, f(t2), 'k')

plt.subplot(212)
plt.plot(t2, np.cos(2*np.pi*t2), 'r--')
plt.show()
```

<img src='http://matplotlib.org/pyplots/pyplot_two_subplots.png' />

実はmatplotlib.pyplotを使用している際、「**plt.figure(1)**」「**plt.subplot(111)**」が暗黙的に実行されている。  
「plt.figure」を実行するとグラフをレンダリングするキャンバス（勝手にキャンバスと呼ぶ）が作成される。引数はキャンバスの番号。  
「plt.subplot」を実行するとキャンバスの中にグラフを描画する領域を作成する。引数は三ケタで前から「行数」「列数」「グラフ番号」となる。  
なので「plt.subplot(211)」は「**キャンバスを2行1列に分割した1番目のグラフ**」、「plt.subplot(212)」は「**キャンバスを2行1列に分割した2番目のグラフ**」という意味になる。  
上記の例を縦ではなく横に並べたければ、「plt.subplot(121)」「plt.subplot(122)」とすればよい。  
キャンバスを複数作成したければ「plt.figure(1)」「plt.figure(2)」、、、とすればよい。

```python
import matplotlib.pyplot as plt
plt.figure(1)                # 1番目のキャンバス
plt.subplot(211)             # 1番目のキャンバスを2行1列に分割し、1つ目のグラフを張る
plt.plot([1, 2, 3])
plt.subplot(212)             # 1番目のキャンバスを2行1列に分割し、2つ目のグラフを張る
plt.plot([4, 5, 6])


plt.figure(2)                # 2番ン目のキャンバス
plt.plot([4, 5, 6])          # デフォルトsubplot(111)の状態にグラフを張る

plt.figure(1)                # 1番目のキャンバスの処理に戻る
plt.subplot(211)             # 1番目のキャンバスの1番目のグラフの処理に戻る
plt.title('Easy as 1, 2, 3') # 1番目のキャンバスの1番目のグラフにタイトルをつける
```

# その他のグラフ

2Dグラフ以外にもヒストグラムやアローダイアグラム等様々表現可能。  
例えば「matplotlib.pyplot.hist」関数を使用するとヒストグラムを描画できる。

```python
import numpy as np
import matplotlib.pyplot as plt

mu, sigma = 100, 15
x = mu + sigma * np.random.randn(10000)

# the histogram of the data
n, bins, patches = plt.hist(x, 50, normed=1, facecolor='g', alpha=0.75)


plt.xlabel('Smarts')
plt.ylabel('Probability')
plt.title('Histogram of IQ')
plt.text(60, .025, r'$\mu=100,\ \sigma=15$')
plt.axis([40, 160, 0, 0.03])
plt.grid(True)
plt.show()
```

<img src='http://matplotlib.org/pyplots/pyplot_text.png' />

「**plt.grid(True)**」で点線で縦横の線が付く。

その他matplotlib.pyplotの関数や詳細は下記参照。

- http://matplotlib.org/api/pyplot_api.html