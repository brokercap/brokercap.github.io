---
title: "插件开发教程"
lastmod: 2019-11-28T22:59:15Z
draft: false
weight: 123
---

**Bifrost**  v1.1.x 版本开始提供接口，在 linux 下支持通过 go1.12+ 版本的 plugin 包生成的 so 进行动态的加载到正在运行的 Bifrost 进程中去

Bifrost 本身提供了相关的接口进行插件注入，也同样提供了相关的测试接口，方便插件开过程中进行调试

#### 创建插件目录

- 在 github.com/brokercap/Bifrost/plugin 目录下创建和插件名称一致的目录名称
(当然这个你也可以在自己的新建一个项目开发插件，但这个目录名必须和插件名保持一致)

拿redis插件作为案例来讲，创建redis 目录

```
github.com/brokercap/Bifrost/plugin/redis
```

- 在插件目录下子目录及文件


```
-rwxr-xr-x 1 root root    90 Mar 18 18:38 redis.go
-rwxr-xr-x 1 root root 14732 Mar 27 10:30 redisTest.go
drwxr-xr-x 0 root root     0 Mar 26 18:41 src
drwxr-xr-x 0 root root     0 Mar 26 08:58 www
```


src 目录

```
-rwxr-xr-x 1 root root 5783 Mar 26 18:41 redis.go
```

www 目录

```
-rwxr-xr-x 1 root root 1325 Mar 26 08:58 doc.html
-rwxr-xr-x 1 root root 3864 Mar 18 10:57 redis.html
-rwxr-xr-x 1 root root 1839 Mar 20 08:50 redis.js
```

```

**src** 是插件go源码处理逻辑所放目录，当然这个你可以根据自己的意愿改，只要在编译的时候，指定这个目录就行了

**www** 是插件存放html及js等静态文件的目录，这个目录名必须为www, 当编译完运行的时候，需要前 当前插件目录及www拷贝到 Bifrost 运行目录下的 plugin 下

redis.html 及 redis.js 两个文件名必须和插件名保持一致，在Bifrsot 管理界面进行加载插件界面的时候，是通过插件名 + .html ,+ .js 进行加载的，
先加载html 再加载 js

doc.html 为插件使用手册界面

#### 插件接口

```
github.com/brokercap/Bifrost/plugin/driver
```

- **往bifrost注册插件**

```
func init(){
    driver.Register("redis",&MyConn{},VERSION,BIFROST_VERION)
}

```

redis : 插件名称，这个名称必须和插件目录名保持一致，以及www目录下的html及js文件名
&MyConn{} : 插件提供bifrost调用的 对象
VERSION : 当前自己开发的插件版本
BIFROST_VERION : 当前开发时候bifrost的版本

- **MyConn 实现接口**
```
type MyConn struct {}

func (MyConn *MyConn) Open(uri string) driver.ConnFun{
    return newConn(uri)
}

func (MyConn *MyConn) GetUriExample() string{
    return "pwd@tcp(127.0.0.1:6379)/0 or 127.0.0.1:6379"
}

func (MyConn *MyConn) CheckUri(uri string) error{
    c:= newConn(uri)
    if c.err != nil{
        return c.err
    }
    c.Close()
    return nil
}
```

Open : Bifrost调用插件接口

GetUriExample ：提供插件连接uri，比如连接 redis 的ip+port

CheckUri ：用于bifrost调用，确认用户输入的连接地址是否有效

- **实现传输数据的接口**

```
type Conn struct {
    Uri         string
}

func (This *Conn) SetParam(p interface{}) (interface{},error){}

func (This *Conn) Connect() bool {}
func (This *Conn) Insert(data *driver.PluginDataType) (*driver.PluginBinlog,error) {}

