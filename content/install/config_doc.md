---
title: "启动配置解析"
date: 2019-11-23T15:30:15Z
draft: false
weight: 13
---


**Bifrost** 默认配置路径在 Bifrost 运行目录下的 etc/config.ini

```
[user]
#管理界面的用户名及密码
Bifrost=Bifrost123

[groups]
#administrator 管理人员可以添加删除等操作
#monitor 只能查看数据
Bifrost=administrator
BifrostMonitor=monitor

[Bifrostd]
#默认会当前启动文件夹
#log_dir=/data/log/
#默认21036端口
listen=0.0.0.0:21036
#默认会当前创建$data_dir/data文件夹，权限为 700
data_dir=./
#默认启动文件夹下Bifrost.pid
#pid=/tmp/bifrost.pid

#设置同步给第三方服务的队列大小，默认5000
toserver_queue_size=5000

#通道队列的大小，默认1000
channel_queue_size=1000

#保存们点磁盘之前的缓存池桶池大小,<=0,则不开始 ,直接刷到leveldb,默认为50
key_cache_pool_size=50

#是否支持动态加plugin so 插件，默认为false
#这个参数只在 linux 下有效，其他平台全部为false
dynamic_plugin=false

#是否支持https
tls=true

#server.key 及 server.crt 相对于 Bifrost 的路径，也可以填绝对路径
tls_key_file=./etc/server.key
tls_crt_file=./etc/server.crt

#用于性能测试
#key是界面添加的DBName
#mysql-bin.000015,107175  位点信息
#假如配置了这个值,则启动的时候,会强制使用这个位点,包括所有配置的toseverlist. 
[PerformanceTesting]
myTest=mysql-bin.000015,107175
```


#### 备注

user,groups 只有在第一次启动初始化的有效,在线新增用户名和修改后,配置文件用户名和密码并不会进行修改