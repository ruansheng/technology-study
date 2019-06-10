### $_SERVER
```
在PHP开发中为了区分线上生产环境还是本地开发环境，如果我们能通过判断$_SERVER['RUNTIME_ENVIROMENT']为 'DEV'还是'PRO'来区分该多好，
可惜的是$_SERVER数组里面根本没有RUNTIME_ENVIROMENT这个元素。
```

###通过nginx的fastcgi_param来设置
```
location ~ \.php($|/) {
    try_files         $uri =404;
    fastcgi_pass      unix:/tmp/php-cgi.sock;
    fastcgi_index     index.php;
    include           fastcgi_params;
    fastcgi_param     SCRIPT_FILENAME  $document_root$fastcgi_script_name;
    fastcgi_param     RUNTIME_ENVIROMENT 'PRO'; # PRO or DEV
}

这里只添加了fastcgi_param RUNTIME_ENVIROMENT 'PRO'一个值

nginx -s reload
```

### 通过php主配置文件php-fpm.conf来设置
```
这个设置必须放在主配置文件php-fpm.conf里，不能放到include指令设置的子配置文件里，
否则会报错：「Array are not allowed in the global section」

直接在配置文件中添加：
env[RUNTIME_ENVIROMENT] = 'PRO'

添加后重启php-fpm
service restart php-fpm
```
