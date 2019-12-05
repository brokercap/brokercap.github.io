---
title: "使用手册"
lastmod: 2019-12-05T22:37:15Z
draft: false
weight: 100
---


## 导航栏

导航栏里有 首页 ，数据源 , 目标库列表 , 表同步列表 ,全量任务, 流量 ,插件,报警, 文档

每个导航菜单采用不同的功能。


## 插件

*插件* 点击进去后，显示的是当前版本 Bifrost 支持 插件信息。在 ToServer 模块里添加ToServer 的时候，需要进行选择插件信息。Linux 平台，是可以自行开发插件，并且动态加载的。请参考 [插件开发教程](https://github.com/jc3wish/Bifrost/wiki/%E6%8F%92%E4%BB%B6%E5%BC%80%E5%8F%91%E6%95%99%E7%A8%8B)

## 目标库列表

*目标库列表* 就是数据需要同步到哪些地方去，就是目标库的配置。

比如 要同步Redis的哪一个实例，要同步到RabbitMQ 哪一个vhost上去等连接信息。就在 ToServer 模块下配置。

ToServerKey 是用来标识整个服务中 ToServer 的唯一值，不能重复。 ToServerKey 用于在绑定 MySQL 数据库指定表同步到哪一个目标库的关系。

在添加 ToServer 的时候，需要点击一下 Check 按扭。确认一下当前配置的 uri 连接地址是否正常。**假如 当前选择的插件连接 填写的 uri 失败是不能添加成功的**。

## 文档

*Bifrost* 当前版本通用文档及各插件的使用文档

##### 通用

**数据类型转换**

从Binlog解析出来的数据,在转成 map[string]interface{} 的数据格式的时候

MySQL里的存储类型对应Golang里的数据类型不一样

括号里的代表是Golang里的数据类型

TINYINT ( int8 | unit8 ) , SMALLINT ( int16 | unit16 ) , MEDIUMINT ( int32 | uint32 ), INT ( int32 | uint32 ) , BIGINT ( int64 | uint64 )

FLOAT ( float32 ) , DOUBLE ( float64 ) ,REAL ( float64 )

DECIMAL , NUMERIC( 返回string类型 )

DATE , TIME , YEAR , DATETIME , TIMESTAMP ( string类型 )

CHAR , VARCHAR ( string )

TEXT , TINYTEXT , MEDIUMINTTEXT , LONGTEXT ( string )

BLOB, TINYBLOB , MEDIUMINTBLOB , LONGBLOB ( string )

ENUM ( string类型 )

SET ( []string )

BIT ( int64类型 )

**DDL 支持说明**

当前只支持字段在表结构末尾追加新字段，如果配置的二进制位点是在DDL 之前的位点，会出现数据和字段对应不上

数据类型修改之后，如果配置的二进制位点是在DDL 之前的位点，可能会出现数据出错


**标签**
{$SchemaName} : 数据库名字

{$TableName} : 表的名字

{$EventType} : 当前记录的增删改事件,新增(insert); 修改(update) ; 删除(delete); SQL(sql)

{$字段名} : 表的字段数据

##### 插件配置
选择不同的插件，显示不同的插件文档信息。不同的插件使用文档不一致，在设置表的同步信息的时候，所要填写的参数信息也不一样。

## 流量

流量显示。支持增量及全量的方式进行显示。支持按 最近1分钟，10分钟，1小时，8小时，24小时 进行查询显示。

增量：规定时间内的处理了数据量

全量：Bifrost 中间件启动之后的处理所有数据量

流量分 处理了多少条数据(Count) , 处理了总的消息大小(ByteSize). 可以进行点击过滤哪一个流量。默认为 Count 和 ByteSize 都同时显示

从菜单栏点击 进入 Flow ，则显示的是整个 Bifrost 服务的流量信息

从 DBList 列表里点击进去的流量信息则是当前 设置的数据源的所有流量信息

从 Channel 列表里点击进去的流量信息则是当前 Channel 通道的所有流量信息

从 DB Detail 详情页，选择了某珍上数据库的数据表之后 进击详情页里的 Flow 按钮之后则显示的是当前表的流量信息

Flow 的流量信息当前不支持持久化。每次 Bifrost 重启之后，统计归零

## 数据源

数据源 是 MySQL 数据源信息。可以添加多个 MySQL 源信息。

每个 MySQL 数据源信息，下面可以继续设置 通道 Channel ,及在详情里可以设置某一个表的数据，要以什么格式通过某一个插件下的ToServer 进行同步数据。一个表的数据可以设置同步到多个地方去。

数据源列表提供展示 当前数据源 同步数据的位点信息，连接状态 及可以点击查看当前数据源连接的 MySQL 库的最新位点进行查看

### 数据源添加

Name ：全局 DBList 中一值。不能重复
ConnUri ： Bifrost 连接 MySQL 的信息uri ，例如：root:root@tcp(127.0.0.1:3306)/test。 连接状态必须要有主从连接的权限

BinlogFileName,BinlogPosition ：从哪一个位点开始监控数据变更。这个值在点击 Check 按钮后，如果测试 ConnUri 能连接成功并且连接的 MySQL 开启了 Binlog 的情况下会自填弃当前 MySQL 主库的当前的位点信息

ServerId ：这个配置不能和主库的 ServerID 一致。点击Check 按钮后会自动随机填充一个ID值

MaxBinlogFileName,MaxBinlogPosition : 解析二进制日志文件到了 Max 配置的位点信息之后就不再解析，自动停止。默认为空。不做限制。


DB默认添加之后是 Close 状态，可以先去详情页设置表的同步信息之后再点击 Start 按钮再开始。


### ChannelList通道
每加一个DB连接之后，会默会 名字为 default 的通道。默认状态为 stop。需要手工点击 Start 按钮进行开启。

Channel通道就是一个队列。Channel通道是用于在设置 数据表信息同步的时候，选择表的数据采用哪一个 Channel通道进行缓存。假如 Bifrost.bifrost_field_test 这个表在设置了采用和 default 这个通道进行绑定，那 Bifrost 解析Binlog信息的时候，Bifrost.bifrost_field_test 这个表的数据信息将通过 进入到 default 这个通道队列。Channel通道的队列大小，可以通过配置文件中 channel_queue_size 进行配。请参数 [配置解析](https://github.com/jc3wish/Bifrost/wiki/%E9%85%8D%E7%BD%AE%E8%A7%A3%E6%9E%90)

##### Channel添加

channel_name ： 通道的名字，这个用于在设置数据表时候的会让进行选择哪一个通道

CosumerCount ： 当前这个通道队列的数据，由多少个消费者同时消费数据。默认为1。假如 CosumerCount > 0 的情况下，消息不能保证有序性。所以有某些对数据有序性没有要求。可以创建一个多少消息者的你通道，和那些没有有序性要求的表进行绑定。提升性能

##### Flow
当前通道的流量信息

##### TableList
进行查询有哪些表绑定了当前通道。通道在 DEL 删除的时候，必须要先删除绑定的所有表信息才能进行删除

##### Channel状态
stop ： 暂停状态，新添加的DB之后默认的default 状态为stop
close : 通道关闭状态。代表这个通道没有任何消息在处理消息
running ：正常处理数据状态

通道只在 close 状态并且没有数据表绑定了当前Channel 的时候才可以点击删除


### 设置数据表同步

数据表的同步信息在 DBList 里的 Detail 模块里设置设置

DB - Detail 详情页里面

Schema List ： 数据库列表

Table List ： 选择的数据库之后的表列表

表列表里的表右边假如出现了 ADD 按钮则代表 这个表已经被过滤掉了，不会进行任何同步数据处理。

1. 点击 ADD 按钮 之后会弹一个窗出来，让选择绑定哪一个Channel通道，这个请参数 Channel 文档

2. 点击弹窗的 保存 按钮后，ADD 按钮会变成 DEL 按钮。说明这个表已经注册到了需要 Bifrost，需要进行同步

3. 然后再点 表名，右边的 Table ToServer List 模块，会显示出当前表设置了哪些同步信息


##### Table ToServer 添加

一个数据表，可以设置多个目标库的同步，设置不同的插件同步到的不同的目标库

ToServerKey,MustBeSuccess,FilterQuery,FilterUpdate,Fields 几个参数为 *Bifrost* 固定选择参数。其他参数则是 各个插件特有参数信息

ToServerKey ： 导航菜单栏 ToServer 里设置的目标库选择。不同的表可以选择同一个ToServerKey

MustBeSuccess : 表示必须成功，假如插件返回失败的时候情况下，会一直重试，直到成功为止。

FilterQuery : 是否过滤 DDL 等 SQL 语句。如果选择的同步插件不支持 sql 语句或者你的业务逻辑中只用的行模式里的数据的，不要更改，默认为过滤

FilterUpdate ：假如开启了这个功能，假如是更新事件的情况下，并且选中的表字段里的内容都没有变更，则不会进行推送。

Fields : 在写入，更新，删除 事件的情况下，要推送哪些字段。默认不选 的情况下为全选

## 表同步列表

*表同步列表* 是查看到所有设置了同步信息的列的信息列表

列表里可以查看 表的同步位点，及 同步状态和报错内容

在 *表同步列表* 可以进行 数据源，通道，及ToServerKey 进行过滤筛选

可以从 数据源列表　，通道列表　及　目标库列表  进行点击进入到 表同步列表

## 全量任务

可以在设置表设置同步界面,进行添加 表全量数据同步 配置

一个表可以添加多个全量同步配置,每个全量同步配置,可以指定同步到哪些目标库

全量任务 不会进行持久化,在重启之后会自动被清除

全量任务在运行过程中,可以点击 stop 按钮进行停目,当再次点击 start 按钮的时候 ,将完全重复跑任务,而不是断点续跑

## 报警

可以设置 Email 及 微信企业号的报警配置信息

在 *数据源* 连接失败 或者 数据表数据同步到第三方服务 连续报错的情况下会进行 通过 报警配置进行报警。

并且报警过后 数据源连接恢复 ，或者同步数据成功之后，会再次通过报警配置发出一条 恢复成功 的信息

#### Email

需要配置 smtp 服务器

假如是 QQ邮箱，密码不是QQ密码，需要去QQ邮箱界面 [申请授权码](https://jingyan.baidu.com/article/6079ad0eb14aaa28fe86db5a.html)

#### 微信企业号

个人也是可以 [申请微信企业号](https://work.weixin.qq.com/)
