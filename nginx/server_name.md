### server_name
nginx中的server_name指令主要用于配置基于名称虚拟主机

#### 配置
```
server_name  localhost www.a.com; #多个域名用空格间隔即可
server_name  192.168.0.2;         #IP是本机的网卡IP地址
```

#### 匹配顺序
```
server_name指令在接到请求后的匹配顺序如下:
1、准确的server_name匹配
server {
    listen 80;
    server_name domain.com www.domain.com;
}

2、以通配符开始的字符串
server {
    listen 80;
    server_name .domain.com;
}

3、以通配符结束的字符串
server {
    listen 80;
    server_name www.;
}

4、匹配正则表达式
server {
    listen 80;
    server_name ~^(?.+).domain.com$;
}

nginx将按照1,2,3,4的顺序对server name进行匹配，只有有一项匹配以后就会停止搜索，
所以我们在使用这个指令的时候一定要分清楚它的匹配顺序（类似于location指令）。
```

#### 一个server块配置多个站点
```
server {
    listen 80;
    server_name ~^(www.)?(.+)$;
    index index.php index.html;
    root /data/wwwsite/$2;
}
```