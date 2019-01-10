### /sbin/nologin
```
/sbin/nologin 是一个特殊的shell
一些系统账号不需要登录，就给设置的/sbin/nologin
```

### /sbin/nologin 账号登录提示
```
当被设置/sbin/nologin shell的账号登录时会提示:This account is currently not available

# su - ruansheng
上一次登录：四 1月 10 17:26:31 CST 2019pts/0 上
This account is currently not available.

可以替换这个文案，在 /etc/nologin.txt 文件内写入想要提示的内容就不会使用默认的内容了

# vi /etc/nologin.txt
can't login

再次切换登录
# su - ruansheng
上一次登录：四 1月 10 18:03:42 CST 2019pts/0 上
can't login
```