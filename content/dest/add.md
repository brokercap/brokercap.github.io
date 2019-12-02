---
title: "目标库新增"
lastmod: 2019-11-23T10:05:00Z
draft: false
weight: 41
---


在线添加数据源,不需要重启服务

一个插件可以绑定多个目标库

![image](/images/dest/dest_add.jpg)

#### 字段解析:

ToServerKey : 全局唯一值,在数据表设置同步的时候,需要用到这个. 30个数字或者字母以内

Plugin : 选择添加哪一个插件的目标库

ConnUri : 插件连接的 URI, 每个插件的连接方式不一样,当选择了 Plugin 之前,会有连接案例显示, 在点击 提交 之前,需要 点击 Check 按钮 验证连接是否正确

MaxConn : 当前目标库的连接池大小,默认 20

Notes : 用于用户记忆的备注