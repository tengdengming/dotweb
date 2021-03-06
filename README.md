### dotweb
简约大方的go Web微型框架

### 安装：

```
go get -u github.com/devfeel/dotweb
```

### 快速开始：

```go
func StartServer() error {
	//初始化DotServer
	dotserver := dotweb.New()
	//设置dotserver日志目录
	dotserver.SetLogPath("/home/logs/wwwroot/")
	//设置路由
	dotserver.HttpServer.GET("/index", func(ctx *dotweb.HttpContext) {
		ctx.WriteString("welcome to my first web!")
	})
	//开始服务
	err := dotserver.StartServer(80)
	return err
}

```
### 特性
* 支持静态路由、参数路由、组路由（前缀路由/命名空间）和路由命名
* 路由支持文件/目录服务
* 中间件支持
* 支持JSON/JSONP/HTML格式输出
* 统一的HTTP错误处理
* 统一的日志处理

### 路由
#### 常规路由
目前支持GET\POST\HEAD\OPTIONS\PUT\PATCH\DELETE 这几类请求方法
另外也支持HiJack\WebSocket\ServerFile三类特殊应用
```go
1、HttpServer.GET(path string, handle HttpHandle)
2、HttpServer.POST(path string, handle HttpHandle)
3、HttpServer.HEAD(path string, handle HttpHandle)
4、HttpServer.OPTIONS(path string, handle HttpHandle)
5、HttpServer.PUT(path string, handle HttpHandle)
6、HttpServer.PATCH(path string, handle HttpHandle)
7、HttpServer.DELETE(path string, handle HttpHandle)
8、HttpServer.HiJack(path string, handle HttpHandle)
9、HttpServer.WebSocket(path string, handle HttpHandle)
```
接受两个参数，一个是URI路径，另一个是 HttpHandle 类型，设定匹配到该路径时执行的方法；
#### 静态路由
静态路由语法就是没有任何参数变量，pattern是一个固定的字符串。
```go
package main

import (
    "github.com/devfeel/dotweb"
)

func main() {
    dotserver := dotweb.New()
    dotserver.Get("/hello", func(ctx *dotweb.HttpContext) {
        ctx.WriteString("hello world!")
    })
    dotserver.StartServer(80)
}
```
测试：
curl http://127.0.0.1/hello
#### 参数路由
参数路由以冒号 : 后面跟一个字符串作为参数名称，可以通过 HttpContext的 GetRouterName 方法获取路由参数的值。
```go
package main

import (
    "github.com/devfeel/dotweb"
)

func main() {
    dotserver := dotweb.New()
    dotserver.Get("/hello/:name", func(ctx *dotweb.HttpContext) {
        ctx.WriteString("hello " + ctx.GetRouterName("name"))
    })
    dotserver.Get("/news/:category/:newsid", func(ctx *dotweb.HttpContext) {
    	category := ctx.GetRouterName("category")
	newsid := ctx.GetRouterName("newsid")
        ctx.WriteString("news info: category=" + category + " newsid=" + newsid)
    })
    dotserver.StartServer(80)
}
```
测试：
<br>curl http://127.0.0.1/hello/devfeel
<br>curl http://127.0.0.1/hello/category1/1
