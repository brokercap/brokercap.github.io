---
title: "标签使用"
lastmod: 2020-09-08T20:45:15Z
draft: false
weight: 52
---

### 一. 标签使用

表配置同步过程中,支持使用标签,代替相对应的数据

{$SchemaName} : 数据库名字

{$TableName} : 表的名字

{$EventType} : 当前记录的增删改事件,新增(insert); 修改(update) ; 删除(delete); SQL(sql)

{$Timestamp} : 当前Bifrost同机器的时间戳

{$BinlogTimestamp} : Binlog 发生的时间戳

{$BinlogFileNum} : Binlog文件编号,并不是 整个Binlog文件名,比如 binlog 文件是 mysql-bin.000001 那这个 BinlogFileNum 的值 是1

{$BinlogPosition} : Binlog position 位点

{$字段名} : 表的字段数据

{$字段名['key1'][0]['key3']} :

假如 JSON 字段内容值为 ：{"testK": [{"Bool": false, "Null": null, "Time": "2020-05-31 23:21:30", "Int32": -2147483648, "Int64": -9223372036854775808, "String": "ss", "Uint32": 4294967295, "Uint64": 18446744073709551615, "Float64": 526637.46}]}

{$字段名['testK'][0]['Time']} 的对应的值 为：2020-05-31 23:21:30

 

备注： 标签支持程度，取决于各个插件的实现,请参考各插件的文档

