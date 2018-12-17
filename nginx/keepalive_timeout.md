### keepalive_timeout
会话保持时间
```
客户端和服务器建立连接后客户端分配keep-alive链接超时时间，服务器将在这个超时时间过后关闭链接，
我们将它设置低些可以让ngnix持续工作的时间更长，1.8.1默认为65秒，一般不超过120秒。

http{
    keepalive_timeout  65 60;
    #后面的60为发送给客户端应答报文头部中显示的超时时间设置为60s，如不设置客户端将不显示超时时间。
}
```

#### 配置
```
http{
    keepalive_timeout  65;
}

# curl -I http://127.0.0.1:9999/
...
Connection: keep-alive
...

http{
    keepalive_timeout  65 60;
}
# curl -I http://127.0.0.1:9999/
...
Connection: keep-alive
Keep-Alive: timeout=60
...

http{
    keepalive_timeout  0;
}
# curl -I http://127.0.0.1:9999/
...
Connection: close
...
```
