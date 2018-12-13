### nginx 配置项 add_header 踩坑记
nginx配置文件通过使用 add_header 指令来设置response header

#### 踩坑上下文
```
在做JWT接口签名验证中，因为是前后端分离，后端接口用nginx作反向代理，在nginx中配置中添加跨域响应头
配置如下:
server {
    listen       80;
    server_name xxx.xxx.com;
    ...

    if ($http_origin ~ http://xxx.xxx.com){
    	set $allow $http_origin;
    }
    add_header 'Access-Control-Allow-Origin' $allow;
    location / {
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header Host $http_host;
        proxy_redirect off;
        proxy_pass http://127.0.0.1:10000;
    }
}

反向代理上游服务 127.0.0.1:10000 返回:
HttpCode=200 正常返回       client能看到 'Access-Control-Allow-Origin'
HttpCode=401 JWT验证失败    client看不到 'Access-Control-Allow-Origin'
```

#### 解决
```
由于nginx配置项add_header配置项导致在401情况下没有添加上Access-Control-Allow-Origin

官方解释add_header:
Syntax:	add_header name value [always];
Default:	—
Context:	http, server, location, if in location

Adds the specified field to a response header provided that the response code equals 200, 201 (1.3.10), 204, 206, 301, 302, 303, 304, 307 (1.1.16, 1.0.13), or 308 (1.13.0). The value can contain variables.
There could be several add_header directives. These directives are inherited from the previous level if and only if there are no add_header directives defined on the current level.
If the always parameter is specified (1.7.5), the header field will be added regardless of the response code.

意思是默认只对 200, 201 (1.3.10), 204, 206, 301, 302, 303, 304, 307 (1.1.16, 1.0.13), or 308 (1.13.0)状态码的响应添加响应头
但在1.7.5版本之后加了一个 always 选项，可以对所有响应都添加response header

改成 add_header 'Access-Control-Allow-Origin' $allow always; 就完美解决了
```