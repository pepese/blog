---
title:       "Spring Frameworkのアノテーションとプロジェクト構造"
URL:         "java-springframework-annotation-project-structure"
subtitle:    ""
description: "Spring Frameworkは、Java言語のWebアプリケーションフレームワークのデファクトスタンダードです。非常に便利なのですが、アノテーションの種類が多く混乱しがちです。この記事ではアノテーションの一覧と機能概要、プロジェクトでの利用例をご紹介します。"
keyword:     "Spring Framework, アノテーション, Java"
date:        2022-04-11
author:      "ぺーぺーSE"
image:       ""
tags:
- java
- "spring framework"
categories:
- tech
---

Spring Frameworkは、Java言語のWebアプリケーションフレームワークのデファクトスタンダードです。  
非常に便利なのですが、アノテーションの種類が多く混乱しがちです。  
この記事ではアノテーションの一覧と機能概要、プロジェクトでの利用例をご紹介します。

<!--more-->

# アノテーション

## Core系

|アノテーション          |付与箇所|説明|
|:-----------------|:------------|:----|
| `@Configuration` |クラス|Java Based Configurationクラスとなる。Bean 定義クラスとして `@Bean` メソッドなどを宣言。|
| `@ComponentScan` |クラス| `@Configuration` 付与されたJava Based Configurationクラスに付与し、コンポーネントスキャンを有効化。|
| `@Bean`          |メソッド| `@Configuration` 付与されたJava Based Configurationクラスのインスタンス化メソッドに付与。|
| `@Component`     |クラス|DIコンテナにbeanとして登録するクラスへ付与。|
| `@Service`       |クラス| `@Component` と同じだが、明確にService層示す際に利用。|
| `@Repository`    |クラス| `@Component` と同じだが、明確にPersistence層（DAO等のDBアクセスを行うクラス）示す際に利用。|
| `@Controller`    |クラス| `@Component` と同じだが、明確にSpringMVCでControllerの役割となるクラスに利用。|
| `@Scope`         |クラス|beanのスコープを設定。|
| `@Transactional` |クラス、メソッド|トランザクション境界を設定。|
| `@Autowired`     |メソッド、フィールド|フィールドの型（インターフェース）の実装クラスのbeanを自動的にインジェクション。|
| `@Qualifier`     |メソッド、フィールド| `@Autowired` と併用し、bean名を指定したい場合に使用。|
| `@Value`         |フィールド| `application.yaml` などで指定した値を代入。|
| `@Required`      |メソッド|Setterメソッドに付与し、インジェクションが必須であることを示す。|

### Beanのスコープ

|スコープ|説明|
|:----------------|:------------|
|singleton|(Default) ApplicationContext に 1 つインスタンス化。|
|prototype|Bean 取得毎にインスタンス化。スレッドアンセーフの場合に利用。|
|request|HTTP リクエスト毎にインスタンス化。|
|session|HTTPセッション単位でインスタンス化。|
|application|サーブレットのコンテキスト単位でインスタンス化。warの単位なイメージ。|
|websocket|WebSocketのライフサイクル単位でインスタンス化。|
|custom|上記以外の独自カスタムスコープ。|

## SpringMVC系

|アノテーション     |付与箇所|説明|
|:--------------------|:------------|:----|
| `@Controller`       |クラス        |MVCコントローラ。|
| `@RestController`   |クラス        |REST用コントローラ。 `@Controller` + `@ResponseBody` と同じ。|
| `@RequestMapping`   |クラス、メソッド|URI（value）、HTTPメソッド（method）、HTTPヘッダ（headers）、リクエストパラメータ（params）などでコントローラへのルーティングを設定。|
| `@RequestParam`     |メソッド       |URLのクエリパラメータをメソッド引数に注入。|
| `@PathVariable`     |メソッド       |URLのパスパラメータをメソッド引数に注入。|
| `@RequestHeader`    |メソッド       |リクエストヘッダをメソッド引数に注入。|
| `@RequestBody`      |メソッド       |リクエストボディをメソッド引数に注入。|
| `@ResponseBody`     |メソッド       |return するオブジェクトをHTTPレスポンスボディに変換。|
| `@ResponseStatus`   |メソッド       |HTTPレスポンスステータスを指定。|
| `@ExceptionHandler` |メソッド       |クラス内で発生した例外を処理するメソッドに付与。|
| `@CookieValue`      |メソッド       |特定の Cookie を処理するメソッドに付与。|
| `@InitBinder`       |メソッド       |これを付与したメソッド用意すると、バインド処理が行われる前にこのメソッドが呼び出され、デフォルトの動作をカスタマイズすることができる。|
| `@ModelAttribute`   |メソッド       |これが付与されたメソッドの返り値は、自動でModelに追加される。|

