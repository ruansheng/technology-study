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

#### fastcgi_bind
```
语法：fastcgi_bind address
默认值：none
使用字段：http, server, location
可用版本：大于0.8.22
示例：
fastcgi_bind  192.168.1.1;
指令在调用connect()函数之前将解析每个上游socket到一个本地地址，可以使用在主机拥有多个网卡接口或别名，但是你只允许到外的连接来自指定的网卡或者地址的情况下。
```

#### fastcgi_buffer_size
```
语法：fastcgi_buffer_size the_size ;
默认值：fastcgi_buffer_size 4k/8k ;
使用字段：http, server, location
这个参数指定将用多大的缓冲区来读取从FastCGI进程到来应答头。
默认的缓冲区大小为fastcgi_buffers指令中的每块大小，可以将这个值设置更小
```

#### fastcgi_buffers
```
语法：fastcgi_buffers the_number is_size;
默认值：fastcgi_buffers 8 4k/8k;
使用字段：http, server, location
这个参数指定了从FastCGI进程到来的应答，本地将用多少和多大的缓冲区读取。
fastcgi_buffers可以设置为你的FastCGI返回的大部分应答大小，这样可以处理大部分的请求，较大的请求将被缓冲到磁盘。
如果想关闭对所有请求道磁盘的缓冲，可以将fastcgi_max_temp_file_size设置为0。
fastcgi_buffer等于：fastcgi_buffer_size + the_number * is_size
如果一个fastcgi应答为12k，那么fastcgi_buffers 64 4k将分配3个4k的buffer。这便是为什么fastcgi_buffers有两个参数，而fastcgi_buffer_size（用于应答头与应答的第一部分）只有1个参数，在fastcgi_buffer_size被清空后，它们和fastcgi_buffers一起被使用。
例如：

fastcgi_buffers 256 4k; #设置buffer大小为：4k + 256 * 4k = 1028k
这意味着大于1M的应答将被缓冲到磁盘，而小于1M的将在内存中处理。
默认这个参数等于分页大小，根据环境的不同可能是4K, 8K或16K。
在linux系统中你可以通过下面的命令得到分页大小：

getconf PAGESIZE
返回的单位为bytes。
指令示例:

fastcgi_buffers 256 4k; # 设置缓冲区大小为4k + 256 * 4k = 1028k
这意味着所有FastCGI返回的应答，nginx将超过1M的部分写入磁盘，1M以内的部分写入内存
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

#### fastcgi_temp_path
```
语法：fastcgi_temp_path path [level1 [level2 [level3]]]
默认值：fastcgi_temp_path fastcgi_temp
使用字段：http, server, location
指令指定存储从别的服务器传送来的数据临时文件路径，同样可以指定三级目录已经哈希存储，level的值指定为哈希设置多少标记，例如，在下列配置中：

fastcgi_temp_path  /spool/nginx/fastcgi_temp 1 2;
临时文件类似如下：

/spool/nginx/fastcgi_temp/7/45/00000123457
```

#### fastcgi_cache
```
语法：fastcgi_cache zone_name|off;
默认值：off
使用字段：http, server, location
为缓存实际使用的共享内存指定一个区域，相同的区域可以用在不同的地方

zone_name的值为fastcgi_cache_path指令创建的缓存名称
hash(zone_name) 作为缓存文件名
```

#### fastcgi_cache_key
```
语法：fastcgi_cache_key line
默认值：none
使用字段：http, server, location
设置缓存的关键字，如：

fastcgi_cache_key localhost:9000$request_uri;
```

#### fastcgi_cache_methods
```
在缓存FastCGI请求中允许哪些http方法。
语法：fastcgi_cache_methods [GET HEAD POST];
默认值：fastcgi_cache_methods GET HEAD;
使用字段：main,http,location
这个指令指定在缓存FastCGI请求中允许哪些http方法。
无法禁用GET/HEAD ，即使你只是这样设置：

fastcgi_cache_methods  POST; #GET和HEAD依然可用
```

#### fastcgi_cache_min_uses
```
语法：fastcgi_cache_min_uses n
默认值：fastcgi_cache_min_uses 1
使用字段：http, server, location
指令指定了经过多少次请求的相同URL将被缓存
```

#### fastcgi_cache_use_stale
```
语法：fastcgi_cache_use_stale [updating|error|timeout|invalid_header|http_500]
默认值：fastcgi_cache_use_stale off;
使用字段：http, server, location
在某些网关错误、超时的情况下，nginx都将传送过期的缓存数据
```

#### fastcgi_cache_valid
```
语法：fastcgi_cache_valid [http_error_code|time]
默认值：none
使用字段：http, server, location
为指定的http返回代码指定缓存时间，例如：

