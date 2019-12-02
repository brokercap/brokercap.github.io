---
title: "插件管理"
publishdate: 2019-11-23T15:32:15Z
draft: false
weight: 3
---

![image](/images/plugin/plugin_list.jpg)

Bifrost v1.1.0 支持 12 个插件

当前版本每个插件都有对应连接使用文档

每个插件都有不同的功能


#### 1. Redis

支持将数据静实时同步到 Redis,可以自定义字段或者内容

#### 2. Memcache

支持将数据静实时同步到 Memcache,可以自定义字段或者内容

#### 3. RabbitMQ

支持将数据同步到指定的 RabbitMQ 队列中,支持 持久化,非持久化,自动创建队列等操作

#### 4. ActiveMQ

支持将数据同步到指定的 ActvieQ 队列中

#### 5. Kafka

支持将数据同步到 Kafka 中

#### 6. Mongodb

支持将数据同步 Mongodb ,自动按 MySQL 表中的索引创建索引 ,及 文档ID

#### 7. ClickHouse

同步到 ClickHouse ,支持 普通同步模式, MySQL 中删除之后 ClickHouse 也相应删除的方式

也支持将 MySQL 中的 delete update 修改删除操作,转成以日志的方式写入到 ClickHouse

#### 8. MySQL

类似于 MySQL 从库的方式,但不支持 DDL, 支持数据表结构不一样,数据类型自动转换

#### 9. [Http 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/plugin/http/example/http_server/http_server.go)

以 Http 的方式推送到自定义的 http server中

#### 10. [Hprose RPC 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/hprose_server/tcp_server.go)

以 hprose rpc 的方式 推送到自定义的  rpc sereve 中

#### 11. TableCount

TableCount 插件是统计每个表的 insert , update , delete 的写入次数 和  条数, 以折线图的方式显示出来, 用于判断分析哪个表的写入量及QPS等场景使用

#### 12. blackhole

blackhole 插件, 思想来源于 MySQL 里的 blackhole 引擎. 

blackhole 没有任何逻辑计算,当数据从 Bifrost server 层传到 blackhole 插件的时候,立马进行返回,不做任何逻辑计算
