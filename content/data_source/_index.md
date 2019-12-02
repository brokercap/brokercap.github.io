---
title: "数据源管理"
publishdate: "2018-11-23T15:32:15Z"
draft: false
weight: 2
---


![image](/images/data_source/data_source_list.jpg)


可以动态添加多个 MySQL 数据源


##### 字段解析

Name : 数据源唯一名字,在添加数据源的时候指定

ConnectUri : 连接数据源的地址

ConnStatus : 当前数据源状态
    
    running : 正在运行,正常解析binlog

    starting : 正在连接 MySQL

    closing : 正在尝试关闭连接

    close : 关闭状态

    stop : 暂停解析状态


Position : 当前成功解析 MySQL Binlog 的位点信息
    
DB Postion : 可以点击  最新位点 按钮  进行获取当前 MySQL 最新的位点,并进行与当前成功解析的位点 计算,延时了多少秒

MaxPosition : 在添加数据源的时候指定的 MaxBinlogFileName 和 MaxBinlogPosition 配置

ServerId : 这个是 MySQL slave 里配置的  server_id 同一个概念,在添加数据源的时候,不能和 MySQL 主库及其其他从库 的 server_id 相同, 建议在配置 MySQL 主从的时候,用 IP 的后三位来当作  mysql 的 server_id 

ConnErr : 当前 数据源连接 或者 解析遇到的错误信息

ChannelCount : 当前数据源有多少个 通道

TableCount : 当前数据源配置了多少数据表同步

ReplicateDoDb : 当前数据源配置了哪些数据库要同步


##### 常见问题

*1. 一直是 starting 状态,有以下可能*

1). 刷新整个界面,看是否会有状态变化

2). 在尝试启动数据源之前,发现位点有问题,会自动的将当前位点文件最开始的位置进行遍历寻找离当前最近的一个可用的位点,如果这个关闭请 点击close 或者 去MySQL kill 连接

3). 网络异常,一直在重试


*2. 卡在 closing 状态*

1). 刷新整个界面,看是否会有状态变化

2). 假如上一个状态是 staring 再点击关闭的,那可能是因为位点不对,后台在寻找正确的位点,比较慢,如果这个时候需要关闭,请 去MySQL 端找到相关连接 进行 Kill 操作

3). 判断 MySQL 连接是否假死等情况,如果要退出,请去MySQL Kill 连接


*3. 位点显示延时*

在点击 最新位点 按钮的时候 并且数据源是running状态

假如会计算当前位点 和 最新位点 不一致的情况下,将会拿最后解析成功的位点时间 与 当前时间点进行对比,算出差值,单位(秒)

这里并不是拿最新位点的时间对比,而是当前服务器时间