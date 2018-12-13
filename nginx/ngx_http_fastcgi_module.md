### ngx_http_fastcgi_module模块
默认就加载的模块，允许将请求传递给另一台服务器，转发请求到FastCGI服务器

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

#### fastcgi_param
```
设置传递给FastCGI服务器的参数值，可以是文本，变量或组合
可用位置：http, server, location

location ~* \.php$ {
    fastcgi_pass #后端fpm服务器IP:9000;
    fastcgi_index index.php;
    fastcgi_param SCRIPT_FILENAME  /usr/share/nginx/html$fastcgi_script_name;
    include fastcgi_params;
    ...
}
```

#### include
```
引入另一个文件内容
location ~* \.php$ {
    ...
    include fastcgi_params;
    ...
}

fastcgi_params文件内容如下:
fastcgi_param  QUERY_STRING       $query_string;
fastcgi_param  REQUEST_METHOD     $request_method;
fastcgi_param  CONTENT_TYPE       $content_type;
fastcgi_param  CONTENT_LENGTH     $content_length;

fastcgi_param  SCRIPT_NAME        $fastcgi_script_name;
fastcgi_param  REQUEST_URI        $request_uri;
fastcgi_param  DOCUMENT_URI       $document_uri;
fastcgi_param  DOCUMENT_ROOT      $document_root;
fastcgi_param  SERVER_PROTOCOL    $server_protocol;
fastcgi_param  REQUEST_SCHEME     $scheme;
fastcgi_param  HTTPS              $https if_not_empty;

fastcgi_param  GATEWAY_INTERFACE  CGI/1.1;
fastcgi_param  SERVER_SOFTWARE    nginx/$nginx_version;

fastcgi_param  REMOTE_ADDR        $remote_addr;
fastcgi_param  REMOTE_PORT        $remote_port;
fastcgi_param  SERVER_ADDR        $server_addr;
fastcgi_param  SERVER_PORT        $server_port;
fastcgi_param  SERVER_NAME        $server_name;

# PHP only, required if PHP was built with --enable-force-cgi-redirect
fastcgi_param  REDIRECT_STATUS    200;
```