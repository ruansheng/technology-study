### sshd
```
SSH (Secure Shell) 是一种能够以安全的方式提供远程登录的协议
1.基于口令的验证---用户账户和密码来验证登录
2.基于秘钥的验证---需要本地生成秘钥对，然后把秘钥对中的公钥上传至服务器
```

### 配置文件 /etc/sshd/sshd_config
```
|配置项            | 值    |               说明  |
| ------ | ------ | ------ |
|Port           |   22              |  默认的sshd服务端口 |
|ListenAddress  | 0.0.0.0           |  舍得sshd服务器监听的IP地址 |
|Protocol       |    2              |   SSH协议的版本号 |
|HostKey       | /etc/ssh/ssh_host_key | SSH协议的版本号为1时，DES私钥存放的位置 |
|HostKey       | /etc/ssh/ssh_host_rsa_key | SSH协议的版本号为2时，RSA私钥存放的位置 |
|HostKey       | /etc/ssh/ssh_host_dsa_key | SSH协议的版本号为2时，DSA私钥存放的位置 |
|PermitRootLogin   | yes  | 设定是否允许root管理员直接登录 |
|StrictModes       | yes  | 当远程用户的私钥改变时直接拒绝连接 |
|MaxAuthTries      | 6    | 最大密码尝试次数 |
|MaxSessions       | 10   | 最大终端数 |
|PasswordAuthentication | yes |  是否允许密码验证 |
|PermitEmptyPasswords   | no  | 是否允许空密码登录(不安全) |
```

