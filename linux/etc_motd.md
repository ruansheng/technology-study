### /etc/motd
```
这个文件是在你登录之后显示的，不管你是 TTY 还是 PTS 登录，也不管是 Telnet 或 SSH 都显示这个文件里面的信息。

在较新的Linux发行版中，这个功能被扩展了，有了动态motd和静态motd的区别，在Ubuntu 16.04.01 LTS中，仅仅启用了动态motd，而未启用静态motd

修改/etc/motd内容，在登录之后会显示出/etc/motd文件中的内容
```