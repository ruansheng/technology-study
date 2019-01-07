### 过滤初衷
```
有时在nginx的access.log中因为上游的负载均衡通过http协议对下游nginx进行存活检测，这样会在下游nginx的access.log中记录太多无用的日志，比如
{"user_ip": "-","lan_ip": "100.116.233.196","uri","/index.html",log_time": "2018-11-05T09:38:12+08:00","user_req": "HEAD / HTTP/1.0","http_code": "200","body_bytes_sents": "0","user_ua": "-","http_referer":"-","http_x_forwarded_for":"-","request_time":"0.000"}
导致收集access.log处理量比较大、无法集中关注到有用的日志，所以想把这部分日志过滤掉，不往access.log中记录
```

### 解决方案
```
1. 上游负载均衡组件将http协议存活检测改成tcp存活检测
    这种方式可能是最有效的，但是有时受限于已上线的业务，可能调整起来不这么简单
2. 通过nginx的第三方模块来拦截这部分日志
     这种方案对业务方式透明的，只需修改下游nginx即可，经调研可以使用ngx_log_if来达到拦截的目的
```

### ngx_log_if模块的使用
```
经过调研，可用nginx的第三方模块ngx_log_if来对日志进行拦截，不记录到access.log文件中

具体实施方案如下:
1.先到Github下载ngx_log_if地址https://github.com/cfsego/ngx_log_if
2.重新编译nginx，把ngx_log_if模块编译进去，注意:nginx支持日更新编译，即可用用之前的参数加上新模块一起重新编译生成nginx二进制文件覆盖之前的文件，然后重启nginx即可，是平滑升级
3.配置参数 ./configure --prefix=/usr/local/nginx --with-http_ssl_module --with-pcre --add-module=/root/nginx-1.15.0/ngx_log_if
4.重新编译 make
5.编译安装 make install
6.在server{} 或者location{} 块中配置要过滤的日志条件，重启nginx即可生效(注意:nginx -s reload并不行，因为进程并没有重启，需要先nginx -s stop ，然后再启动nginx)

server {
    access_log_bypass_if ($status = 400);   //不记录状态码为400的日志信息
    access_log_bypass_if ($host ~* 'nolog.com'); // 不记录host为nolog.com的日志信息
    access_log_bypass_if ($uri = 'status.nginx') and; // 不记录uri为status.nginx的日志信息
    access_log_bypass_if ($status = 200); 不记录状态码为200的日志信息
}
server {
    access_log_bypass_if ($status = 400);
    location / {
        access_log_bypass_if ($host ~* 'nolog.com');
    }
}
```

