---
title: "二进制文件解析"
publishdate: "2018-11-28T21:49:15Z"
draft: false
weight: 99
---

Bristol 是一个解析 MySQL Binlog 模块

Github : https://github.com/brokercap/Bristol


#### 特点:

经过了数据校验测试: https://github.com/brokercap/Bristol/tree/master/test

支持了 5.1 - 8.0

提供解析 Binlog 性能测试工具


https://github.com/brokercap/Bristol/tree/master/test/BristolPerformanceTest


##### 使用案例

```
    filename := "mysql-bin.000071"

    var position uint32 = 203785789
    var DBsource = "root:root123@tcp(127.0.0.1:3306)/test"

    reslut := make(chan error, 1)
    m := make(map[string]uint8, 0)
    m["bifrost_test"] = 1
    m["mysql"] = 1
    BinlogDump := &mysql.BinlogDump{
        DataSource:    DBsource,
        CallbackFun:   callback,
        ReplicateDoDb: m,
        OnlyEvent:     []mysql.EventType{mysql.QUERY_EVENT, mysql.WRITE_ROWS_EVENTv1, mysql.UPDATE_ROWS_EVENTv1, mysql.DELETE_ROWS_EVENTv1,mysql.WRITE_ROWS_EVENTv2, mysql.UPDATE_ROWS_EVENTv2, mysql.DELETE_ROWS_EVENTv2},
    }
    go BinlogDump.StartDumpBinlog(filename, position, 100,reslut,"",0)
    go func() {
        for {
            v := <-reslut
            log.Printf("monitor reslut:%s \r\n", v)
        }
    }()

    for {
        time.Sleep(10 * time.Second)
    }
```


##### DDL 支持说明

当前只支持字段在表结构末尾追加新字段，如果配置的二进制位点是在DDL 之前的位点，会出现数据和字段对应不上

数据类型修改之后，如果配置的二进制位点是在DDL 之前的位点，可能会出现数据出错


##### 存储类型数据转换

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