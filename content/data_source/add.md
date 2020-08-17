---
title: "数据源新增"
lastmod: 2020-08-T10:05:00Z
draft: false
weight: 21
---



![image](/images/data_source/data_source_add.jpg)

在线添加数据源,不需要重启服务


##### 字段解析

Name : 数据源唯一名字,不能和其他数据源名字重复,30个数字或者字母以内

ConnectUri : 连接数据源的地址

BinlogFileName : 从哪一个位点文件开始解析 , 如果为空的话,在点击 Check 按钮的时候,会自动填充当前 MySQL 最新的们点信息

BinlogPosition : 从哪一个位点开始解析 , 如果为空的话,在点击 Check 按钮的时候,会自动填充当前 MySQL 最新的们点信息
    

ServerId : 这个是 MySQL slave 里配置的  server_id 同一个概念,在添加数据源的时候,不能和 MySQL 主库及其其他从库 的 server_id 相同, 建议在配置 MySQL 主从的时候,用 IP 的后三位来当作  mysql 的 server_id .如果为空的话,在点击 Check 按钮的时候,会自动随机一个 数字出来


MaxBinlogFileName ,MaxBinlogPosition : 解析到哪一个位点的时候,就close掉,不再进行binlog解析


##### 所需权限

GRANT SELECT, SHOW DATABASES, SUPER, REPLICATION SLAVE, EVENT ON *.* TO 'xxtest'@'%'

RDS云产品数据库云产品权限,可能不是按 MySQL 开源权限来的,请自行确认是否有足够权限

*会执行的SQL：*

SELECT `COLUMN_NAME`,`COLUMN_DEFAULT`,`IS_NULLABLE`,`COLUMN_TYPE`,`COLUMN_KEY`,`EXTRA`,`COLUMN_COMMENT`,`DATA_TYPE`,`NUMERIC_PRECISION`,`NUMERIC_SCALE` FROM `information_schema`.`columns` WHERE TABLE_SCHEMA = ? AND TABLE_NAME = ?

SELECT COLUMN_NAME,COLUMN_KEY,COLUMN_TYPE,CHARACTER_SET_NAME,COLLATION_NAME,NUMERIC_SCALE,EXTRA,COLUMN_DEFAULT,DATA_TYPE,CHARACTER_OCTET_LENGTH FROM information_schema.columns WHERE table_schema= ? AND table_name= ? ORDER BY `ORDINAL_POSITION` ASC

SELECT `SCHEMA_NAME` FROM `information_schema`.`SCHEMATA`

SELECT TABLE_NAME,TABLE_TYPE FROM `information_schema`.`TABLES` WHERE TABLE_SCHEMA = ?

SHOW GLOBAL VARIABLES LIKE 'BINLOG_CHECKSUM'

SHOW MASTER STATUS

SHOW VARIABLES LIKE 'server_id'

SHOW VARIABLES LIKE 'binlog_format'

SELECT connection_id()

SELECT TIME,STATE FROM `information_schema`.`PROCESSLIST` WHERE ID = ?'

KILL 当前帐号binlog监控的异常连接


*根据以上SQL,如果是RDS等云产品,请自行确认是否有 监听Binlog的权限，KILL 当前帐号连接的权限,SET 权限*


##### 其他

新增了数据源之后 状态默认为 close 状态,在配置了数据表同步之后再去点击 start 按钮开始

默认情况下会自动为这个数据源添加一个名为 default 的通道. default 通道默认为开始状态. 通道信息,请查阅 通道相关文档

