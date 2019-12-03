---
title: "性能测试"
lastmod: 2019-12-03T23:00:15Z
draft: false
weight: 110
---

*Bifrost* 提供了一些用于性能测试的方法

### Bristol性能测试

[Bristol性能测试工具文档](https://github.com/brokercap/Bifrost/tree/master/Bristol/test/BristolPerformanceTest)

### Bifrost性能测试

#### 1.首先先安装启动 Bifrost [安装部署文档](http://wiki.xbifrost.com/install/data_source_build/)

在管理界面添加数据源及目标库等配置信息,配置好哪一个表要同步到哪些目标库



#### 2. 修改 Bifrost.ini 配置文件,新增 [PerformanceTesting]参数

```
[PerformanceTesting]
myTest=mysql-bin.000015,107175
```
myTest : 是界面添加数据源时候的 Name

mysql-bin.000015,107175 : 当前数据源的最新位点信息


#### 3. 关闭 Bifrost

这里一定要关闭Bifrost


#### 4. 刷数据到数据库中

这里采用 Biforst 自带的 [刷数据的工具](https://github.com/brokercap/Bifrost/blob/master/test/insertDataTest.go)  这个工具会自动根据表里的字段类型创建指条条数的数据

`````go
go run insertDataTest.go -host 127.0.0.1 -user root -pwd root -port 3306 -schema bifrost_test -table test1 -count 100000

`````

也可自行想办法刷数据


#### 5. 重启Bifrost,会看到如下数据:

```
2019/04/19 20:33:37 myTest SchemaName: bifrost_test TableName: bristol_performance_test blackhole blackhole ToServer consume_to_server  start
2019/04/19 20:33:37 myTest SchemaName: bifrost_test TableName: bristol_performance_test blackhole blackhole ToServer consume_to_server  start
2019/04/19 20:33:49 consume_to_server: blackhole blackhole 3  start no data
2019/04/19 20:33:49 consume_to_server: blackhole blackhole 1  start no data
```

start no data 的时间 减去 consume_to_server  start 的时候再减去 5秒 就是真正同步数据的时间