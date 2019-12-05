---
title: "API接口"
lastmod: 2019-12-05T22:52:15Z
draft: false
weight: 122
---


*Bifrost* 提供相应的监控接口,用于第三方监控服务器对Bifrost进行监控

#### 数据源列表

URL: /db/list?format=json

METHOD: GET

返回数据结构:

```

map[string]DbListStruct

type DbListStruct struct {
    Name                    string    // DbName
    ConnectUri              string    // 连接地址
    ConnStatus              string    // close,stop,starting,running
    ConnErr                 string    // 连接错误
    ChannelCount            int       // 通道数量
    LastChannelID           int       // 最大的通道ID
    TableCount              int       // 设置了同步的表的数量
    BinlogDumpFileName      string    // 位点文件
    BinlogDumpPosition      uint32    // 位点
    MaxBinlogDumpFileName           string    // 到哪一个位点文件停止解析
    MaxBinlogDumpPosition           uint32    // 到哪一个位点停止解析
    ReplicateDoDb           map[string]uint8  // 需要同步的库 
    ServerId                uint32    // 模拟从库的ServerId
    AddTime             int64    // 添加数据源时候的时间戳
}
```

##### 案例
```
func main(){
    //跳过证书验证
    tr := &http.Transport{
        TLSClientConfig: &tls.Config{InsecureSkipVerify: true},
    }
    url := "https://127.0.0.1:21036/db/list?format=json"
    client := &http.Client{Timeout:10 * time.Second,Transport: tr,}
    req, err := http.NewRequest("GET",url, nil)
    if err != nil {
        log.Println(err)
        return
    }
    req.SetBasicAuth("Bifrost","Bifrost123")
    resp, err2 := client.Do(req)
    if err2 != nil{
        log.Println(err2)
        return
    }
    defer resp.Body.Close()

    body, err := ioutil.ReadAll(resp.Body)

    log.Println("body:",string(body))
}
```
```
https://127.0.0.1:21036/db/list?format=json
```

```
{"myTest":{"Name":"myTest","ConnectUri":"root:@tcp(127.0.0.1:3306)/test","ConnStatus":"running","ConnErr":"running","ChannelCount":1,"LastChannelID":1,"TableCount":2,"BinlogDumpFileName":"mysql-bin.000015","BinlogDumpPosition":43311,"MaxBinlogDumpFileName":"","MaxBinlogDumpPosition":0,"ReplicateDoDb":{"bifrost_test":1,"test":1},"ServerId":167,"AddTime":0}}
```


#### 表同步列表

表同步列表是设置了需要同步的表的列表

URL : /synclist?format=json

METHOD : POST | GET

##### 参数

```
dbname       string  数据源名称             可选

toserverkey  string  目标库 ToServerKey    可选

channelid    int     通道id,不是通道名字     可选

schema_name  string  数据库名称             可选

table_name   string  表名称                可选

```

返回数据结构:

```

[]syncListStruct

type syncListStruct struct {
    DbName string `json:"DbName"`   // 数据源名称
    SchemaName string `json:"SchemaName"`  // 数据库名
    TableName string `json:"TableName"`    // 表名
    ToServerList []*server.ToServer `json:"ToServerList"`
}

type ToServer struct {
    ToServerID      int         
    PluginName          string
    MustBeSuccess       bool
    FilterQuery     bool
    FilterUpdate        bool
    FieldList           []string
    ToServerKey         string
    BinlogFileNum       int                     // 当前同步的位点文件
    BinlogPosition      uint32                  // 当前同步的位点
    PluginParam         map[string]interface{}  // 插件参数
    Status              string                  // 同步状态
    Error               string                  // 同步失败时候产生的错误信息.假如这个错误不为空,则说明同步有问题了
    ErrorWaitDeal       int                     // 1 为下一次重试的时候,则错过一条数据,界面上Miss 按钮,就是将这个值个性为 1
    ErrorWaitData       interface{}             // 当前同步失败的时候的 数据
}
```

这个接口可以用于监控同步是否正常,当 Error 不为空的时候,则说明同步可能存在问题

监控也要以参考 *Bifrost* 报警模块

##### 案例
```
https://127.0.0.1:21036/synclist?dbname=myTest&schema_name=&table_name=&toserverkey=&channelid=0&format=json

```

```
[{"DbName":"myTest","SchemaName":"bifrost_test","TableName":"binlog_field_test","ToServerList":[{"ToServerID":1,"PluginName":"hprose","MustBeSuccess":true,"FilterQuery":false,"FilterUpdate":false,"FieldList":[],"ToServerKey":"sdfsdfsf","BinlogFileNum":0,"BinlogPosition":0,"PluginParam":{},"Status":"","Error":"","ErrorWaitDeal":0,"ErrorWaitData":null}]},{"DbName":"myTest","SchemaName":"test","TableName":"test","ToServerList":[]}]

```

#### 流量

流量接口,可提供 1分钟,10分钟,1小时,8小时,24小时内的的流量信息

返回的流量是全量信息,不是增量,但可以自行通过全量数据的差值算出增量数据

URL : /flow/get?format=json

METHOD : GET

##### 参数
```

dbname       string  数据源名称             可选

channelid    int     通道id,不是通道名字     可选

schema       string  数据库名称             可选

table_name   string  表名称                可选

type         string  查询数据类型(minute,tenminute,hour,eighthour,day)          必填      

```

返回数据结构:

```
[]CountContent

type CountContent struct {
    Time int64               // 时间戳
    Count int64              // 全量条数
    ByteSize int64           // 全量大小,单位 byte
}

```

##### 案例

```
https://127.0.0.1:21036/flow/get?format=json&type=minute
```
```
[
{"Time":1555079740,"Count":0,"ByteSize":0},{"Time":1555079745,"Count":0,"ByteSize":0},
{"Time":1555079750,"Count":0,"ByteSize":0},{"Time":1555079755,"Count":0,"ByteSize":0},
{"Time":1555079760,"Count":0,"ByteSize":0},{"Time":1555079765,"Count":0,"ByteSize":0},
{"Time":1555079770,"Count":0,"ByteSize":0},{"Time":1555079775,"Count":0,"ByteSize":0},
{"Time":1555079780,"Count":0,"ByteSize":0},{"Time":1555079790,"Count":0,"ByteSize":0},{"Time":1555079800,"Count":0,"ByteSize":0},{"Time":1555079805,"Count":0,"ByteSize":0}
]
```
