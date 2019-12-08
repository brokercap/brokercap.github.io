---
title: "Bifrost 运维"
lastmod: 2019-12-03T22:22:15Z
draft: false
weight: 200
---


### Bifrost 常见问题

##### 1. 全量同步没有问题，但insert,update等增量没过去

1). 检查数据源是不是 running 状态,重新启动一下数据源

2). 检查数据表绑定的通道是不是 running 状态，尝试重启通道

3). 数据源的 Binlog 的 format 是 row 格式不？

在 MySQL 中执行 SHOW VARIABLES LIKE 'binlog_format'

- - -

##### 2. 性能测试 Bifrost 同步到 Redis 等插件的时候 OOM 了

1). 查看一下数据源修改是不是一条sql 修改 成千上万条数据。

假如一条sql写入或者修改成千上万的数据，在 Binlog 中是一个事件，Bifrost 的队列缓存是按事件来存储的。

可以尝试把配置文件中的 toserver_queue_size 配置改小，默认为5000

```
toserver_queue_size=10000
```

2). 升级硬件配置

如果修改配置，还不行，建议尝试升级一下硬件配置

3). 检查同步到目标库性能否扛得住

- - -

##### 3. Bifrost 管理界面显示内存比较大

Bifrost 显示的是最大使用过多少内存,并不是当前 Bifrost 进程占了多少内存,这个内存显示只能参考

Bifrost 实现内存使用情况,请使用top命令查看

```
top -p Bifrost进程PID

```

top 命令中 RES 列显示的内在则为 实际使用内存大小