---
title: "批量同步配置"
lastmod: 2020-09-08T20:49:15Z
draft: false
weight: 53
---

#### 批量配置的使用场景:

1. 有多个表要设置以同样的配置需要同步同一个 目标库里去

2. 要设置整个所有库或者某一个库里的所有表,包括以后新增的表同步到kafka 等目标库里去



Bifrost 支持两种批量配置的方式,一种是 选择多个表,点击批量添加的按钮,第二种是 配置整个库的表的所有表,包括以后新增的表


#### 1. 批量绑定表和通道

![image](/images/syncData/tableBindChannel_batch_1.jpg)
![image](/images/syncData/tableBindChannel_batch_2.jpg)

1). 选中要批量绑定表的 check 框

2). 点击批量绑定,弹出一个框,点击保存

3). 绑定成功会显示日志

```
开始添加

binlog_field_hprose_plugin_test ADD Result:success

binlog_field_http_plugin_test ADD Result:success

添加完成


```

#### 2. 批量设置多个表以同样的配置同步到同一个目标库

##### 2.1 不同表结构的表

![image](/images/syncData/tableSyncAddBatch_1.jpg)

不同表结构的表,批量配置同步到同一个目标库,取决于插件的实现是否支持,Kafka,http,Redis 等没有数据结构要求的表,是支持

请查看相关插件,是否支持不同表结构的表批量配置


1). 选中要批量设置同步表的 check 框 (必须绑定了同步的表才有生效,没绑定通道的表 无效)

2). 按选择目标库配置来配置 , 批量批量的表,选择字段无效

3). 点击 批量添加  的按钮, 假如不支持批量配置的插件,将会提示不支持

4). 批量绑定成功后会日志

```
开始提交

binlog_field_test success

bristol_performance_test success

执行完成
```

##### 2.2 进行横拆的表,数据结构相同,有规律的表名的表

Bifrost V1.4 版本之后支持将多个相同表结构的表,统一配置同步,并且后续新增的表,也自动匹配

![image](/images/syncData/tableMathingSyncAddBatch_1.png)

假如横拆的表全名规则 是 表名_1,表名_2 这样的规则

例如:  binlog_field_test_1,binlog_field_test_2,binlog_field_test_3

1). 点击其中一个表的ADD按钮

2). 将 FuzzyMatching 选项修改为 YES

3). 修改 TableName 为 binlog_field_test_*

4). 选择通道

5). 点击 添加 按钮

添加完之后,在表列表末尾将会出现一个 binlog_field_test_* 虚拟表名,可以对这个虚拟表,进行设置同步

![image](/images/syncData/tableMathingSyncAddBatch_2.png)



#### 3. 设置所有库或者某个库所有表同步到同一个目标库

![image](/images/syncData/tableSyncAddBatch_2.jpg)

1). 选择要同步的库名(假如库名为 AllDataBases ,则代表所有库)

2). 选中 AllTables 表名( 假如选择的库为 AllDataBases ,又选择了 AllTables 表,则代表所有库及表)

3). 按选择目标库配置来配置

4). 点击 提交 按钮,假如不支持批量配置的插件,将会提示不支持


#### 4. 模糊匹配表配置规则


在点击添加表和通道绑定的界面，可以选择 FuzzyMatching 为 Yes, 可以进表名进行编辑

模块匹配的表名，必须带有 *

案例

假如 bifrost_test 库有以下的表

bak_binlog_field_test_mytest

binlog_field_test

binlog_field_test_1

binlog_field_test_2

binlog_field_test_3

table_nodata

table_nodata_2

 

规则1：

binlog_field_test_*

匹配表：binlog_field_test_1 , binlog_field_test_2 , binlog_field_test_3

不会匹配: bak_binlog_field_test_mytest 和 binlog_field_test




#### 5. 备注

1). 源数据的库名不能是关键字 AllDataBases

2). 源数据的表名不能是关键字 AllTables

3). 批量配置同步配置的时候,不支持选择字段,选择了也是无效

4). 假如配置的时候,使用了 {$id} 这样的字段名标签 , 假如源表里没有这样的字段,将会转换成""











