## jenkins安装
Jenkins是一个开源软件项目，旨在提供一个开放易用的软件平台，使软件的持续集成变成可能
Jenkins是基于Java开发的一种持续集成工具，用于监控持续重复的工作

## jenkins安装方法一
```
官网下载jar包：http://mirrors.jenkins.io/war-stable/latest/jenkins.war
启动命令: java -jar jenkins.war
访问地址: http://localhost:8080
```

## jenkins安装方法二
```
官网下载jar包：http://mirrors.jenkins.io/war-stable/latest/jenkins.war
将jenkins.war文件放入tomcat下的webapps目录下
访问地址: http://localhost:8080/jenkins
```

## 配置jenkins
1. 重置密码
根据路径提示找到密码，输入到框里
/Users/ruansheng/.jenkins/secrets/initialAdminPassword

2. 创建用户
输入用户名、密码、确认密码、全名


