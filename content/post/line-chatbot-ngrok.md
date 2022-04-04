---
title:       "LINEチャットボットをFlaskとngrokで"
URL:         "line-chatbot-ngrok"
subtitle:    ""
description: ""
keyword:     ""
date:        2017-11-01
author:      "ぺーぺーSE"
image:       ""
tags:
- flask
- python
- ngrok
- line
categories:
- tech
---

[Flask](http://flask.pocoo.org/) と [ngrok](https://ngrok.com/) でメッセージをオウム返しする LINE のチャットボットを作ってみた。

<!--more-->
Heroku で公開したい場合は以下参照。

- [Heroku入門](https://blog.pepese.com/heroku-basics/)

# セットアップ

```bash
$ pip install Flask gunicorn line-bot-sdk
$ brew cask install ngrok
```

# ソースコード

app.py
```python
from flask import Flask, request, abort
from linebot import LineBotApi, WebhookHandler
from linebot.exceptions import InvalidSignatureError
from linebot.models import MessageEvent, TextMessage, TextSendMessage

app = Flask(__name__)

line_bot_api = LineBotApi('アクセストークン')
handler = WebhookHandler('Channel Secret')

@app.route("/")
def hello_world():
    return "hello world!"

@app.route("/webhook", methods=['POST'])
def webhook():
    # get X-Line-Signature header value
    signature = request.headers['X-Line-Signature']

    # get request body as text
    body = request.get_data(as_text=True)
    app.logger.info("Request body: " + body)

    # handle webhook body
    try:
        handler.handle(body, signature)
    except InvalidSignatureError:
        abort(400)

    return 'OK'


@handler.add(MessageEvent, message=TextMessage)
def handle_message(event):
    line_bot_api.reply_message(
        event.reply_token,
        TextSendMessage(text=event.message.text))


if __name__ == "__main__":
    app.run()
```

Procfile
```
web: gunicorn -b 0.0.0.0:$PORT app:app --log-file=-
```

requirements.txt
```
Flask==0.12.2
gunicorn==19.7.1
line-bot-sdk==1.5.0
```

runtime.txt
```
python-3.6.2
```

# 起動

## ローカルでAP起動

```bash
$ FLASK_APP=app.py flask run
 * Serving Flask app "app"
 * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
```

## ngrokで公開

```bash
$ ngrok http 5000
ngrok by @inconshreveable                                       (Ctrl+C to quit)

Session Status                online                                            
Version                       2.2.8                                             
Region                        United States (us)                                
Web Interface                 http://127.0.0.1:4040                             
Forwarding                    http://8eeb5441.ngrok.io -> localhost:5000        
Forwarding                    https://8eeb5441.ngrok.io -> localhost:5000       

Connections                   ttl     opn     rt1     rt5     p50     p90       
                              2       0       0.02    0.01    0.01    0.01      

HTTP Requests                                                                   
-------------                                                                   

GET /favicon.ico               404 NOT FOUND                                    
GET /                          200 OK
```

なお好きなホスト名をつけたい場合は、ngrokの有料サービスを利用する必要がある模様。

# おすすめ記事

<!-- プログラミング学習のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-programing-learning" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">プログラミング学習のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">プログラミングスキルを上げたい人向けに学習方法について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>

<!-- 転職活動のすすめ -->
<div class="blogcardfu" style="width:auto;max-width:9999px;border:3px solid #FBE599;border-radius:3px;margin:10px 0;padding:15px;line-height:1.4;text-align:left;background:#FFFAEB;"><a href="https://blog.pepese.com/article-job-changing" target="_blank" style="display:block;text-decoration:none;"><span class="blogcardfu-image" style="float:right;width:100px;padding:0 0 0 10px;margin:0 0 5px 5px;"><img src="https://images.weserv.nl/?w=100&url=ssl:blog.pepese.com/img/yaruwo.gif" width="100" style="width:100%;height:auto;max-height:100px;min-width:0;border:0 none;margin:0;"></span><br style="display:none"><span class="blogcardfu-title" style="font-size:112.5%;font-weight:700;color:#333333;margin:0 0 5px 0;">転職活動のすすめ | ぺーぺーSEのブログ</span><br><span class="blogcardfu-content" style="font-size:87.5%;font-weight:400;color:#666666;">ITエンジニアの転職活動の意義について説明します。</span><br><span style="clear:both;display:block;overflow:hidden;height:0;">&nbsp;</span></a></div>