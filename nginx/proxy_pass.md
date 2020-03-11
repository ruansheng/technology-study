## proxy_pass后面的url的加与不加/的区别的几种情况
```
location /static{
    proxy_pass  192.168.2.321:81;
    #跳转到
    #http://host:port/static -> http://192.168.2.321:81/static/
    #http://host:port/static/ -> http://192.168.2.321:81/static/
    #http://host:port/static/1.html -> http://192.168.2.321:81/static/1.html
    #http://host:port/static/1.html?a=b -> http://192.168.2.321:81/static/1.html?a=b
}

location /static{
    proxy_pass  192.168.2.321:81/;
    #跳转到
    #http://host:port/static -> http://192.168.2.321:81//
    #http://host:port/static/ -> http://192.168.2.321:81//
    #http://host:port/static/1.html -> http://192.168.2.321:81//1.html
    #http://host:port/static/1.html?a=b -> http://192.168.2.321:81//1.html?a=b
}

location /static/{
    proxy_pass  192.168.2.321:81;
    #跳转到
    #http://host:port/static -> http://192.168.2.321:81/static/
    #http://host:port/static/ -> http://192.168.2.321:81/static/
    #http://host:port/static/1.html -> http://192.168.2.321:81/static/1.html
    #http://host:port/static/1.html?a=b -> http://192.168.2.321:81/static/1.html?a=b
}

location /static/{
    proxy_pass  192.168.2.321:81/;
    #跳转到
    #http://host:port/static -> http://192.168.2.321:81/
    #http://host:port/static/ -> http://192.168.2.321:81/
    #http://host:port/static/1.html -> http://192.168.2.321:81/1.html
    #http://host:port/static/1.html?a=b -> http://192.168.2.321:81/1.html?a=b
}
```
