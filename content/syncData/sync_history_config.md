---
title: "全量任务配置"
lastmod: 2019-11-23T15:30:15Z
draft: false
weight: 55
---

全量任务同步 是建立在  表配置同步到目标库  之上

假如没有 配置表同步到目标库 则不能配置全量同步


### 配置过程

#### 1. 点击 刷全量数据 按钮

![image](/images/syncData/tableSync_history_add_1.jpg)

#### 2. 配置查询全量数据的条件

![image](/images/syncData/tableSync_history_add_2.jpg)

*字段解析*


Where : 支持写 where 条件 ,比如  update_time >= '2019-11-23 00:00:00' AND user_id = 100

ThreadNum : 开启多少个连接同步进行 SELECT 语句查询

ThreadCountPer : 每次 SELECT 查询多少条数据

ToServer : 选择此次刷全量数据,需要刷到哪些目标库里去


#### 3. 点击保存

假如写的 where 条件有问题,则会提示 错误 ,保存不了


#### 4. 保存成功之后 提示成功,并点击确认跳转到 全量任务列表

#### 5. 点击 start 按钮

#### 6. 全量任务 列表查询

DbName : 数据源名称

SchemaName : 数据库名

TableName : 表名

##### Property : 连接配置同步

1). ThreadNum : 几个连接

2). ThreadCountPer : 每个连接每次最多查询几条数据

3). Where : where 条件

NowStartI : 下一次查询从第几条数据开始

##### ThreadPool : 连接池的查询情况

1). Num : 第几个连接

2). NowStartI : 当前连接是从第几条数据开始查询 ,LIMIT N,100 中的 N

3). Error : 查询过程中遇到的错误信息

ToServerIDList : 同步到哪些目标库

StartTime : 开始时间

OverTime : 结束时间,整个全量查询所用的时间,这个时间 并不代表是整个表的数据已经同步到了目标库,只是从表中查询出来,放已提交给了同步的队列中去

##### Status : 全量任务的状态

1). close : 关闭

2). running : 正在查询

3). halfway : 查询过程中,有连接中途出错了

4). over : 查询结束

5). killed : 查询的过程中被stop掉了
