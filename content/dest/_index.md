---
title: "目标库管理"
publishdate: 2019-11-23T15:32:15Z
draft: false
weight: 4
---

目标库是建立在插件支持上的

比如 同步到 Redis 这是一个插件

但是一个数据源或者多个数据源的数据,需要同步到多个 Redis Cluster

利用哪个插件数据同步到哪个地址, 就是我们当前讲的目标库

一个插件下可以绑定多个目标库

添加了目标库之后,才能设置数据源里的某一个表的数据同步到哪一个目标库里去

![image](/images/dest/dest_list.jpg)


#### 字段解析:

ToServerKey : 全局唯一值,在添加目标库的时候必须唯一,在数据表设置同步的时候,需要用到这个

PluginName : 当前目标库绑定的是哪一个插件

PluginVersion : 插件版本

ConnUri : 插件连接的 URI

LastID : 自增ID,表示插件连接的ID

MaxConn : 当前目标库连接池大小

CurrentConn : 当前目标库已经创建了多少个连接

AvailableConn : 当前目标库已经创建了,但并没有使用的连接数量

Notes : 用于用户记忆的备注


#### 其他

查看绑定表  按钮,可以连接到 查看 有哪些表绑定了当前目标库同步

