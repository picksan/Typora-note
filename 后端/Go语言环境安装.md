# GO语言安装与配置

- 下载地址：[The Go Programming Language (google.cn)](https://golang.google.cn/)

下载windows的安装程序，一路点击下去。

安装完成后，修改环境变量，go的安装路径的bin目录，以及设置go的工作目录GOPATH（自己定），和工作目录下的bin目录

环境变量（系统变量或者用户变量）设置如下

| Path | D:\Golang\bin           |
| -------- | ----------------------- |
| GOPATH   | F:\code\GoWorkDirectory |
| Path     | %GOPATH%\bin            |

在GOPATH路径下创建src文件夹，专门用来放源代码



## 使用编辑器：VS CODE

vs code安装go插件提示下载失败

原因：某些路径的github无法访问

推荐使用goproxy代替

参考链接：[goproxy.cn/README.zh-CN.md at master · goproxy/goproxy.cn (github.com)](https://github.com/goproxy/goproxy.cn/blob/master/README.zh-CN.md)

```
$ go env -w GO111MODULE=on
$ go env -w GOPROXY=https://goproxy.cn,direct
windows
打开powershell
C:\> $env:GO111MODULE = "on"
C:\> $env:GOPROXY = "https://goproxy.cn"
或者手动设置环境变量，如下
1. 打开“开始”并搜索“env”
2. 选择“编辑系统环境变量”
3. 点击“环境变量…”按钮
4. 在“<你的用户名> 的用户变量”章节下（上半部分）
5. 点击“新建…”按钮
6. 选择“变量名”输入框并输入“GO111MODULE”
7. 选择“变量值”输入框并输入“on”
8. 点击“确定”按钮
9. 点击“新建…”按钮
10. 选择“变量名”输入框并输入“GOPROXY”
11. 选择“变量值”输入框并输入“https://goproxy.cn”
12. 点击“确定”按钮
```