func (This *Conn) Update(data *driver.PluginDataType) (*driver.PluginBinlog,error) {}
func (This *Conn) Del(data *driver.PluginDataType) (*driver.PluginBinlog,error) {}
func (This *Conn) Query(data *driver.PluginDataType) (*driver.PluginBinlog,error) {}
func (This *Conn) Commit() (*driver.PluginBinlog,error){}
```

Connect :  用户在调用 MyConn 对象Open方法的时候,会进行进行Connet的方法，这个方法里进行连接Redis等第三方服务

SetParam :
每一次执行Insrt,Update,Delete,Query及Commit 接口之行,都会执行一次SetParam. 每个表第一次执行SetParam的时时候,提交的是 

*用户在插件界面配置的参数信息。传进来的参数是由插件js ，方法doGetPluginParam() 里返回的result.data 的数据*.. 数据结构为 map[string]interface{}

但是在第二次以后执行的时候,传入的是 由上一次SetParam 接口返回的 第一个结果值.

例如A同步数据.伪代码如下:

```
var tableParamOriginal = map[sting]interface{}
var tableParam interface{}
var err error
for{
    slect{
        case data := <- ch
        pluginObj := GetPlugin(ToServerKey)
        if tableParam == nil{
            tableParam,err = pluginObj.SetParam(tableParamOriginal)
        }
        switch data.EventType{
        case "insert":
        pluginObj.Insert(data)
        break
        case "update":
        pluginObj.Update(data)
        break
        case "delete":
        pluginObj.Delete(data)
        break
        case "sql":
        pluginObj.Query(data)
        break
        }
        break
        
        case <-time.After(5 * time.Second):
            pluginObj := GetPlugin(ToServerKey)
            if tableParam == nil{
                tableParam,err = pluginObj.SetParam(tableParamOriginal)
            }
            pluginObj.Commit()
        }
    }
    
}

````

编写插件的时候,应该判断一下SetParam传进来的是什么数据结构,进行优化

因为可以自行在SetParam 接口里接传进来的map数据结构转面struct之后再以指针的时候再返回回去,这样下一次数据数据同步的时候,就只要传递指标了.

以 Redis 插件为例:

```
func (This *Conn) GetParam(p interface{}) (*PluginParam,error){
    s,err := json.Marshal(p)
    if err != nil{
        return nil,err
    }
    var param PluginParam
    err2 := json.Unmarshal(s,&param)
    if err2 != nil{
        return nil,err2
    }
    This.p = &param
    return &param,nil
}

func (This *Conn) SetParam(p interface{}) (interface{},error){
    if p == nil{
        return nil,fmt.Errorf("param is nil")
    }
    switch p.(type) {
    case *PluginParam:
        This.p = p.(*PluginParam)
        return p,nil
    default:
        return This.GetParam(p)
    }
}
```

Insert : 是数据库中新增数据的情况下会进行回调的方法,数据结构为 
github.com/brokercap/Bifrost/plugin/driver 下面的 PluginBinlog


Update ：在数据进行修改的时候，回调的方法

Delete : 数据被删除的时候回调的方法

Query : Binlog 中记录的日志为sql 格式的时候，比如alter等语句，进行回调的方法

Commit ：假如连续5秒都没有数据更新，回调一次这个方法
 

#### 插件接口数据结构
```
type PluginDataType struct {
    Timestamp       uint32
    EventType       string
    Rows            []map[string]interface{}
    Query           string
    SchemaName      string
    TableName       string
    BinlogFileNum   int
    BinlogPosition  uint32
}
```

**TimeStamp** : 事件发生的时间戳

**EventType** ：事件类型，insert,uupdate,delete,sql

**Rows**      ：在事件类型为 insert ,update,delete 的时候，这个数组里会有数据，insert , delete ，只有有Rows[0],但是在update类型的时候，Rows[0]为更新之前的数据，Rows[1] 为更新之后的数据。

**Query**    : 当EventType == sql 的时候，这个字段不为空，存储的是执行的sql语句

**SchemaName**: 数据名名称

**TableName** : 表名

**BinlogFileNum** : 二进制日志文件后缀名，去掉了多余了000，比如二进制文件为 mysql-bin.000070，而这个字段存的是70

**BinlogPosition** : 二进制位点


