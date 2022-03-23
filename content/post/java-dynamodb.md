---
title:       "JavaでDynamoDBへアクセスする"
URL:         "java-dynamodb"
subtitle:    ""
description: ""
date:        2017-08-30
author:      "ぺーぺーSE"
image:       ""
tags:
- java
- maven
- aws
- dynamodb
categories:
- tech
---

Java から DynamoDB にアクセスしてみる。

<!--more-->

以下の記事を読んだ前提で書く。

- [Macで開発環境を作る](https://blog.pepese.com/mac-dev-environment/)
- [DynamoDB入門](https://blog.pepese.com/dynamodb-basics/)

# API・インターフェースの種類

AWS SDK for Java には以下のインターフェースがある。

- Low-Level Interface
    - com.amazonaws.services.dynamodbv2.AmazonDynamoDB
    - テーブルの CRUD 、 Item の CRUD が可能
    - [データ型識別子](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html#Programming.LowLevelAPI.DataTypeDescriptors)を指定する必要がある
- Document Interface
    - com.amazonaws.services.dynamodbv2.document.DynamoDB
    - テーブルの CR 、 Item の CRUD が可能
    - [データ型識別子](http://docs.aws.amazon.com/ja_jp/amazondynamodb/latest/developerguide/Programming.LowLevelAPI.html#Programming.LowLevelAPI.DataTypeDescriptors)を指定する必要がない
- High-Level Interface
    - com.amazonaws.services.dynamodbv2.datamodeling.DynamoDBMapper
    - テーブル操作はできないが、 Item の CRUD が可能
    - Java オブジェクトを DynamoDB テーブルと属性にマッピング可能

ここでは、 Low-Level Interface を使用する。

# 環境構築

```sh
$ mvn archetype:generate \
  -DgroupId=com.pepese.sample \
  -DartifactId=dynamodb-sample \
  -DarchetypeArtifactId=maven-archetype-quickstart \
  -DinteractiveMode=false
$ cd dynamodb-sample
$ mvn eclipse:eclipse
```

# 実装

- pom.xml
- com.pepese.sample.App.java

## pom.xml

<script src="https://gist-it.appspot.com/github/pepese/java-sample/blob/master/master/dynamodb-sample/pom.xml?footer=0"></script>

## com.pepese.sample.App.java

<script src="https://gist-it.appspot.com/github/pepese/java-sample/blob/master/dynamodb-sample/src/main/java/com/pepese/sample/dynamodb/DynamoDBClient.java?footer=0"></script>
