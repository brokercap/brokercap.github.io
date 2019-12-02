---
title: "数据源新增"
lastmod: 2019-11-23T10:05:00Z
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



##### 其他

新增了数据源之后 状态默认为 close 状态,在配置了数据表同步之后再去点击 start 按钮开始

默认情况下会自动为这个数据源添加一个名为 default 的通道. default 通道默认为开始状态. 通道信息,请查阅 通道相关文档

