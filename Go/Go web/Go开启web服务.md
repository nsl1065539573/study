# Go web服务

### 标准库启动web服务

go语言内置了`net/http`包，封装了HTTP网络编程的基础接口。下面通过简单的例子来简单介绍一下这个库的使用。

```go
package main
import (
	"io"
	"net/http"
	"log"
)
// hello world, the web server
func HelloServer(w http.ResponseWriter, req *http.Request) {
	io.WriteString(w, "hello, world!\n")
}
func main() {
	http.HandleFunc("/hello", HelloServer)
	err := http.ListenAndServe(":12345", nil)
	if err != nil {
		log.Fatal("ListenAndServe: ", err)
	}
}
```

如上是go语言标准库中的案例，我们首先声明了一个路由：`/hello`，然后为该路由指定处理方法：`helloServer`，之后启动了web服务：`err := http.ListenAndServe(":12345", nil)`，之后使用`go run`运行该文件，访问`http://localhost:12345/hello`将会获得"hello world!"。

**查看`http.ListenAndServe(":9999", nil)`方法:**

```go
package http

type Handler interface {
    ServeHTTP(w ResponseWriter, r *Request)
}

func ListenAndServe(addr string, handler Handler) error
```

我们发现它的第二个参数传入的应该是`Handler`接口，我们点开go的github查看其源码：

```go
// ListenAndServe listens on the TCP network address addr and then calls
// Serve with handler to handle requests on incoming connections.
// Accepted connections are configured to enable TCP keep-alives.
//
// The handler is typically nil, in which case the DefaultServeMux is used.
//
// ListenAndServe always returns a non-nil error.
func ListenAndServe(addr string, handler Handler) error {
	server := &Server{Addr: addr, Handler: handler}
	return server.ListenAndServe()
}
// ListenAndServe listens on the TCP network address srv.Addr and then
// calls Serve to handle requests on incoming connections.
// Accepted connections are configured to enable TCP keep-alives.
//
// If srv.Addr is blank, ":http" is used.
//
// ListenAndServe always returns a non-nil error. After Shutdown or Close,
// the returned error is ErrServerClosed.
func (srv *Server) ListenAndServe() error {
	if srv.shuttingDown() {
		return ErrServerClosed
	}
	addr := srv.Addr
	if addr == "" {
		addr = ":http"
	}
	ln, err := net.Listen("tcp", addr)
	if err != nil {
		return err
	}
	return srv.Serve(ln)
}
```

我们可以通过重写`ServeHTTP(w ResponseWriter, r *Request)`来实现`Handler`接口，在启动服务时使用我们自定义的`Handler`。

### 实现http.Handler接口

```go
type Handler func(w http.ResponseWriter, req *http.Request)

// 继承 http.Handler
type engine struct {
	router map[string]Handler
}

// 实现 http.Handler   判断URL与method是否已经注册
func (e *engine) ServeHTTP(w http.ResponseWriter, req *http.Request) {
	key := req.Method + "-" + req.URL.Path
	if v, ok := e.router[key]; ok {
		v(w, req)
	} else {
		fmt.Fprintf(w, "404 not found\n")
	}
}
```

我们采用map来存储路由信息，判断是否存在路由并且调用何种处理方法。

根据原作者的目录设计，我们与其目录一样，将web框架的处理放在gee目录下

```
gee/
  |--gee.go
  |--go.mod
main.go
go.mod
```



