---
layout: post
title: 浅淡使用Kafka连接Elasticsearch的注意事项
tags: [Big Data]
comments: true
---
* [问题描述](#%E9%97%AE%E9%A2%98%E6%8F%8F%E8%BF%B0)
* [kafka Connector](#kafka-connector)
* [问题的解决方案](#%E9%97%AE%E9%A2%98%E7%9A%84%E8%A7%A3%E5%86%B3%E6%96%B9%E6%A1%88)

## 问题描述
在微服务架构中，通常会使用kafka作为消息队列，一方面进行上游生产者service和下游消费者service的解耦合，一方面还能进行数据的持久化。其中，由confluentinc提供的kafka-connect-elasticsearch是一个很轻量级的工具

[kafka-connect-elasticsearch](https://github.com/confluentinc/kafka-connect-elasticsearch)

在Elasticsearch这个文档数据库中，每条记录都会有一个id和它对应，这个connector支持帮我们自动地生成这个id，格式为KAFKA_TOPIC+PARTITION_ID+KAFKA_OFFSET (for example, events+0+12333)，作为elasticsearch的ID。但是当我们在生产环境中使用新的一个kafka集群中，新的kafka偏移量会从0开始。这样就会导致新进来的id会把旧的id给冲掉，本质的问题是由于connector生成的id不够unique，只能保证在单个kafka集群中的unique，如果我们需要跨kafka集群的进行id unique的保证，我们需要做更多的事情。

## kafka Connector
我们可以使用kafka connector的transformation功能来给topic加上一个cluster name，来获得一个全局unique的topic name。
Connector Properties，主要是为connector worker配置，包括kafka连接的配置，序列化格式以及commit间隔。支持以下几种配置：
1. bootstrap.servers：想要连接的kafka集群地址
2. key.converter： kafka message中key的格式，比如org.apache.kafka.connect.json.JsonConverter
3. value.converter：kafka message中value的格式，比如 rg.apache.kafka.connect.json.JsonConverter
4. ffset.storage.file.filename：那个来保存offset数据的文件

除了基本的worker properties file，我们还需要一个connector的properties file来配置connector本身。Connector的配置文件主要有以下几个重要的配置项：
1. name: 这个connector的unique的名字
2. connector.class: 这个connector所对应的java的类
3. task：这个connector所对应的最大task的数量
4. topics: 一系列topic names，作为这个kafka conenctors的输入

## 问题的解决方案
我们可以使用connector中的transformations配置来修复connector创建的elasticsearch id不一致的问题。这章就让我们来详细看下conenctor中的transformation配置。

Connector能够经由transformations来配置成轻量级的消息改动，比如我们可以在小溪上添加一个静态的field来修改这个message。我们在例子中使用的JSON是一个schemaless的JSON数据。所以我们需要在connector的配置文件中，将以下两个配置设置成false：
~~~java
key.converter.schemas.enable = false
value.converter.schemas.enable = false
~~~
然后我们可以来看个message转换的例子
假设我们每行message是一个line，我们需要将它wrap到一个Map，然后加上一个field，这里我们需要两个transformation,HoistField和InsertField，例子如下
~~~java
transforms=MakeMap, InsertSource
transforms.MakeMap.type=org.apache.kafka.connect.transforms.HoistField$Value
transforms.MakeMap.field=line
transforms.InsertSource.type=org.apache.kafka.connect.transforms.InsertField$Value
transforms.InsertSource.static.field=data_source
transforms.InsertSource.static.value=test-file-source
~~~
分别定义了两个tranformation的别名，MakeMap和InsertSource，这两个transformation是有先后顺序的，分别定义了一个transformation chain。然后设置type，是这种transformation的类名。在我们的例子中，我们会着重用一个RegexRouter的transformation，它可以修改某一个record所在的topic，根据替换string和正则表达式。
~~~java
"transforms":"addPrefix",
"transforms.addPrefix.type":"org.apache.kafka.connect.transforms.RegexRouter",
"transforms.addPrefix.regex":"(.*)",
"transforms.addPrefix.replacement":"east1-$1"
~~~





