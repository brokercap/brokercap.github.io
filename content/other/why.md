---
title: "为什么选择 Bifrost"
lastmod: 2019-11-27T23:04:15Z
draft: false
weight: 101
---


#### 1. 很多人对于 Bifrost Binlog 解析及同步会有几个疑问

1). 为什么我不用 canal ,自己将数据 同步到 kafka ,然后业务再从 kafka 消费出来处理 . 这样我想怎么同步就怎么同步

2). Bifrost 有什么优势 ?


##### 反问

假如要自己去实现 canal 或者 其他 Binlog 解析工具(包括 Bristol) 同步到 kafka ,再从 kafka 消费出来,自己实现同步 ? 请问需要去实现哪些功能?

1). 流量监控

请问你不想知道你的工具什么时候同步了多少数据吗?

2). 监控报警

整个应用过程中,MySQL 连接不上,或者 目标库 连接不上 咋发现呢 ?

同步目标库的过程中,要是某一数据同步同步失败了 咋发现呢 ? 

假如因为某一条数据本身有问题了,要不要提供机制错过数据等功能 ?

3). 目标库丰富性

假如一个表的数据要同步多个地方去,是不是就要写多套程序 ?


##### Bifrost 优势

1). 自带流量监控,哪一个表同步了多少数据,一目了然

2). 自带报警功能,数据源,目标库连接不上,及 某一条数同步出错了,可以报警出来

3). 丰富的插件支持

* Redis
* Memcache
* RabbitMQ
* ActiveMQ
* Kafka
* Mongodb
* ClickHouse
* MySQL
* [Http 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/plugin/http/example/http_server/http_server.go)
* [Hprose RPC 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/hprose_server/tcp_server.go)

* TableCount

4). MySQL 多个版本支持

* 5.1 - 8.0

5). 支持 *增量* 和 *全量* 数据同步

6). 界面动态配置,可视化管理

在线管理数据源,要同步表等操作

7). 部署简单,不烧硬件

go 语言编写,一键安装

1核 CPU,  256MB 内存 都能运行 

表的目标库同步是异步同步的,多个目标库多个协程同时同步


#### 2. 小结

Bifost 不能解决所有问题

但如果现在的工具,为什么不用呢?

