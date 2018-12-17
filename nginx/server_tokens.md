### server_tokens
隐藏ngxin版本号
```
http{
    server_tokens on;
}
```

### server_tokens on
默认时 server_tokens on
```
# curl -I http://127.0.0.1:9999/
HTTP/1.1 200 OK
Server: nginx/1.15.0
Date: Mon, 17 Dec 2018 08:29:32 GMT
Content-Type: text/html
Content-Length: 13
Last-Modified: Mon, 17 Dec 2018 08:24:21 GMT
Connection: keep-alive
ETag: "5c175d35-d"
Accept-Ranges: bytes
```

### server_tokens off
由下可见nginx版本号被隐藏了
```
# curl -I http://127.0.0.1:9999/
HTTP/1.1 200 OK
Server: nginx
Date: Mon, 17 Dec 2018 08:30:52 GMT
Content-Type: text/html
Content-Length: 13
Last-Modified: Mon, 17 Dec 2018 08:24:21 GMT
Connection: keep-alive
ETag: "5c175d35-d"
Accept-Ranges: bytes
```