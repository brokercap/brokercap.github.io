---
title: "通道"
publishdate: "2018-11-23T15:32:15Z"
draft: false
weight: 10
---

每个数据源下面都可以配置多个通道

每个数据源添加的时候,默认会启动一个名为 default 的通道


#### 通道的作用:

1. 一个通道就是一个阻塞队列,队列大小,默认为 1000,可以通过 channel_queue_size 修改

2. 一个通道,可以绑定多个数据表

3. 绑定了表的数据,从 Binlog 解析出来之后,写到入 通道队列中

4. 一个通道 可以设置 N 个消费者,默认情况下为 1 个,如果要顺序,请设置为 1

5. 通道队列的消费者消费数据出来之后,判断数据表配置同步到哪些目标库里去,再将数据写到 目标库的队列中去


#### 通道管理

1. 进入通道管理界面

![image](/images/channel/channel_1.jpg)

2. 通道管理

![image](/images/channel/channel_2.jpg)

1). 通道在线启动关闭

Stop : 在线暂停通道队列数据消费,消费协程不退出, Status 会修改为 stop

Close : 完全关闭队列的消费者,退出消费者协程. Status 个性为 close

Start : 在 stop 或者 close 状态下, 点击 Start 按钮后 ,会进行恢复消费队列数据的能力,状态修改为 running


2). 在线通道添加

channel_name : 通道的名字

CosumerCount : 通道队列消费者的数量,如果数据需要顺序,请设置为 1

3). 其他字段解析

CurrentThreadNum : 当前有多少个消费者正在运行

Status : 当前通道状态, running 正在运行, stop 暂停 , close 关闭

