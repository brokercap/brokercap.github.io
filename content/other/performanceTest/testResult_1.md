---
title: "Mac 测试结果 1"
lastmod: 2019-12-08T12:36:15Z
draft: false
weight: 1101
---

#### 查看当前 MySQL 位点

连接上 MySQL 后 执行  

```
show master status\G;

```
```
*************************** 1. row ***************************
            File: mysql-bin.000020
        Position: 739
    Binlog_Do_DB: 
Binlog_Ignore_DB: 
1 row in set (0.00 sec)

```

#### 造数据

往 bifrost_test.bristol_performance_test 表里写 100000 条数据

如果 bifrost_test 库 和 bristol_performance_test 表不存在的情况下会自动创建 

```
/BristolPerformanceTest -count 100000 -u root  -P 3306 -conndb test -schema bifrost_test -table bristol_performance_test -onlydata true
```


结果:

```
2019/12/08 12:27:45 ExecSQL success: CREATE DATABASE /*!32312 IF NOT EXISTS*/ `bifrost_test`
2019/12/08 12:27:46 ExecSQL: CREATE TABLE bifrost_test.`bristol_performance_test` (`id` int(11) unsigned NOT NULL AUTO_INCREMENT,`testtinyint` tinyint(4) NOT NULL DEFAULT '-1',`testsmallint` smallint(6) NOT NULL DEFAULT '-2',`testmediumint` mediumint(8) NOT NULL DEFAULT '-3',`testint` int(11) NOT NULL DEFAULT '-4',`testbigint` bigint(20) NOT NULL DEFAULT '-5',`testvarchar` varchar(10) NOT NULL,`testchar` char(2) NOT NULL,`testenum` enum('en1','en2','en3') NOT NULL DEFAULT 'en1',`testset` set('set1','set2','set3') NOT NULL DEFAULT 'set1',`testtime` time NOT NULL DEFAULT '00:00:00',`testdate` date NOT NULL DEFAULT '0000-00-00',`testyear` year(4) NOT NULL DEFAULT '1989',`testtimestamp` timestamp NOT NULL DEFAULT CURRENT_TIMESTAMP,`testdatetime` datetime NOT NULL DEFAULT '0000-00-00 00:00:00',`testfloat` float(9,2) NOT NULL DEFAULT '0.00',`testdouble` double(9,2) NOT NULL DEFAULT '0.00',`testdecimal` decimal(9,2) NOT NULL DEFAULT '0.00',`testtext` text NOT NULL,`testblob` blob NOT NULL,`testbit` bit(8) NOT NULL DEFAULT b'0',`testbool` tinyint(1) NOT NULL DEFAULT '0',`testmediumblob` mediumblob NOT NULL,`testlongblob` longblob NOT NULL,`testtinyblob` tinyblob NOT NULL,`test_unsinged_tinyint` tinyint(4) unsigned NOT NULL DEFAULT '1',`test_unsinged_smallint` smallint(6) unsigned NOT NULL DEFAULT '2',`test_unsinged_mediumint` mediumint(8) unsigned NOT NULL DEFAULT '3',`test_unsinged_int` int(11) unsigned NOT NULL DEFAULT '4',`test_unsinged_bigint` bigint(20) unsigned NOT NULL DEFAULT '5',PRIMARY KEY (`id`)) ENGINE=MyISAM AUTO_INCREMENT=0 DEFAULT CHARSET=utf8  Err: Error 1050: Table 'bristol_performance_test' already exists
2019/12/08 12:27:46 insert data start
2019/12/08 12:28:08 insert data over
2019/12/08 12:28:08 use time 22 s
```


#### Britol 二进制性能解析测试

```
./BristolPerformanceTest -count 100000 -u root  -P 3306 -conndb test -schema bifrost_test -table bristol_performance_test -master_log_file mysql-bin.000020 -master_log_pos 739
```

-master_log_file mysql-bin.000020 

-master_log_pos 739

代表从哪个位点开始解析

```
2019/12/08 12:31:05 Schema: bifrost_test
2019/12/08 12:31:05 TableName: bristol_performance_test
2019/12/08 12:31:05 analysis binlog start
2019/12/08 12:31:05 start binlog info: mysql-bin.000020 739
2019/12/08 12:31:05 root:@tcp(127.0.0.1:3306)/test start DumpBinlog... filename: mysql-bin.000020  position: 739
2019/12/08 12:31:05 monitor reslut:starting 
2019/12/08 12:31:05 monitor reslut:running 
2019/12/08 12:31:07 analysis binlog over
2019/12/08 12:31:07 analysis success count: 100000
2019/12/08 12:31:07 end binlog info: mysql-bin.000020 44100865
2019/12/08 12:31:07 use time: 2 s
```


#### Bifrost性能测试

不同的插件性能不一样,所以我们这里只测经过了 二进制解析,并且将数据传送到 插件模块 的性能

所以我们用 blackhole 插件作为参考

##### 1).界面配置表数据同步

![image](/images/performanceTest/tableSync_config.jpg)


##### 2).修改位点


修改方法 [界面修改位点](/data_source/edit/)


但修改位点的时候, 同时更新ToSever 选项一定要选择为 True

![image](/images/performanceTest/dataSource_position_edit.jpg)

A. 修改完位点之后,点击 start 按钮

B. 查看日志

```
2019/12/08 13:09:45 myTest monitor: starting
2019/12/08 13:09:45 myTest monitor: running
2019/12/08 13:09:45 myTest SchemaName: bifrost_test TableName: bristol_performance_test blackhole blackholeTest ToServer consume_to_server  start
2019/12/08 13:09:54 consume_to_server: blackhole blackholeTest 1  start no data
2019/12/08 13:09:54 myTest SchemaName: bifrost_test TableName: bristol_performance_test blackhole blackholeTest ToServer consume_to_server over
```


这两个 over 和 start 之间的时间差,再减去 5 ,就是耗时时间,单位秒

比如这次测试 10万条数据 从 binlog 解析出来 并写同步到  blackhole 插件 总花费时间 4 秒


#### 小结

硬件 :  Mac Pro 2015 款

MySQL 版本 : 5.5.41-log

Bifrost 版本 : v1.1.0

数据条数 : 10万条

表字段数: 30个

Britol 解析时间 : 2 秒 左右

Bifrost,包括了二进制解析及数据处理之后写到插件的所有时间 : 4-5 秒左右











