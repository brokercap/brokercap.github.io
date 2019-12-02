---
title: "数据源修改"
lastmod: 2019-11-23T10:05:00Z
draft: false
weight: 22
---



![image](/images/data_source/data_source_edit.jpg)


可以在线修改数据源信息,唯独不能修改数据源的Name

数据源只有在 close 状态下才能进行修改

close 状态下, 数据源列表的 Position 列 会出现一个 修改 按钮, 点击 之后可以对数据源进行位点,连接地址等进行修改

其他 状态下,修改 按钮不会显示

字段解析 参考 数据源新增 

修改数据源有一个特殊字段选项

*同时更新ToServer*

True : 会强制将所有 配置了 数据同步的表最后的位点更新到 此位点. 这个在更新了数据源连接地址,或者 数据源 Binlog 发生重大改变之后 选用

False : 只修改当前连接 MySQL 的位点 , 同步表记录的位点不进行修改.


