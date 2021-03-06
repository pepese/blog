# SEO

SEO対策について記載する。

# 概要

SEOは、“Search Engine Optimization” の略であり、検索エンジン最適化を意味する。  
その目的は、主に Google 検索エンジン上の検索結果の上位に表示させ、検索流入を増やすこと。

## 注意点

いかに SEO 対策をしたところで、以下のようなことが起こることは覚悟すること。

- 効果が現れるまでに時間がかかる
  - 効果が表れるまでに時間がかかり、成果が出るまでに年単位で時間がかかることも覚悟する
- 検索エンジンのアップデートによる順位下落リスク
  - サイトを評価するアルゴリズムが定期的にアップデートされるため、流入数が安定しない

## 検索順位が決まる仕組み

検索順位は、検索エンジンの巡回（ **クロール** ）とデータベースへの保存（ **インデックス** ）、そして **アルゴリズム** によるサイト評価によって決定される。

- **クロール**
  - クローラーという検索エンジンのロボットが、リンクを辿って世界中のページを巡回し、ページ情報を取得する
- **インデックス**
  - クロールによって検出したページにアクセスし、各ページの内容を分析した結果をデータベースに保存する
- 検索結果の表示
  - **アルゴリズム** によってサイトを評価し順位を決定、検索結果に反映する

## Googleの方針

### E-A-T（専門性・権威性・信頼性）

- Expertise（専門性）
- Authoritativeness（権威性）
- Trustworthiness（信頼性）

### YMYL（Your Money or Your Life）

1. 最新ニュース・イベント
2. 市民の権利・義務、政治、法律
3. 経済
4. ショッピング
5. 健康と安全
6. 人々のグループ（人種・宗教・国籍など）
7. その他
    - 上記には当てはまらないものの、人々の生活に大きな影響を及ぼすとされるテーマ
    - 栄養学や不動産、職探しに関する情報などが例として上げられる

### 不正行為の禁止

- ブラックハットSEO

# 上位に表示される要件

重要度順に以下の対策がある。

1. コンテンツ評価が高い
    1. サイト全体でキーワードに関連するコンテンツが多い
    2. 対象ページのコンテンツがユーザニーズを満たしている
2. 被リンク評価が高い
    1. サイト全体で評価の高いサイトからのリンクを多く集めている
    2. 対象ページに評価の高いサイトからのリンクを多く集めている
3. Googleガイドラインに則ったコーディング
    1. クローラビリティ対策
    2. キーワード対策
    3. 内部リンク対策
    4. スパム対策
    5. モバイル対策
    6. アルゴリズム対策

# 対策

前節の「上位に表示される要件」の重要度項目の採番に沿って記載する。

## 1. コンテンツ評価が高い

対策キーワードで上位表示されている競合コンテンツの内容を見て、下記項目を確認する。  
なお、対策キーワードが分からない場合は Google Search Console を利用するとよい。

- タイトルや見出しなどのタグに対策キーワードは入っているか
  - タイトルタグ・見出しタグ（hタグ）は、SEO対策において重要な役割
  - 対策キーワードが入っているかを確認し、入っていない場合は含む形で修正
- 自コンテンツに足りないトピックはないか
  - 上位表示されているコンテンツに入っていて、自コンテンツに入っていないトピックがあれば、コンテンツの流れに沿うように追加
  - すでに上位表示されているコンテンツは、Googleの検索エンジンから「ユーザーにとって有益」と判断されていると考えられるため
- 自コンテンツに不要なトピックはないか
  - 逆に、自コンテンツに上位表示に不要なトピックを含んでしまっていないかも確認
  - コンテンツのテーマにそぐわない要素が入っていたり、異なる言い回しで同じ内容を繰り返し書いていないか

また、狙っている検索キーワードでヒットしない場合は以下の対策も必要。

- 対策キーワードに関連するコンテンツを追加
  - 検索順位が高いページほど、サイト内でカバーしている関連キーワード数が多いことがわかってい

## 2. 被リンク評価が高い

評価の高いサイトからの非リンクを増やし、ドメイン評価を上げる必要がある。  
評価の高いサイトとは？

- やらないこと：アンコントローラブルなサイトからの流入
  - TechFeed や Menthas 、 NewsPics のようなキュレーションに拾ってもらおうと思ったが、ほぼほぼ企業系テックブログなど非個人記事が占めているため狙わない
