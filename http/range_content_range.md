## Range Content-Range
范围请求是允许从指定的位置下载资源的机制

### Range
```
在请求头中携带 
Range: bytes=5001-10000   从5001到10000字节
或者
Range: bytes=5001-   从5001之后的全部
或者
Range: bytes=-3001, 5000-7000      从一开始到3000字节和5000到7000字节
```

### Content-Range
```
在响应头中携带 Content-Range: bytes 5001-10000/10000
针对范围请求，响应会返回状态码206 Partial Content的响应报文
如果服务端无法响应范围请求，则会返回状态码200 OK和完整的内容

```