## SpringBoot系

|アノテーション          |付与箇所|説明|
|:----------------|:------------|:----|
| `@SpringBootApplication`   |クラス| `@Configuration` 、 `@EnableAutoConfiguration` 、 `@ComponentScan` をまとめたもの。SpringBootアプリケーション起動クラスに付与する。|
| `@EnableAutoConfiguration` |クラス|SpringBoot の自動構成メカニズムを有効化。|

## Test系

|アノテーション          |付与箇所|説明|
|:----------------|:------------|:----|
| `@RunWith(SpringRunner.class)`       |クラス|Spring BootでJUnitテストするときはコレをつける。|
| `@SpringBootTest`                    |クラス|SpringのJava/XML Based Configurationなどの設定を読み込んでくれる。|
| `@AutoConfigureMockMvc`              |メソッド|コントローラ層のモック（ MockMvc ）を作成でき、これでコントローラのJUnitテストが可能になる。|
| `@WebMvcTest(HelloController.class)` |クラス|上記に加えてコントローラクラスを指定することもできる。|
| `@MockBean`                          |フィールド|コントローラ内でDIされるモジュールのモック（Mockito）を作成できる。|

# プロジェクト構造と設定

以下の CleanArchitecture 風なプロジェクト構成を前提に記載します。

- `src/main/java/com/pepese/sample/Application.java` ： SpringBoot 起動クラス
- `src/main/java/com/pepese/sample/config/` ： Java Based Configuration クラス
- `src/main/java/com/pepese/sample/adapter/` ： コントローラとか
- `src/main/java/com/pepese/sample/domain/` ： ドメインとなる Bean クラス
- `src/main/java/com/pepese/sample/infrastructure/` ： リポジトリとか
- `src/main/java/com/pepese/sample/usecase/` ： ユースケースとなる Service クラス
- `src/main/resources/logback.xml` ： ログ設定ファイル

## Java Based Configuration と XML Based Configuration

Spring Framework の設定には以下の 2 種類あります。

- Java Based Configuration
- XML Based Configuration

もはや前者しか使わないと思われますので、前者に限定して記載します。

- Spring設定クラス（ `src/main/java/com/pepese/sample/config/AppConfig.java` に配置）
  - DIやAOPなどの設定
  - `@Configuration` アノテーションを付与
- SpringMVC設定クラス（ `src/main/java/com/pepese/sample/config/WebMvcConfig.java` に配置）
  - コンテンツネゴシエーションなどSpring MVCのDispatcherServletに関する設定
  - `WebMvcConfigurer` を継承
    - Spring4 -> 5、SpringBoot1 -> 2 にかけて `WebMvcConfigurerAdapter` が非推奨となった
  - `@Configuration` アノテーションを付与すること
    - SpringBootの場合、 `@EnableWebMvc` を付与する必要はない
    - SpringBootを利用しないSpring MVCアプリケーションを作成する場合には付与する
- SpringSecurity設定クラス（ `src/main/java/com/pepese/sample/config/WebSecurityConfig.java` に配置）
  - `@Configuration` 、 `@EnableWebSecurity` アノテーションを付与
  - `WebSecurityConfigurerAdapter` を継承
  - `configure` メソッドなどをオーバーライドして利用
- SpringSession設定クラス（ `src/main/java/com/pepese/sample/config/SessionConfig.java` に配置）
  - `@Configuration` 、 `@EnableRedisHttpSession` アノテーションを付与
  - ConnectionFactory などを生成するメソッドを実装して利用

