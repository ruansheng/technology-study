## Go创建Web服务器的几种方式

### http.HandleFunc函数
```
使用HandleFunc函数是http封装好的一个函数，可以直接使用，
第一个参数是web请求路径，第二个参数是的 func(writer http.ResponseWriter, request *http.Request)函数。

再使用http.ListenAndServe(":8080",nil)语句，监听8080端口

其中http.ResponseWriter代表对客户端的响应体，而http.Request代表客户端发送服务器的请求数据。

func hello(writer http.ResponseWriter, request *http.Request) {
    writer.Write([]byte("hello world !"));
}

func main(){
    http.HandleFunc("/hello", hello)
    log.Fatal(http.ListenAndServe(":8080",nil))
}
```

### http.Handle函数
```
跟HandleFunc一样，Handle也是http封装好的函数，第一个参数跟HandleFunc一样，
而第二个参数则是必须是实现了http.Handler接口的类型，http.Handler在http包的定义如下：
type Handler interface {
	ServeHTTP(ResponseWriter, *Request)
}

下面我们定义一个Controller结构体，在该结构定义ServeHTTP方法，因此Controller结构也实现http.Handler接口，
而通过http.HandlerFunc也能将hello方法转成一个实现http.HandlerFunc，http.HandlerFunc也实现http.Handler，http.HandlerFunc在http包的定义如下：
type HandlerFunc func(ResponseWriter, *Request)
func (f HandlerFunc) ServeHTTP(w ResponseWriter, r *Request) {
	f(w, r)
}

下面是自定义:
type Controller struct {}
func (c Controller)ServeHTTP(writer http.ResponseWriter, request *http.Request){
    writer.Write([]byte("hello,1"));
}

func hello(writer http.ResponseWriter, request *http.Request) {
    writer.Write([]byte("hello,2"));
}

func main(){
    http.Handle("/hello1",&Controller{})
    http.Handle("/hello2",http.HandlerFunc(hello))
    log.Fatal(http.ListenAndServe(":8080",nil))
}

```