fastcgi_cache_valid  200 302  10m;
fastcgi_cache_valid  404      1m;

将响应状态码为200和302缓存10分钟，404缓存1分钟。
默认情况下缓存只处理200，301，302的状态。
同样也可以在指令中使用any表示任何一个。

fastcgi_cache_valid  200 302 10m;
fastcgi_cache_valid  301 1h;
fastcgi_cache_valid  any 1m;
```

#### fastcgi_connect_timeout
```
语法：fastcgi_connect_timeout time
默认值：fastcgi_connect_timeout 60
使用字段：http, server, location
指定同FastCGI服务器的连接超时时间，这个值不能超过75秒
```

#### fastcgi_hide_header
```
语法：fastcgi_hide_header name
使用字段：http, server, location
默认情况下nginx不会将来自FastCGI服务器的”Status”和”X-Accel-…“头传送到客户端，这个参数也可以隐藏某些其它的头。
如果必须传递”Status”和”X-Accel-…“头，则必须使用fastcgi_pass_header强制其传送到客户端
```

#### fastcgi_pass_header
```

```

#### fastcgi_ignore_client_abort
```
语法：fastcgi_ignore_client_abort on|off
默认值：fastcgi_ignore_client_abort off
使用字段：http, server, location
如果当前连接请求FastCGI服务器失败，为防止其与nginx服务器断开连接，可以用这个指令
```

#### fastcgi_ignore_headers
```
语法：fastcgi_ignore_headers name [name…]
使用字段：http, server, location
这个指令禁止处理一些FastCGI服务器应答的头部字段，比如可以指定像”X-Accel-Redirect”, “X-Accel-Expires”, “Expires”或”Cache-Control”等。
```

#### fastcgi_intercept_errors
```
语法：fastcgi_intercept_errors on|off
默认值：fastcgi_intercept_errors off
使用字段：http, server, location
这个指令指定是否传递4xx和5xx错误信息到客户端，或者允许nginx使用error_page处理错误信息。
你必须明确的在error_page中指定处理方法使这个参数有效，正如Igor所说“如果没有适当的处理方法，nginx不会拦截一个错误，这个错误不会显示自己的默认页面，这里允许通过某些方法拦截错误
```

#### fastcgi_max_temp_file_size
```

```

#### fastcgi_no_cache
```

```

#### fastcgi_next_upstream
```
语法：fastcgi_next_upstream error|timeout|invalid_header|http_500|http_503|http_404|off
默认值：fastcgi_next_upstream error timeout
使用字段：http, server, location
指令指定哪种情况请求将被转发到下一个FastCGI服务器：

error — 传送中的请求或者正在读取应答头的请求在连接服务器的时候发生错误。
timeout — 传送中的请求或者正在读取应答头的请求在连接服务器的时候超时。
invalid_header — 服务器返回空的或者无效的应答。
http_500 — 服务器返回500应答代码。
http_503 — 服务器返回503应答代码。
http_404 — 服务器返回404应答代码。
off — 禁止请求传送到下一个FastCGI服务器。
注意传送请求在传送到下一个服务器之前可能已经将空的数据传送到了客户端，所以，如果在数据传送中有错误或者超时发生，这个指令可能无法修复一些传送错误
```

#### fastcgi_read_timeout
```
语法：fastcgi_read_timeout time
默认值：fastcgi_read_timeout 60
使用字段：http, server, location
前端FastCGI服务器的响应超时时间，如果有一些直到它们运行完才有输出的长时间运行的FastCGI进程，或者在错误日志中出现前端服务器响应超时错误，可能需要调整这个值
```

#### fastcgi_redirect_errors
```

```

#### fastcgi_send_timeout
```
语法：fastcgi_send_timeout time
默认值：fastcgi_send_timeout 60
使用字段：http, server, location
指令为上游服务器设置等待一个FastCGI进程的传送数据时间，如果有一些直到它们运行完才有输出的长时间运行的FastCGI进程，那么可以修改这个值，如果你在上游服务器的error log里面发现一些超时错误，那么可以恰当的增加这个值。
指令指定请求服务器的超时时间，指完成了2次握手的连接，而不是完整的连接，如果在这期间客户端没有进行数据传递，那么服务器将关闭这个连接。
```

#### fastcgi_split_path_info
```

```

#### fastcgi_store
```

```

#### fastcgi_store_access
```
语法：fastcgi_store_access users:permissions [users:permission …]
默认值：fastcgi_store_access user:rw
使用字段：http, server, location
这个参数指定创建文件或目录的权限，例如：

fastcgi_store_access  user:rw  group:rw  all:r;
如果要指定一个组的人的相关权限，可以不写用户，如：

fastcgi_store_access  group:rw  all:r;
```



