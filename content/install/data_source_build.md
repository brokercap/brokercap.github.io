---
title: "编译安装"
lastmod: 2020-08-17T15:30:15Z
draft: false
weight: 11
---


#### 安装好 go　语言环境，go 1.12+,如果这个搞不定的，请查看下面的 二进制包安装

下载源码包
```
git clone git clone https://github.com/brokercap/Bifrost.git
```

#### 编译方式1
```
make install prefix=./target
```

#### 编译方式2
```
./build.sh linux

./build.sh windows

./build.sh freebsd

./build.sh darwin
```

编译完将会在 tags 目录下成的相应平台名称的文件夹,

以linux 为例

```
./build.sh linux

cd ./tags/$version/linux

./Bifrost-server start
```

浏览器打开 管理界面  https://127.0.0.1:21036

默认用户名 Bifrost  : Bifrost123