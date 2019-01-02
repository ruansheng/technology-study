### SELinux
```
Security Enhanced Linux 安全强化的Linux，由美国国家安全局(NSA)开发的
SeLinux是在进程程序、文件等权限设置依据的一个内核模块，能够控制网络服务到否访问系统资源的一道关卡
```

### getenforce setenforce
```
#getenforce   查看当前系统SELinux的模式
#setenforce [0|1] 将SELinux在Enforcing 与 permissive模式之间切换，无法再Disable模式下面进行切换
```