---
title: "数据写入"
publishdate: 2020-08-11T08:05:00Z
draft: false
weight: 63
---

在每次数据写完之后,都会进行判断文件大小是否大于16MB,如果大于后,标记下一次是否需要创建新文件


```
const FileMaxSize int64 = 16 * 1024 * 1024
func (This *Queue) Append(content string) error{
    return This.AppendBytes([]byte(content))
}

func (This *Queue) AppendBytes(b []byte) error{
    This.Lock()
    defer This.Unlock()
    if This.writeInfo == nil{
        This.writeInfoInit()
    }
    if This.writeInfo.pos > 0 {
        This.writeInfo.fd.Write([]byte(";"))
        This.writeInfo.pos+=1
    }
    n := Int32ToBytes(int32(len(b)))
    _,err0 := This.writeInfo.fd.Write(n)
    if err0 != nil{
        log.Fatal(err0)
    }
    This.writeInfo.fd.Write(b)
    This.writeInfo.fd.Write(n)
    This.writeInfo.pos += 8+int64(len(b))

    if This.writeInfo.pos >= FileMaxSize{
        This.writeInfo.fd.Close()
        This.writeInfo = nil
    }

    return nil
}


func (This *Queue) writeInfoInit(){
    This.maxId = This.maxId+1
    fileName := This.path+"/"+fmt.Sprint(This.maxId)+".list"
    fd0,err:=os.OpenFile(fileName,os.O_RDWR|os.O_CREATE|os.O_APPEND,0700)
    if err!=nil{
        log.Fatal("filequeue writeInfoInit err:",err)
    }
    This.fileCount++
    This.writeInfo = &FileInfo{
        fd:fd0,
        name:fileName,
        pos:0,
    }
}

```

[源码参考](https://github.com/brokercap/Bifrost/tree/master/server/filequeue)