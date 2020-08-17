---
title: "Binlog解析"
lastmod: 2020-08-17T19:05:00Z
draft: false
weight: 23
---

#### 存储类型数据转换

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

JSON ( map | array )


#### Binlog 对 DDL 支持说明

正常Bifrost同步过程中 DDL 是没有问题的

但是如果配置的 *位点* 是在DDL 之前的位点,会出现数据和字段对应不上,甚至数据解析出解析出错,造成不可解析的问题.

这种情况,会有日志显示