## ロガー

インターフェースに SLF4J 、実装に Logback を利用します。  
SpringBoot の場合は依存に含まれているため、特に POM への追加は必要ありません。  
LoggerFactory に「クラス」or「名前」を指定することで取得できますが、基本は「クラス」になります。

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LoggertTest {
    // クラス
    private static final Logger log = LoggerFactory.getLogger(LoggerTest.class);
}
```

```java
import org.slf4j.Logger;
import org.slf4j.LoggerFactory;

public class LoggertTest {
    // 名前
    private static final Logger log = LoggerFactory.getLogger("MyLogger.test");
}
```

Lombok の `@slf4j` アノテーションをクラスに付与すれば `log` というフィールドで利用可能になります。

```java
import lombok.extern.slf4j.Slf4j;

@slf4j
public class LoggertTest {
}
```

ログメッセージに `{}` を記述すると **プレースフォルダー** として利用できます。

```java
int num1 = 1234;
int num2 = 5678;
log.debug("この数値は{}と{}です。", num1, num2);
// 20:00:33 DEBUG - この数値は1234と5678です。
```

Filter などで MDC に項目を追加できます。

```java
import org.slf4j.MDC;
import org.springframework.web.filter.OncePerRequestFilter;
import lombok.extern.slf4j.Slf4j;

@Slf4j
public class LoggingFilter extends OncePerRequestFilter {
    
  public LoggingFilter() {
    super();
  }

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response, FilterChain filterChain) throws ServletException, IOException {
    String httpmethod = request.getMethod();
    MDC.put("httpMethod", httpmethod); // HTTPメソッドをログに追加
    try {
      filterChain.doFilter(request, response);
    } finally {
    }
  }
}
```

Logback の設定ファイルは `src/main/resources/logback.xml` です。

# おすすめ書籍

<!-- ad link - amazon/rakuten books - springboot -->
<!-- START MoshimoAffiliateEasyLink -->
<script type="text/javascript">
(function(b,c,f,g,a,d,e){b.MoshimoAffiliateObject=a;
b[a]=b[a]||function(){arguments.currentScript=c.currentScript
||c.scripts[c.scripts.length-2];(b[a].q=b[a].q||[]).push(arguments)};
c.getElementById(a)||(d=c.createElement(f),d.src=g,
d.id=a,e=c.getElementsByTagName("body")[0],e.appendChild(d))})
(window,document,"script","//dn.msmstatic.com/site/cardlink/bundle.js?20220329","msmaflink");
msmaflink({"n":"Javaによる高速Webアプリケーション開発のためのSpring Boot入門【電子書籍】[ WINGSプロジェクト 小林昌弘 ]","b":"","t":"","d":"https:\/\/thumbnail.image.rakuten.co.jp","c_p":"","p":["\/@0_mall\/rakutenkobo-ebooks\/cabinet\/4113\/2000008544113.jpg"],"u":{"u":"https:\/\/item.rakuten.co.jp\/rakutenkobo-ebooks\/9f4680988e103ee695a458e7f8bd8d00\/","t":"rakuten","r_v":""},"v":"2.1","b_l":[{"u_bc":"#ff9900","u_tx":"Amazonで見る","u_url":"https:\/\/amzn.to\/3Jx0qrk","s_n":"custom_2","u_so":0,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":2},{"u_bc":"#bf0000","u_tx":"楽天ブックスで見る","u_url":"https:\/\/a.r10.to\/haY69i","s_n":"custom_3","u_so":1,"a_id":0,"p_id":0,"pc_id":0,"pl_id":0,"id":3},{"id":1,"u_tx":"楽天市場で見る","u_bc":"#f76956","u_url":"https:\/\/item.rakuten.co.jp\/rakutenkobo-ebooks\/9f4680988e103ee695a458e7f8bd8d00\/","a_id":3351919,"p_id":54,"pl_id":27059,"pc_id":54,"s_n":"rakuten","u_so":2}],"eid":"eWOl7","s":"s"});
</script>
<div id="msmaflink-eWOl7">リンク</div>
<!-- MoshimoAffiliateEasyLink END -->

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>