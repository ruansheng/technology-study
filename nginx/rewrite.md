### rewrite
```
在server块下，会优先执行rewrite部分，然后才会去匹配location块
server中的rewrite break和last没什么区别，都会去匹配location，所以没必要用last再发起新的请求，可以留空

location中的rewirte：
不写last和break - 那么流程就是依次执行这些rewrite
1. rewrite break
　　url重写后，直接使用当前资源，不再执行location里余下的语句，完成本次请求，地址栏url不变

2. rewrite last
　　url重写后，马上发起一个新的请求，再次进入server块，重试location匹配，超过10次匹配不到报500错误，地址栏url不变

3. rewrite redirect
　　返回302临时重定向，地址栏显示重定向后的url，爬虫不会更新url（因为是临时）

4. rewrite permanent
　　返回301永久重定向, 地址栏显示重定向后的url，爬虫更新url

如果location中rewrite后是对静态资源的请求，不需要再进行其他匹配，一般要使用break或不写，直接使用当前location中的数据源，完成本次请求
如果location中rewrite后，还需要进行其他处理，如动态fastcgi请求(.PHP,.jsp)等，要用last继续发起新的请求
(根的location使用last比较好, 因为如果有.php等fastcgi请求还要继续处理)
location / {
	index index.php index.html index.htm;
 	rewrite ^/(.*) /index_api.php/$1 last;
}

如果是直接请求某个真实存在的文件,则用break语句停止rewrite检查
if (-f $request_filename) {
    break;
}
```

#### break
```
server{
    ...
    rewrite ^(.*)$ /2.html break;
    ...
}
不再执行余下的语句，完成本次请求，地址栏url不变(只有一个请求)

server{
    ...
    rewrite ^(.*)$ http://www.baidu.com last;
    ...
}
客户端请求，url重写后，响应302给客户端，客户端发起一个新的请求
```

#### last
```
server{
    ...
    rewrite ^(.*)$ /2.html last;
    ...
}
不再执行余下的语句，完成本次请求，地址栏url不变(只有一个请求)

server{
    ...
    rewrite ^(.*)$ http://www.baidu.com last;
    ...
}

客户端请求，url重写后，响应302给客户端，客户端发起一个新的请求
```

#### redirect
```
server{
    ...
    rewrite ^(.*)$ /2.html redirect;
    ...
}
返回302临时重定向，地址栏显示重定向后的url，配置不当可能出现死循环重定向
```

#### permanent
```
server{
    ...
    rewrite ^(.*)$ /2.html permanent;
    ...
}
返回301永久重定向, 地址栏显示重定向后的url
```

