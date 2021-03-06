---
title:       "Cognitoを使ってAngularアプリからユーザ認証する"
URL:         "angular-cognito"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-05-20
author:      "ぺーぺーSE"
image:       ""
tags:
- angular
- javascript
- typescript
- aws
- cognito
categories:
- tech
---

Amazon Cognito を使ってユーザ認証ができる Angular アプリを作成してみる。

<!--more-->

以下の記事の知識を前提とする。

- [Macで開発環境を作る](https://blog.pepese.com/mac-dev-environment/)
- [Angular入門](http://blog.pepese.com/angular-basics/)

# Amazon Cognito の概要

Amazon Cognito はユーザ認証やソーシャル認証の機能を提供するAWSのマネージドサービス。  
大きく以下の機能がある。

- User Pools （ユーザプール）
    - ユーザーディレクトリを作成・管理し、アプリケーションのサインアップとサインイン機能を提供する
    - メールや電話番号の検証、MFA 認証などの拡張セキュリティ機能有り
    - カスタムアトリビュートを設定できる
- Federated Identities （フェデレーテッドアイデンティティ）
    - ユーザの ID を作成し、フェデレーテッド ID プロバイダー（Amazon、Facebook、Google、TwitterなどのOpenID Connect プロバイダおよびSAML ID プロバイダ)
  で認証できる
- Sync
    - アプリケーション関連のユーザーデータのオフラインでのアクセスとデバイス間の同期機能を提供する

ここでは１つ目の **User Pools** を使用する。

## Amazon Cognito User Pools の作成

以下の手順で User Pools を作成する。

1. 「 https://ap-northeast-1.console.aws.amazon.com/cognito/ 」（Tokyoリージョンの場合）へアクセスする
2. 「Manage your User Pools」をクリック
3. 「Create a User Pool」をクリック
4. 任意の User Pool 名を入力して「Step through settings」をクリック
    - 「Review defaults」を設定すると各種設定画面をスキップしてデフォルト値が設定される
5. [Attribute 設定画面] ユーザプロファイルとして管理したい項目を選択して「Next step」をクリックする
    - ここでは「email」「phone number」を選択する
6. [Policies 設定画面] パスワードの設定ポリシー、ユーザの作成権限ポリシー、使用されないユーザアカウントのTTLを設定して「Next step」をクリックする
7. [Verifications 設定画面] MFAの使用如何、Verification Code の送信先（E-Mail or SMS）、SMS を使用する場合は Cognito へ Role の付与を設定して「Next step」をクリックする
    - ユーザがサインアップした際、 E-Mail か SMS へ数字6桁の Verification Code が送付され初回の本人認証に使用する
    - ここでは email を選択する
8. [Message Customization 設定画面] 送付するメッセージをカスタマイズして「Next step」をクリックする
9. [Tags 設定画面] タグを設定して「Next step」をクリックする
10. [Devices 設定画面] ユーザがアクセスしてきたデバイスを記憶するか否かを設定して「Next step」をクリックする
11. [App clients 設定画面] AWS-SDK を使用したクライアントアプリケーションから User Pool へアクセスする場合は「Add an app client」を実施して「Next step」をクリックする
    - Client の作成では、App client 名、 Client のシークレットキーを使用するか否か、認証用の HTTP ヘッダを使用するか否かを設定して」「Create app client」をクリックする
    - ここでは「Generate client secret」のチェックボックスを外し、シークレットキーを使用しないこととする
12. [Triggers 設定画面] 認証フローの各種チェックポイントで AWS Lambda を実行するか否かを設定して「Next step」をクリックする
13. [Review 画面] 最後にこれまでの設定を確認して「Create pool」をクリックする

以上の手順で User Pools が作成できる。  
Pool details 画面の **Pool Id** と、 App clients 画面の **App client id** は後ほど使用するのでひかえておく。

# Cognito で認証するAngularアプリを作成する

```bash
$ npm upgrade -g @angular/cli
$ ndenv rehash
$ ng new cognito-js --style=scss
$ cd cognito-js
$ npm install amazon-cognito-identity-js --save # 依存から aws-sdk も導入される
$ ng generate service services/cognito
```

`aws-sdk` には `@type/aws-sdk` が含まれている模様。（[参考](https://www.npmjs.com/package/@types/aws-sdk)）  
`src/tsconfig.app.json` を以下のように編集。

```javascript
{
  "extends": "../tsconfig.json",
  "compilerOptions": {
    "outDir": "../out-tsc/app",
    "module": "es2015",
    "baseUrl": "",
    "types": ["node"] # ここを加筆
  },
  "exclude": [
    "test.ts",
    "**/*.spec.ts"
  ]
}
```

`src/app/services/cognito.service.ts` を以下のように修正。

```typescript
import { Injectable } from '@angular/core';
import * as AWS from "aws-sdk";
import { CognitoUserPool, CognitoUserAttribute, CognitoUser, AuthenticationDetails } from 'amazon-cognito-identity-js';
import { environment } from '../../environments/environment';

@Injectable()
export class CognitoService {
  userPool = null;

  constructor() {
    AWS.config.region = environment.region;
    const data = { UserPoolId: environment.userPoolId, ClientId: environment.clientId};
    this.userPool = new CognitoUserPool(data);
  }

  signUp(username, password, email, phone) {
    const userData = {
      Username : username,
      Pool : this.userPool,
      Storage: sessionStorage
    };
    let attributeList = [];
    const dataEmail = {
      Name : 'email',
      Value : email
    };
    const dataPhoneNumber = {
      Name : 'phone_number',
      Value : phone
    };
    let attributeEmail = new CognitoUserAttribute(dataEmail);
    let attributePhoneNumber = new CognitoUserAttribute(dataPhoneNumber);
    attributeList.push(attributeEmail);
    attributeList.push(attributePhoneNumber);
    this.userPool.signUp(username, password, attributeList, null, function(err, result){
      if (err) {
        alert(err);
        return;
      }
      const cognitoUser = result.user;
      alert("SignUp is success!\nUser name is " + cognitoUser.getUsername() + ".\nYou need to check your SMS or E-Mail.");
    });
    return;
  }

  confirmRegistration(username, verification_code) {
    const userData = {
      Username : username,
      Pool : this.userPool,
      Storage: sessionStorage
    };
    const cognitoUser = new CognitoUser(userData);
    cognitoUser.confirmRegistration(verification_code, true, function(err, result) {
      if (err) {
        alert(err);
        return;
      }
      alert('Registration is success!');
      console.log('call result: ' + result);
    });
    return;
  }

  signIn(username, password) {
    const userData = {
      Username : username,
      Pool : this.userPool,
      Storage: sessionStorage
    };
    const cognitoUser = new CognitoUser(userData);
    const authenticationData = {
        Username : username,
        Password : password
    };
    const authenticationDetails = new AuthenticationDetails(authenticationData);
    cognitoUser.authenticateUser(authenticationDetails, {
      onSuccess: function (result) {
        alert('SignIn is success!');
        console.log('access token + ' + result.getAccessToken().getJwtToken());
      },
      onFailure: function(err) {
        alert(err);
      }
    });
    return;
  }
}
```

`src/app/app.component.ts` を以下のように修正。

```typescript
import { Component } from '@angular/core';
import { Http, Response, Headers, RequestOptions } from '@angular/http';
import { Observable } from 'rxjs/Observable';
import 'rxjs/add/operator/map';
import { CognitoService } from './services/cognito.service';
import { environment } from '../environments/environment';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.scss'],
  providers: [CognitoService]
})
export class AppComponent {
  title = 'Cognito Sample';
  username = '';
  password = ``;
  email = ``;
  phone = ``;
  verification_code = ``;
  url = '';
  result = '';

  constructor(
    private cognitoService: CognitoService,
    private http: Http
  ){}

  siginUp() {
    this.cognitoService.signUp(this.username, this.password, this.email, this.phone);
  }

  confirmRegistration() {
    this.cognitoService.confirmRegistration(this.username, this.verification_code);
  }

  signIn() {
    this.cognitoService.signIn(this.username, this.password);
  }

  get() {
    let key = 'CognitoIdentityServiceProvider.' + environment.clientId + '.' + this.username + '.idToken';
    let idToken = sessionStorage.getItem(key);
    let headers = new Headers({ 'Authorization': idToken });
    let options = new RequestOptions({ headers: headers });
    return this.http.get(this.url, options)
                    .map(res => res.text())
                    .subscribe(t => this.result = t);
  }
}
```

`src/app/app.component.html` を以下のように修正。

```html
<h1>
  {{title}}
</h1>
<hr />
<div>
  <h2>サインアップ</h2>
  <p>以下の項目を入力してユーザを新規に仮登録します。</p>
  <table>
    <tr><td>ユーザ名：</td><td><input type="text" [(ngModel)]="username"></td><tr>
    <tr><td>パスワード：</td><td><input type="text" [(ngModel)]="password"></td></tr>
    <tr><td>メールアドレス：</td><td><input type="text" [(ngModel)]="email"></td></tr>
    <tr><td>携帯番号：</td><td><input type="text" [(ngModel)]="phone"></td></tr>
  </table>
  <button (click)="siginUp()">サインアップ</button>
</div>
<hr />
<div>
  <h2>認証コードの確認</h2>
  <p>サインアップしたユーザにメールやSMSで送付された認証コードを確認し、ユーザを本登録します。</p>
  <p>認証コードは6桁数字です。</p>
  <table>
    <tr><td>ユーザ名：</td><td>{{username}}</td></tr>
    <tr><td>認証コード：</td><td><input type="text" [(ngModel)]="verification_code"></td></tr>
  </table>
  <button (click)="confirmRegistration()">確認</button>
</div>
<hr />
<div>
  <h2>サインイン</h2>
  <p>本登録したユーザでサインイン（ログイン）します。</p>
  <table>
    <tr><td>ユーザ名：</td><td>{{username}}</td></tr>
    <tr><td>パスワード：</td><td>{{password}}</td></tr>
  </table>
  <button (click)="signIn()">サインイン</button>
</div>
<hr />
<div>
  <h2>GET アクセス</h2>
  <p>Session StorageのidTokenをAuthorizationヘッダへ付与してGetリクエストを送付します。</p>
  <p>API GatewayのAuthorizationなどで試してみてください。</p>
  <table>
    <tr><td>URL：　</td><td><input type="text" [(ngModel)]="url"></td></tr>
  </table>
  <button (click)="get()">Send</button>
  <table>
    <tr><td>{{result}}</td></tr>
  </table>
</div>
```

`src/environments/environment.ts` を以下のように修正。

```typescript
export const environment = {
  production: false,
  region: 'ap-northeast-1',
  userPoolId: 'ap-northeast-1_xxxxxxxxx',
  clientId: 'xxxxxxxxxxxxxxxxxxxxxxxxxx'
};
```

`$ ng serve` して起動を確認。

# API GatewayでCognitoで認証されたユーザを認可する

以下を参照。

- [Cognito UserPoolを使ってAPIを保護しよう](http://www.h4a.jp/detail/25148)
- [API Gateway リソースの CORS を有効にする](http://docs.aws.amazon.com/ja_jp/apigateway/latest/developerguide/how-to-cors.html)

# その他参考

- チュートリアル: JavaScript アプリのユーザープールを統合する
    - http://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/tutorial-integrating-user-pools-javascript.html
- 例: JavaScript SDK の使用
    - http://docs.aws.amazon.com/ja_jp/cognito/latest/developerguide/using-amazon-cognito-user-identity-pools-javascript-examples.html
- プロキシ設定が必要な場合（いらんかった）
    - http://docs.aws.amazon.com/sdk-for-javascript/v2/developer-guide/node-configuring-proxies.html
- [JSON Web Token の効用](http://qiita.com/kaiinui/items/21ec7cc8a1130a1a103a)

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>