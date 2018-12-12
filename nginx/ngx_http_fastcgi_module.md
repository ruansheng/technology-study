### ngx_http_fastcgi_module模块
允许将请求传递给另一台服务器，转发请求到FastCGI服务器

#### fastcgi_pass
```
语法：fastcgi_pass fastcgi-server
默认值：none
使用字段：http, server, location
指定FastCGI服务器监听端口与地址，可以是本机或者其它：
fastcgi_pass   localhost:9000;
使用Unix socket:
fastcgi_pass   unix:/tmp/fastcgi.socket;

同样可以使用一个upstream字段名称：

upstream backend  {
  server   localhost:1234;
}

fastcgi_pass   backend;
```

#### fastcgi_index
```
语法：fastcgi_index file
默认值：none
使用字段：http, server, location

如果URI以斜线/结尾，文件名将追加到URI后面，这个值将存储在变量$fastcgi_script_name中。例如：
fastcgi_index  index.php;
fastcgi_param  SCRIPT_FILENAME  /home/www/scripts/php$fastcgi_script_name;
请求"/page.php"，参数SCRIPT_FILENAME将被设置为"/home/www/scripts/php/page.php"，
但是请求"/"则为"/home/www/scripts/php/index.php"

如果配置经常是
location ~* \.php$ {
    fastcgi_pass    cos_ups;
    fastcgi_index   index.php;
    include         fastcgi.conf;
}
在这里指定fastcgi_index  其实是没有用的，因为前面的location ~* \.php$已经把“/结束”这种情况排除了
```