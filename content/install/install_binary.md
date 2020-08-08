---
title: "二进制安装"
lastmod: 2020-08-008T19:12:15Z
draft: false
weight: 12
---



二进制包安装，不用安装 go 语言环境，下载二进制包可修改配置可直接运行

下载二进制包

以v1.3.2-release linux版本为例

```
wget https://github.com/jc3wish/Bifrost/releases/download/v1.3.2-release/linux.zip

unzip linux.zip 

mv ./linux ./Bifrost_v1.3.2-release

cd ./Bifrost_v1.3.2-release

chmod a+x ./Bifrost ./Bifrost-server

./Bifrost-server start
```


windows 下启动

```
Bifrost.exe -config ./etc/Bifrost.ini

```

浏览器打开 管理界面  https://127.0.0.1:21036
