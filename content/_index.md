---
title: "前言"
publishdate: 2019-11-23T15:32:15Z
draft: false
weight: 1
---

## Bifrost ---- 将MySQL数据实时同步 到 Redis , MongoDB等的高性能异构中间件


*Bifrost* 是一个可以通过界面配置,将 MySQL 数据库中的数据异构的方式同步到Redis,Mongodb,RabbitMQ等甚至自定义RPC服务里的 *简单 高效* 的 异构中间件 .

##### [为什么选择 Bifrost ?](/other/why/)

##### *实时同步数据只要在界面上用 鼠标 点 点 点 .....*


提供 [Linux](https://github.com/brokercap/Bifrost/releases) , [mac](https://github.com/brokercap/Bifrost/releases) , [windows](https://github.com/brokercap/Bifrost/releases) , [freebsd](https://github.com/brokercap/Bifrost/releases)  平台二进制可执行文件下载.


*Bifrsot* 支持同步到多种目标库, for example:

* Redis
* Memcache
* RabbitMQ
* ActiveMQ
* Kafka
* Mongodb
* ClickHouse
* MySQL
* [Http 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/plugin/http/example/http_server/http_server.go)
* [Hprose RPC 自定义服务](https://github.com/brokercap/Bifrost/blob/v1.1.x/hprose_server/tcp_server.go)


#### 在线Bifrost界面体验

http://www.xbifrost.com/demo/

![image](/images/bifrost.png)


#### *Bifrsot* Diagram

![image](/images/diagram.png)


### Bifrost 特点

* 支持 MySQL 所有存储类型

* 界面化动态灵活配置不同的数据表及目标库

* 多数据源,多种目标库支持

* 同时支持 *增量* 及 *全量* 数据同步

* 宕机后重启自动恢复配置

* 支持自定义插件开发，动态加载

* 支持 Email 及 微信企业号等报警功能

* 提供第三方监控平台访问接口(Http Basic验证)

* 支持对 Null 的解析，但对 Null 的支持程度，取决于插件的支持，如果有 Null 数据，请查阅 相关插件文档


### Bifrost 还不足的地方

* 支持批量刷数据的插件，在数据同步异常的情况下，不支持错过当前数据同步功能，例如： ClickHouse,Kafka

* datetime,timestamp 解析还不支持精确到毫秒,毫秒部分将会自动被丢弃


### WIKI

[Bifrost WIKI](https://github.com/brokercap/Bifrost/wiki)

### MySQL 版本（经过测试）

* 5.1.73

* 5.5.42

* 5.6.36

* 5.7.18

* 8.0.11 (用户密码验证需要采用 mysql_native_password 方式)


### 安装

##### 编译

```sh

git clone https://github.com/brokercap/Bifrost.git

make install prefix=./target
```

##### 二进制文件安装
`````sh

wget https://github.com/brokercap/Bifrost/releases/download/v1.1.x-release/linux.zip

unzip linux.zip

cd linux

`````

##### 启动

`````shell
./Bifrost-server start

`````

##### 停止

`````shell
./Bifrost-server stop

`````


##### 界面管理

https://127.0.0.1:21036

用户名和密码是在 etc/Bifrost.ini 配置文件中配置的

默认用户名和密码

用户名：Bifrost

密码：Bifrost123


##### Docker启动

`````shell
docker pull jc3wish/bifrost

mkdir -p /data/BifrostData

docker run -d -p21036:21036 -v /data/BifrostData:/linux/data jc3wish/bifrost

`````


##### 配置
`````sh

vim Bifrost.ini

[user]
#登入用户名和密码
#用户名在前，= 号后面是密码
Bifrost=Bifrost123
BifrostMonitor=Bifrost123

[groups]
#administrator 管理人员可以添加删除等操作
#monitor 只能查看数据
Bifrost=administrator
BifrostMonitor=monitor

[Bifrostd]
#log写到入的文件夹，默认为启动文件所在文件夹下的logs目录
#log_dir=/data/log/

#监听端口,默认 0.0.0.0:21036
listen=0.0.0.0:21036

#通过界面配置的信息保存文件夹,默认为启动文件所在文件夹下的data目录
data_dir=./

#设置同步给第三方服务的队列大小，默认5000
toserver_queue_size=5000

#通道队列的大小，默认1000
channel_queue_size=1000

#是否支持动态加plugin so 插件，默认为false
#这个参数只在 linux 下有效，其他平台全部为false
dynamic_plugin=false

#是否支持https
tls=true

#server.key 及 server.crt 相对于 Bifrost 的路径，也可以填绝对路径
tls_key_file=./etc/server.key
tls_crt_file=./etc/server.crt

#采用什么方式存储,默认文件方式
meta_storage_type=redis

#存储介质的连接地址
meta_storage_path=127.0.0.1:6379

#用于区别实例的名字
cluster_name=bifrostTestClusterName

`````

##### 生成https证书

[参考证书生成](https://github.com/brokercap/Bifrost/wiki/HTTPS%E8%AF%81%E4%B9%A6)



### 界面操作文档

[Bifrost官网](http://www.xbifrost.com/)

### 感谢相关依懒包提供者

- github.com/gmallard/stompngo
- github.com/hprose/hprose-golang
- github.com/Shopify/sarama
- github.com/bradfitz/gomemcache/memcache
- gopkg.in/mgo.v2
- github.com/streadway/amqp
- github.com/go-redis/redis
- github.com/syndtr/goleveldb/leveldb
- github.com/kshvakov/clickhouse

### QQ群号： 70331601

<img src="/images/qq.jpg" width="250" alt="qq群"/>


