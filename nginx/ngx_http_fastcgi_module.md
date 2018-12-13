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

#### fastcgi_cache_path
```
语法：fastcgi_cache_path path [levels=m:n] keys_zone=name:size [inactive=time] [max_size=size]
默认值：none
使用字段：http
clean_time参数在0.7.45版本中已经移除。
这个指令指定FastCGI缓存的路径以及其他的一些参数，所有的数据以文件的形式存储，缓存的关键字(key)和文件名为代理的url计算出的MD5值。
Level参数设置缓存目录的目录分级以及子目录的数量，例如指令如果设置为：

fastcgi_cache_path  /data/nginx/cache  levels=1:2   keys_zone=one:10m;
那么数据文件将存储为：
/data/nginx/cache/c/29/b7f54b2df7773722d382f4809d65029c

缓存中的文件首先被写入一个临时文件并且随后被移动到缓存目录的最后位置，0.8.9版本之后可以将临时文件和缓存文件存储在不同的文件系统，但是需要明白这种移动并不是简单的原子重命名系统调用，而是整个文件的拷贝，所以最好在fastcgi_temp_path和fastcgi_cache_path的值中使用相同的文件系统。
另外，所有活动的关键字及数据相关信息都存储于共享内存池，这个值的名称和大小通过key_zone参数指定，inactive参数指定了内存中的数据存储时间，默认为10分钟。
max_size参数设置缓存的最大值，一个指定的cache manager进程将周期性的删除旧的缓存数据
```