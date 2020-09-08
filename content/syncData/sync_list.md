---
title: "同步列表字段解析"
lastmod: 2019-11-27T21:05:15Z
draft: false
weight: 54
---

![image](/images/syncData/tableSync_list_1.jpg)

#### 字段解析

1. sliceId/ID   : 第几个同步 / 自增ID

2. PluginType : 插件名称

3. ToServerKey : 绑定的目标库唯一名字,添加目标库时候的 ToServerKey

4. FieldList : 选择了哪些字段要同步

5. Others : Bifrost 内部参数

1). MustBeSuccess : 每一条数据是否必须同步成功,不成功就一直在重试 (v1.4.0 版本后请参考 plugin_commit_timeout,plugin_retry_sync_time 配置)

2). FilterQuery : 是否过滤 sql 语句事件

3). FilterUpdate : update事件中,选择的字段的数据都没有被更新,是否过滤

4). BinlogFileNum : 最后一次同步成功的binlog文件编号

5). BinlogPos : 最后一次同步成功的binlog文件位点

6). LastBinlogFileNum : 最后一条进入到队列中的数据的binlog文件编号

7). LastBinlogPosition : 最后一条进入到队列中的数据的binlog文件位点

8). QueueMsgCount : 当前队列中待处理的消息条数


6. PluginParam : 当前目标库同步 配置的参数

7. Error : 当前目标库同步出错的错误内容,比如连接失败,数据转换失败等

当出现了 Error 的时候, Error 后面会跟着 一个 Miss 按钮

因为有一种可能,一条数据因为数据原因,一直同步到止目标库失败,比如数据转换失败等

需要错过这条数据,不能影响业务

所以需要点击 这个 Miss 按钮,进行错过这一条数据

对于 Miss 按钮 的支持程度,取决于 插件的实现,请查询插件相关资料