- やること：ある程度コントローラブルなサイト・メディアからの流入（はてぶ で探すといいか、、、？）
  - 「はてブ」ボタン設置
  - Twitter で記事関連事項をつぶやく
  - Qiita 、 Zenn で雑な記事を書き、ブログを参照する
    - Qiita : プログラミングに関すること
    - Zenn : Qiita で弾かれそうなこと
  - noteも、、、？

ちなみにテックブログへの流入は 「はてぶ > smartnews > google 検索」 くらいになる模様。

## 3. Googleガイドラインに則ったコーディング

### 3.1. クローラビリティ対策

`sitemap.xml` 、 `robots.txt` などをちゃんと設置するとかかな？

### 3.2. キーワード対策

関連キーワードを以下のようなサービスを使って探す。

- [ラッコキーワード](https://related-keywords.com/)
  - キーワードを 1 つ入力すると様々な検索エンジンのサジェストキーワードの一覧を表示してくれる

如何に的確な関連キーワードを選択できたところで、自サイトが検索にヒットしなければ流入は無い。  
そこで以下のようなサービスを使って、キーワードの難易度をチェックする。

- [rishirikonbu.jp](http://rishirikonbu.jp/difficulty_checker/)
- [SEOキーワード難易度チェックツール](https://seopack.jp/keyword/)

また、折角検索でヒットしても、そこに需要がなければ流入数はあまり増加しない。  
そこで以下のようなサービスを使って、検索ボリューム（検索数、順位毎のアクセス数予測など）をチェックする。

- [aramakijake.jp](https://aramakijake.jp/)
- Googleキーワードプランナー

上記で関連キーワードを決めたら、そのワードに沿ったコンテンツを作成し、以下にキーワードを盛り込む。

- titleタグ
  - 記事内容を表すメインキーワードを必ず含める（できれば前半）
  - 文字数は30～35文字
  - 関係のないキーワードを入れない
- 見出しタグ（h1、h2…）
  - キーワードを含める
- メタディスクリプション
  - headタグ内に記載
  - 記事の内容を100文字程度のテキストで要約した概要文

### 3.3. 内部リンク対策

内部リンクとは同一サイト内でのリンクのこと。  
以下のような対策を行う。

- 自サイト内の関連する記事同士を繋ぐリンク
- 内部リンクを設置することで、クローラーの巡回を促し、インデックスを促進する
- 関連性の高いページ同士を繋ぐことで、ユーザビリティが向上し、結果的にサイト評価が上がる

### 3.4. スパム対策

省略

### 3.5. モバイル対策

- MFI（モバイルファーストインデックス）
  - Googleのクローラーがモバイル版のページを優先的にクロール・インデックスする
  - [モバイルフレンドリーテスト](https://search.google.com/test/mobile-friendly) で調べる
- ページスピード
  - [PageSpeed Insights](https://pagespeed.web.dev/) で計測

### 3.6. アルゴリズム対策

- 構造化データ
  - 構造化データを設定することで、検索エンジンがコンテンツを認識しやすくなったり、検索結果に [リッチスニペット](https://www.willgate.co.jp/promonista/glossary/rich_snippet/) が表示される
- 重複コンテンツ
  - サイトの内部・外部で同じようなコンテンツが掲載されていること
  - 無断でコピーされたコンテンツはペナルティの対象になるので、オリジナルな内容のコンテンツを作成すること
  - どうしても重複する場合の対策として「canonicalタグによるURL正規化」などがある
- 低品質コンテンツ
  - ユーザーの求めている情報が載っていない低品質コンテンツは削除する

# 対策効果の計測

- Google Analytics
- Google Search Console

# keywords

- [ラッコキーワード](https://related-keywords.com/) で keywords 探索
- [aramakijake.jp](https://aramakijake.jp/) で検索 volume 確認
  - 出ない場合はキーワードプランナー -> 「1000～1万」みたいな表示
- [SEOキーワード難易度チェックツール](https://seopack.jp/keyword/) で難易度確認

|keywords|検索 volume|難易度|
|:---|---:|---:|
|aws|619,272|69|
|aws 資格|7,512|6|
|gcp|186,120|56|
|google cloud|227,743|80|
|googlecloud|278,503|78|
|asdf|124,531|60|
|asdf java|10～100|39|
|golang|83,246|62|

# キラーコンテンツへの内部リンク

```
# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>
```