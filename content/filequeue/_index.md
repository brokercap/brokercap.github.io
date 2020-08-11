---
title: "文件队列"
publishdate: 2020-08-11T08:05:00Z
draft: false
weight: 6
---

#### *Bifrsot* Diagram

![image](/images/diagram.png)


Bifrsot 架构中采用了 通道队列缓存 , 同步队列缓存 两层队列缓存来实现 一个 Binlog 解析, 各个同步配置同时进行同步

但是由于只有一个 Binlog , 多个地方都用到数据,那就存在一个可能,某一个同步出问题了, 然后就造成了,Binlog 解析阻塞,间接着影响了其他同步延时

在 *Bifrost v1.2.1* 版本开始 , Bifrost 新增了一个文件队列功能,在同步出现阻塞的时候,自动启动文件队列,并不影响 Binlog 解析


#### 文件队列配置

```
#是否开启文件队列功能 true|false
file_queue_usable=true

#统计是否启文件队列的时间，单位毫秒
file_queue_usable_count_time_diff=5000

#file_queue_usable_count_time_diff 时间内内存队列被挤满的次数
file_queue_usable_count=10

```

可以通过 file_queue_usable 控制,是否自动文件队列,如果这个 配置 为 false 的时候,不管情况 下都不会启用文件队列的

默认自动启用条件

5000ms 的时间里,每次往队列写入数据,连续10次,内存队列都是满了的情况


文件队列,是按表的同步配置为单位启动的,也就是说,假如一个表配置了 10 个同步, 其中第一个配置同步是同步到 Redis, 在这个同步到 Redis 的同步中同步慢了,出现了内存队列阻塞的情况下,则启用一个文件队列,用于缓冲 同步到当前这个表同步到 Redis 


