---
title: "批量同步配置"
lastmod: 2019-11-27T21:05:15Z
draft: false
weight: 52
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

![image](/images/syncData/tableSyncAddBatch_1.jpg)

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


#### 3. 设置所有库或者某个库所有表同步到同一个目标库

![image](/images/syncData/tableSyncAddBatch_2.jpg)

1). 选择要同步的库名(假如库名为 AllDataBases ,则代表所有库)

2). 选中 AllTables 表名( 假如选择的库为 AllDataBases ,又选择了 AllTables 表,则代表所有库及表)

3). 按选择目标库配置来配置

4). 点击 提交 按钮,假如不支持批量配置的插件,将会提示不支持


#### 4. 标签

在配置数据同步的插件模块里,是可以使用标签的

标签支持程度，取决于各个插件的实现,请参考各插件的文档

{$SchemaName} : 数据库名字

{$TableName} : 表的名字

{$EventType} : 当前记录的增删改事件,新增(insert); 修改(update) ; 删除(delete); SQL(sql)

{$Timestamp} : 当前Bifrost同机器的时间戳

{$BinlogTimestamp} : Binlog 发生的时间戳

{$BinlogFileNum} : Binlog文件编号,并不是 整个Binlog文件名,比如 binlog 文件是 mysql-bin.000001 那这个 BinlogFileNum 的值 是1

{$BinlogPosition} : Binlog position 位点

{$字段名} : 表的字段数据



#### 5. 备注

1). 源数据的库名不能是关键字 AllDataBases

2). 源数据的表名不能是关键字 AllTables

3). 批量配置同步配置的时候,不支持选择字段,选择了也是无效

4). 假如配置的时候,使用了 {$id} 这样的字段名标签 , 假如源表里没有这样的字段,将会转换成""











