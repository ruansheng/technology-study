### 基础环境
```
安装mvn:
  mvn是java中用来管理包依赖关系的一个工具

  wget http://repos.fedorapeople.org/repos/dchen/apache-maven/epel-apache-maven.repo -O /etc/yum.repos.d/epel-apache-maven.repo
  yum -y install apache-maven
```

### 安装zkui
```
zkui是java写的一个开源系统，源码在github上面  https://github.com/DeemOpen/zkui
    1.下载zkui源码
    2.用mvn编译打包成一个jar文件  mvn clean install
    3.将config.cfg复制到上一步生成的jar文件所在目录
    4.修改配置文件 zkServer=localhost:2181
    5.启动服务  java -jar zkui-2.0-SNAPSHOT-jar-with-dependencies.jar， 可用nohup启动
    6.测试，访问 http://localhost:9090，如果看到界面则表示启动成功
    7.默认的账号密码  username:admin      password:manager
```