```
type PluginBinlog struct {
    BinlogFileNum int
    BinlogPosition uint32
}
```

**BinlogFileNum** : 对标PluginDataType.BinlogFileNum
**BinlogPosition** : 对标PluginDataType.BinlogPosition


#### 其他函数

```
func TransfeResult(val string, data *PluginDataType,rowIndex int) string {}
```

这个函数是由插件调用,将用户input输入的参数里的{$SchemaName} 等标签进行替换的方法

val : 拥有标签的字符串

data : 事件数据

rowIndex : 这个为Rows[] 里的下标值，代表用哪一个Rows[rowIndex]里的数据进行替换指定的标签

{$TableName} ： 会被替换成 data.TableName的值

{$SchemaName} ： 会被替换成 data.SchemaName

{$EventType} ： 会被替换成 data.EventType

{$字段名} : 会被替换成 个Rows[rowIndex][字段名] 的值

- 插件开发测试

```
github.com/brokercap/Bifrost/sdk/pluginTestData
```

pluginTestData 包提供了 新增，更新，删除，及 SQL 事件的模拟数据

在src 下新增 *_test.go 文件 进行单元测试，案例如下：

```
package src_test

import (
    "testing"
    "log"
    "github.com/brokercap/Bifrost/sdk/pluginTestData"
    MyPlugin "github.com/brokercap/Bifrost/plugin/redis/src"
    "github.com/brokercap/Bifrost/test/pluginTest"
    //"github.com/garyburd/redigo/redis"
    "github.com/go-redis/redis"
    "fmt"
    "strings"
)

var url string = "10.40.2.41:6379"

var redisConn *redis.Client
func getParam() map[string]interface{}{
    p := make(map[string]interface{},0)
    p["KeyConfig"] = "{$SchemaName}-{$TableName}-{$id}"
    p["DataType"] = "json"
    p["Type"] = "set"
    p["DataType"] = "json"
    return p
}

func initRedisConn() error{
    redisConn =  redis.NewClient(&redis.Options{
        Addr:       url,
        Password:   "", // no password set
        DB:         0,
    })
    if redisConn == nil{
        return fmt.Errorf("connect error")
    }
    return nil
}

func TestChechUri(t *testing.T){
    var url string = "127.0.0.1:6379"
    myConn := MyPlugin.MyConn{}
    if err := myConn.CheckUri(url);err!= nil{
        log.Println("TestChechUri err:",err)
    }else{
        log.Println("TestChechUri success")
    }
}

func TestSetParam(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())
}

func TestInsert(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())
    conn.Insert(pluginTest.GetTestInsertData())
    log.Println("test over")
}

func TestUpate(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())
    conn.Insert(pluginTest.GetTestUpdateData())
    log.Println("test over")
}


func TestDelete(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())
    conn.Insert(pluginTest.GetTestDeleteData())
    log.Println("test over")
}


func TestQuery(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())
    conn.Insert(pluginTest.GetTestQueryData())
    log.Println("test over")
}

func TestCommit(t *testing.T){
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.Commit()
    log.Println("test over")
}

func TestCheckData(t *testing.T){

    var err error
    err = initRedisConn()
    if err!=nil{
        t.Fatal(err)
    }
    myConn := MyPlugin.MyConn{}
    conn := myConn.Open(url)
    conn.SetParam(getParam())

    e := pluginTestData.NewEvent()

    var checkResult map[string][]string

    t.Log("")
    t.Log("insert test start")

    insertData := e.GetTestInsertData()
    //log.Println(insertData)
    _, err = conn.Insert(insertData)
    if err != nil{
        t.Fatal(err)
    }
    var key string
    key = insertData.SchemaName+"-"+insertData.TableName+"-"+fmt.Sprint(insertData.Rows[0]["id"])
    var c string
    c,err = redisConn.Get( key).Result()
    if err!=nil{
        t.Fatal(err)
    }

    checkResult,err = e.CheckData(insertData.Rows[0],c)
    if err != nil{
        log.Fatal(err)
        t.Fatal(err)
    }


    for _,v := range checkResult["ok"]{
        t.Log(v)
    }

    for _,v := range checkResult["error"]{
        t.Error(v)
    }


    t.Log("")
    t.Log("update test start")

    updateData := e.GetTestUpdateData()
    _, err = conn.Update(updateData)
    if err != nil{
        t.Fatal(err)
    }

    key = updateData.SchemaName+"-"+updateData.TableName+"-"+fmt.Sprint(updateData.Rows[1]["id"])
    c,err = redisConn.Get( key).Result()
    if err!=nil{
        t.Fatal(err)
    }

    checkResult,err = e.CheckData(updateData.Rows[1],c)
    if err != nil{
        t.Fatal(err)
    }

    for _,v := range checkResult["ok"]{
        t.Log(v)
    }

    for _,v := range checkResult["error"]{
        t.Error(v)
    }


    t.Log("")
    t.Log("delete test start")

    deleteData := e.GetTestDeleteData()

    _, err = conn.Del(deleteData)
    if err != nil{
        t.Fatal(err)
    }

    key = deleteData.SchemaName+"-"+deleteData.TableName+"-"+fmt.Sprint(deleteData.Rows[0]["id"])
    c,err = redisConn.Get( key).Result()
    if strings.Contains(fmt.Sprint(err),"redis: nil") {
        t.Log("key:",key, " delete success")
    }else{
        t.Error("key:",key, " delete error,",err)
    }

    log.Println("test over")
}

//模拟正式环境刷数据
func TestSyncLikeProduct(t *testing.T)  {
    p := pluginTestData.NewPlugin("redis",url)
    err0 := p.SetParam(getParam())
    if err0 != nil{
        t.Fatal(err0)
    }

    var n uint = 10
    err := p.DoTestStart(n)

    if err != nil{
        t.Fatal(err)
    }else{
        t.Log("test success")
    }

}


//模拟正式环境性能测试(只随机生成一条数据。循环提交)
func TestSyncLikeProductForSpeed(t *testing.T)  {
    p := pluginTestData.NewPlugin("redis",url)
    err0 := p.SetParam(getParam())
    p.SetEventType(pluginTestData.INSERT)
    if err0 != nil{
        t.Fatal(err0)
    }

    var n uint = 100
    err := p.DoTestStartForSpeed(n)

    if err != nil{
        t.Fatal(err)
    }else{
        t.Log("test success")
    }

}

```


