---
title: "文件存储"
publishdate: 2020-08-11T08:05:00Z
draft: false
weight: 62
---

文件队列是按每个同步配置为单位管理的

每个同步配置对应一个文件夹进行文件存储 

$DataDir/filequeue/$dbName/$SchemaName/$TableName/$ToServerID

$DataDir : Bifrost.ini 配置中的 data_dir 路径

$dbName  : 数据源名称

$SchemaName : 数据库名称

$TableName : 表名

$ToServerID : 同步ID

在文件队列存储目录下,还会自动生成一个tmp目录 (这里为什么要加 tmp 结尾来创建，是因为 在实际 测试中centos7  go1.14.2 版本中数字结尾的目录，最后一层个位数字名字目录，可能是不会被创建的, 这里tmp目录 ,并没有其他存储意义)


#### 存储文件

16MB 一个文件进行存储,文件名从 0 开始自增生成的文件

```
0.list

1.list

2.list

```








