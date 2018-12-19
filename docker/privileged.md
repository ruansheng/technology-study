### privileged
```
配置项目服务器，调用了systemctl命令启动服务，报错
# systemctl start squid
Failed to get D-Bus connection: Operation not permitted

这个的原因是因为dbus-daemon没能启动。其实systemctl并不是不可以使用。
将你的CMD或者entrypoint设置为/usr/sbin/init即可。会自动将dbus等服务启动起来。
然后就可以使用systemctl了。命令如下：
docker run --privileged ...
```