---
title: "HTTPS证书"
lastmod: 2019-12-03T22:51:15Z
draft: false
weight: 13
---


#### 脚本生成
```
cd etc/
./crt_create.sh
```

#### 自行生成
```
openssl genrsa -out server.key 2048
openssl req -new -x509 -sha256 -key server.key -out server.crt -days 3650
```
[证书生成参考](https://gist.github.com/denji/12b3a568f092ab951456)

#### 免费证书

[Let’s Encrypt](https://letsencrypt.org/)