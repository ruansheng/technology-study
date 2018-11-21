### --with-http_stub_status_module
http_stub_status_module可以用来定位nginx的瓶颈，http_stub_status_module模块的源码nginx的自带的，只是nginx默认并没有安装这个模块。
查看当前nginx是否安装的模块可以用nginx -V来查看:
```
# nginx -V
nginx version: nginx/1.15.0
built by gcc 4.8.5 20150623 (Red Hat 4.8.5-28) (GCC)
built with OpenSSL 1.0.2k-fips  26 Jan 2017
TLS SNI support enabled
configure arguments: --prefix=/usr/local/nginx --with-pcre --with-http_ssl_module --with-http_stub_status_module --with-http_gzip_static_module --add-module=/root/nginx-1.15.0/ngx_log_if
```

### 安装http_stub_status_module模块
```
首次安装编译
直接 ./configure --prefix=/usr/local/nginx --with-http_stub_status_module
make && make install 即可
安装完成之后通过nginx -V查看是否安装成功

在升级安装
重新编译 ./configure --prefix=/usr/local/nginx --with-http_stub_status_module
make && make install 即可
安装完成之后通过nginx -V查看是否安装成功，注意:如果是正在运行的nginx，需要重新启动nginx进程才会生效
```

### http_stub_status_module模块的配置
```
在nginx.conf的server块中添加如下代码:
location /nginx_status {
    # Turn on nginx stats
    stub_status on;
    # I do not need logs for stats
    access_log   off;
    # Security: Only allow access from 192.168.1.100 IP #
    #allow 192.168.1.100;
    # Send rest of the world to /dev/null #
    #deny all;
}
```

### nginx状态查看
```
访问nginx的状态，就可以通过 curl http://127.0.0.1/nginx_status访问了

返回结果类似于：
Active connections: 1  
server accepts handled requests 
 655 655 1985 
Reading: 0 Writing: 1 Waiting: 0  

// Active connections: 对上游服务发起的连接数
// server accepts handled requests  总共处理了655个连接,成功创建655次握手(证明中间没有失败的),总共处理了1985个请求.
// Reading: nginx读取到客户端的Header信息数. 
// Writing: nginx返回给客户端的Header信息数.
// 需要注意的是如果reading或writing的值很高，说明正在处理的数据量很大，可能是因为后端的动态就用程序处理慢,这个时候需要对后端进行优化。
// Waiting: 开启keep-alive的情况下,这个值等于 Active – (Reading + Writing),意思就是nginx已经处理完成,正在等候下一次请求指令的驻留连接.所以,在访问效率高,请求很快被处理完毕的情况下,Waiting数比较多是正常的.waiting的意思就是
// 已经将请求处理完毕，并且把数据已经返回给了客户端，已经闲置在等待接受下次请求了，因此这个值比较高往往说明请求处理的很快。一般应该是writing和reading越小“越好”，而waiting越高“越好”。
```