- 插件编译

和 Bifrost 源码一起静态编译

1. 修改Bifrost import_toserver.go文件

```
github.com/brokercap/Bifrost/plugin/import_toserver.go
```
在import_toserver.go 文件里 import 插件源码

2. go build ./Bifrost.go

3. 将插插目录下的 www 目录拷贝到 github.com/brokercap/Bifrost/plugin/插件名/www


独立编译成so

在插件根目录新建 插件名.go 文件名，案例如下:
```
# ll plugin/redis/
total 17
-rwxr-xr-x 1 root root    90 Mar 18 18:38 redis.go

# cat plugin/redis/redis.go 
package main

import (
_ "github.com/brokercap/Bifrost/plugin/redis/src"
)

func main() {

}
```

```
# cd plugin/redis/
# go build --buildmode=plugin -o redis.so redis.go
# ll
total 7885
-rwxr-xr-x 1 root root      90 Mar 18 18:38 redis.go
-rwxr-xr-x 1 root root 8055309 Mar 29 17:32 redis.so
-rwxr-xr-x 1 root root   14732 Mar 27 10:30 redisTest.go
drwxr-xr-x 0 root root       0 Mar 26 18:41 src
drwxr-xr-x 0 root root       0 Mar 26 08:58 www

```

将生成 redis.so 及 www 拷贝到 Bifrost运行目录下的redis目录下即可

但这个只限linux 平台下，并且Bifrost 配置文件中配置 dynamic_plugin=true才有效，这个配置默认是false
