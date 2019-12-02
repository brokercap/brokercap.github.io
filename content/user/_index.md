---
title: "用户管理"
publishdate: "2018-11-28T21:49:15Z"
draft: false
weight: 13
---


#### 用户组


administrator : 超级管理员组,是这个组的用户有超级管理人员权限

monitor : 普通用户组,只能查看数据的权限,连自己的密码也不能修改, 修改密码也只能由 administrator 用户组帐号来修改


#### 添加用户

![image](/images/user/user_add.jpg)

#### 修改用户

![image](/images/user/user_edit.jpg)

#### 备注

在第一次启动的时候,会加载 Bifrost.ini 配置文件中 的 [user] 和 [group] 配置用的户名,密码,和用户组 保存到 对应的存储中去

假如界面上在线修改了用户名和密码等操作,并不会同步到 Bifrost.ini 配置中去

与此同时在第一次启动之后,修改 Bifrost.ini 配置文件中的用户名和密码等无效,只能在线修改
