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

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
  xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <groupId>com.pepese.sample</groupId>
  <artifactId>dynamodb-sample</artifactId>
  <version>0.0.1-SNAPSHOT</version>
  <packaging>jar</packaging>
  <name>dynamodb-sample</name>

  <parent>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-parent</artifactId>
    <version>1.5.6.RELEASE</version>
    <relativePath />
  </parent>

  <properties>
    <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
    <project.reporting.outputEncoding>UTF-8</project.reporting.outputEncoding>
    <java.version>1.8</java.version>
  </properties>

  <dependencies>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter</artifactId>
    </dependency>
    <dependency>
      <groupId>com.amazonaws</groupId>
      <artifactId>aws-java-sdk-dynamodb</artifactId>
      <version>1.11.185</version>
    </dependency>
    <dependency>
      <groupId>org.projectlombok</groupId>
      <artifactId>lombok</artifactId>
    </dependency>
    <dependency>
      <groupId>org.springframework.boot</groupId>
      <artifactId>spring-boot-starter-test</artifactId>
    </dependency>
  </dependencies>

  <build>
    <plugins>
      <plugin>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

## com.pepese.sample.App.java

```java
package com.pepese.sample.dynamodb;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.Iterator;
import java.util.List;
import java.util.Map;
import java.util.Set;

import javax.annotation.PostConstruct;

import org.springframework.beans.factory.annotation.Value;
import org.springframework.stereotype.Service;

import com.amazonaws.services.dynamodbv2.AmazonDynamoDB;
import com.amazonaws.services.dynamodbv2.AmazonDynamoDBClientBuilder;
import com.amazonaws.services.dynamodbv2.model.AttributeValue;
import com.amazonaws.services.dynamodbv2.model.BatchWriteItemRequest;
import com.amazonaws.services.dynamodbv2.model.BatchWriteItemResult;
import com.amazonaws.services.dynamodbv2.model.DeleteItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemRequest;
import com.amazonaws.services.dynamodbv2.model.GetItemResult;
import com.amazonaws.services.dynamodbv2.model.PutItemRequest;
import com.amazonaws.services.dynamodbv2.model.QueryRequest;
import com.amazonaws.services.dynamodbv2.model.QueryResult;
import com.amazonaws.services.dynamodbv2.model.ReturnConsumedCapacity;
import com.amazonaws.services.dynamodbv2.model.ScanRequest;
import com.amazonaws.services.dynamodbv2.model.ScanResult;
import com.amazonaws.services.dynamodbv2.model.WriteRequest;

import lombok.extern.slf4j.Slf4j;

@Service
@Slf4j
public class DynamoDBClient {

	private static AmazonDynamoDB client;

	@Value("${aws.dynamodb.region:AP_NORTHEAST_1}")
	private String region;

	@PostConstruct
	public void init() {
		// 低レベルAPI を使用する
		client = AmazonDynamoDBClientBuilder.standard().withRegion(region).build();
		// default で、back-off strategy アルゴリズムでエラーリトライ 3 回される
		// そのため、各メソッドでリトライの実装はしない（batch系は除く）
	}

	public void putItem(String tableName, Map<String, AttributeValue> item) {
		if (tableName == null || item == null || item.size() == 0) {
			return;
		}
		PutItemRequest request = new PutItemRequest().withTableName(tableName).withItem(item);
		try {
			client.putItem(request);
			log.debug("PutItem is succeeded.");
		} catch (Exception e) {
			throwRuntimeException("PutItem is failed.", e);
		}
	}

	public Map<String, AttributeValue> getItem(String tableName, Map<String, AttributeValue> key) {
		if (tableName == null || key == null || key.size() == 0) {
			return null;
		}
		GetItemRequest request = new GetItemRequest().withTableName(tableName).withKey(key);
		GetItemResult result = null;
		try {
			result = client.getItem(request);
			if (result != null && result.getItem() != null) {
				log.debug("GetItem is succeeded.");
			} else {
				log.debug("GetItem is succeeded but no matching key is found.");
			}
		} catch (Exception e) {
			throwRuntimeException("GetItem is failed.", e);
		}
		return result != null ? result.getItem() : null;
	}

	public List<Map<String, AttributeValue>> query(String tableName, String keyConditionExpression,
			Map<String, AttributeValue> expressionAttributeValues) {
		if (tableName == null || keyConditionExpression == null || expressionAttributeValues == null) {
			return null;
		}
		QueryRequest request = new QueryRequest().withTableName(tableName)
				.withKeyConditionExpression(keyConditionExpression)
				.withExpressionAttributeValues(expressionAttributeValues);
		QueryResult result = null;
		try {
			result = client.query(request);
			if (result != null && result.getItems() != null) {
				log.debug("Query is succeeded.");
			} else {
				log.debug("Query is succeeded but no matching key is found.");
			}
		} catch (Exception e) {
			throwRuntimeException("Query is failed.", e);
		}
		return result != null ? result.getItems() : null;
	}

	public List<Map<String, AttributeValue>> scan(String tableName) {
		if (tableName == null) {
			return null;
		}
		ScanRequest request = new ScanRequest().withTableName(tableName);
		ScanResult result = null;
		try {
			result = client.scan(request);
			if (result != null && result.getItems() != null) {
				log.debug("Scan result is succeeded.");
			} else {
				log.debug("Scan result is succeeded but no matching key was found.");
			}
		} catch (Exception e) {
			throwRuntimeException("Scan is failed.", e);
		}
		return result != null ? result.getItems() : null;
	}

	public void deleteItem(String tableName, Map<String, AttributeValue> key) {
		if (tableName == null || key == null || key.size() == 0) {
			return;
		}
		DeleteItemRequest request = new DeleteItemRequest().withTableName(tableName).withKey(key);
		try {
			client.deleteItem(request);
			log.debug("DeleteItem is succeeded.");
		} catch (Exception e) {
			throwRuntimeException("DeleteItem is failed.", e);
		}
	}

	// 作成中
	public void batchWrite(Map<String, List<WriteRequest>> _items) {
		if (_items == null || _items.isEmpty()) {
			return;
		}
		int batchWriteMaxNum = 25;
		Set<String> tables = _items.keySet();
		int remainingRequestNum = 0;
		Map<String, List<WriteRequest>> items = new HashMap<String, List<WriteRequest>>();
		// テーブル毎のループ
		for (Iterator<String> tableIt = tables.iterator(); tableIt.hasNext();) {
			String table = tableIt.next();
			List<WriteRequest> _writeRequests = _items.get(table);
			List<WriteRequest> writeRequests = new ArrayList<WriteRequest>();
			// 1テーブルに対するリクエストのループ
			for (Iterator<WriteRequest> writeRequestsIt = _writeRequests.iterator(); writeRequestsIt.hasNext();) {
				WriteRequest writeRequest = writeRequestsIt.next();
				writeRequests.add(writeRequest);
				// リクエスト数がbatchWriteMaxNumに達した場合
				if (writeRequests.size() == batchWriteMaxNum - remainingRequestNum) {
					items.put(table, writeRequests);
					batchWriteCore(items);
					writeRequests = new ArrayList<WriteRequest>();
					items = new HashMap<String, List<WriteRequest>>();
					remainingRequestNum = 0;
					// あるテーブルのリクエスト数がbatchWriteMaxNum未満だが、次のテーブルがある場合
				} else if (!writeRequestsIt.hasNext() && tableIt.hasNext()) {
					items.put(table, writeRequests);
					remainingRequestNum += writeRequests.size();
					writeRequests = new ArrayList<WriteRequest>();
					// 最終ループの場合
				} else if (!writeRequestsIt.hasNext() && !tableIt.hasNext() && writeRequests.size() > 0) {
					items.put(table, writeRequests);
					batchWriteCore(items);
				}
			}
		}
	}

	private void batchWriteCore(Map<String, List<WriteRequest>> items) {
		if (log.isDebugEnabled()) {
			int batchWriteSize = 0;
			for (String table : items.keySet()) {
				batchWriteSize += items.get(table).size();
			}
			log.debug("BatchWrite size is " + batchWriteSize);
		}
		try {
			BatchWriteItemRequest bwir = new BatchWriteItemRequest()
					.withReturnConsumedCapacity(ReturnConsumedCapacity.TOTAL).withRequestItems(items);
			BatchWriteItemResult result = client.batchWriteItem(bwir);
			if (result == null) {
				return;
			}
			log.debug("batchWrite() CC: " + result.getConsumedCapacity()); // 消費されたキャパシティーユニット

			if (result.getUnprocessedItems() != null && !result.getUnprocessedItems().isEmpty()) {
				Thread.sleep(1000); // Exponential Backoff アルゴリズムに変更する？
				log.warn("UNPROCESSED " + result.getUnprocessedItems().size());
				batchWrite(result.getUnprocessedItems());
			}
		} catch (Exception e) {
			throwRuntimeException("BatchWrite is failed.", e);
		}
	}

	private void throwRuntimeException(String message, Exception e) {
		log.error(message, e);
		throw new RuntimeException(message, e);
	}
}
```
