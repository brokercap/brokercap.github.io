---
title: "Bifrost 集群设想"
lastmod: 2019-12-03T22:20:15Z
draft: false
weight: 103
---


Bifrost 当前并不支持高可用，但作者本人，一直在想，Bifrost 高可用要怎么实现。

#### Bifrost 集群要求

1. 不强依懒第三方系统

2. 只有一个 Bifrost 应用，不需要部署多个应用

3. 一个界面可以管理整个集群的配置

4. 同一个同步配置，性能不能相对单机降低

5. 高可用切换的时候，数据源位点 要尽可能接近宕机那一个节点的位点


#### Bifrost 集群设想

1. Bifrost 自身集成 ETCD

2. Bifrost 将配置及位点写到本进程中集成的 ETCD 节点上

3. Bifrost 配置分配按数据源来分配，一个数据源的配置同步，只会运行在一个节点上


#### Bifrost 集群设想要解决的问题

1. 作者对 ETCD 集成并不熟悉

2. 配置写到 ETCD ，管理界面获取数据的时候，数据源可能在多个节点上运行，位点，流量等等数据，得重新计算获取

3. 不同版本不能支持集群？因为不同版本插件等都不同，那通过什么办法热升级？


#### 小结

Bifrost 集群，包括主要是界面管理上那块数据展上，还有些地方需要考滤，不能为了做而做

如果谁有兴趣，可以联系我们

### QQ群号： 70331601
<img src="https://github.com/brokercap/Bifrost/blob/master/qq.jpg" width="250" alt="qq群"/>