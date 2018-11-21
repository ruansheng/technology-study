## nginx做4层代理
nginx从1.9.0开始，新增加了一个stream模块，用来实现四层协议的转发、代理或者负载均衡等，但是需要在编译选项中添加 --with-stream

## 编译安装
```
下面是我安装编译的选项
# ./sbin/nginx -V
nginx version: nginx/1.15.0
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-28) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx --with-http_ssl_module --with-http_stub_status_module --with-file-aio --with-stream
```

## 配置
```
注意: stream{}和http{}属于同一层级的
stream {  
   upstream mysql_tcp_proxy {
        hash $remote_addr consistent;  #远程地址做个hash
        server 10.111.12.87:3306;
   }
   server {
        listen 2222;
        proxy_connect_timeout 1s;
        proxy_timeout 10s;  #后端连接超时时间
        proxy_pass mysql_tcp_proxy;
   }
}
```
