## Docker 简单使用分享

#### Docker的简单介绍
轻量级的虚拟化技术

#### 虚拟机和容器
```
虚拟机:
  占用资源多、冗余步骤多、启动慢
容器:
  启动快、资源占用少、体积小
```

#### 什么是镜像
镜像就好比是模板，镜像是用来启动容器的只读模板

#### 什么是容器
容器就是将软件打包成标准化单元，用于开发、交付、部署，容器是以镜像为基础模板来运行的一个实例

#### 镜像仓库
类似于git仓库，可以上传、下载 管理镜像
也可部署私有的镜像仓库服务，类似于github、gitlib的关系

#### Hypervisor虚拟化 与 Docker虚拟化
```
Hypervisor虚拟化:
一种运行在物理服务器和操作系统之间的中间层软件，hypervisor是所有虚拟化技术的核心;
关于Hypervisor可以阅读(https://www.ibm.com/developerworks/cn/linux/l-hypervisor/)

Docker虚拟化:
Docker是居于容器技术的轻量虚拟化，docker虚拟化技术是居于内核的Cgroup和Namespace技术，
没有Hypervisor层的开销，处理逻辑与内核深度融合，所以在很多方面，它的性能与物理机非常接近
```

#### Docker Server
Docker server也叫 Docker daemon，其功能是接收来自客户端的请求，功能涉及了容器、镜像、存储等多方面的内容

#### LXC
LXC又名Linux container，是一种虚拟化的解决方案，这种是内核级的虚拟化。(主流的解决方案Xen ,KVM, LXC)
LXC将Linux进程沙盒化，使得进程之间相互隔离，并且能够控制各进程的资源分配。

#### Namespace
主要做访问隔离，原理是对一类资源进行抽象，封装在一起提供给一个容器使用，每个容器之间是不可见的，这样就做到访问隔离

#### Cgroup
control group 控制组，主要做资源控制，原理是将一组进程放在一个控制组李，通过给控制组分配指定可用资源，达到控制一组进程可用资源的目的

#### rootfs
rootfs是根文件系统，在docker中的作用是"文件系统隔离"

#### Libcontainer 容器引擎
```
Docker是建立在Libcontainer之上的
容器的创建、删除等生命周期管理都是通过Libcontainer来实现的
容器引擎是一种驱动和管理容器生命周期的runtime工具
runC是docker的runtime工具
项目地址: https://github.com/opencontainers/runc
```

#### 容器创建的原理
容器= cgroup + namespace + rootfs + 容器引擎
```
第一步:
pid = clone(fun, stack, flags, clone_arg);
(flags: CLONE_NEWPID | CLONE_NEWNS | CLONE_NEWUSER | CLONE_NEWNET | CLONE_IPC | CLONE_NEWUTS | ...)
创建一个子进程，该进程拥有自己的Namespace，拥有自己的pid、mount、user、net、ipc、uts namespace

第二步:
echo $pid > /sys/fs/groups/cpu/tasks
echo $pid > /sys/fs/groups/cpuset/tasks
echo $pid > /sys/fs/groups/blkio/tasks
echo $pid > /sys/fs/groups/emeory/tasks
echo $pid > /sys/fs/groups/devices/tasks
echo $pid > /sys/fs/groups/freezer/tasks
将该子进程写入各个Cgroup子系统中，这样进程就可以受到相应的子系统控制

第三步:
fun()
{
  ...
  pivot_root("path_of_rootfs/", path);
  ...
  exec(/bin/bash);
  ...
}
通过pivot_root系统调用，使子进程进入一个新的rootfs，之后通过exec系统调用，在新的namespace、cgroup、rootfs中执行/bin/bash
```

#### 镜像的简单使用操作
```
查看镜像列表
docker images   // 可通过docker images --help查看子命令

删除镜像
docker rmi image_name/image_id

创建镜像
1. 从无到有创建
2. 以现有镜像为基础进行增量开发
3. 可以把容器保存为镜像

查询镜像
docker search mongo  // 默认将从官方镜像仓库查询

下载镜像
docker pull mongo
```

#### Dockerfile
Dockerfile是用来创建镜像的语法描述文件
example:
```
FROM centos
MAINTAINER ruansheng 825307513@qq.com

# set time zone
RUN /usr/bin/cp -f /usr/share/zoneinfo/Asia/Shanghai /etc/localtime

# install supervisor
RUN yum -y install epel-release
RUN yum -y install supervisor
RUN mkdir -p /var/log/supervisor
RUN sed -i "s/nodaemon=false/nodaemon=true/g" /etc/supervisord.conf

# install vim
RUN yum -y install vim git

# install tcpdump
RUN yum -y install tcpdump

# install net-tools ifconfig
RUN yum -y install net-tools

# install strace
RUN yum -y install strace

# install make
RUN yum -y install gcc gcc-c++ make m4 autoconf

## install zip unzip
RUN yum -y install zip unzip
```

#### Docker 网络
Linux平台下，Docker容器网络资源通过内核的Network Namespace机制实现隔离，不同的Network Namespace有各自的网络设备、协议栈、路由表、防火墙规则等，
同一Network Namespace下的进程共享同一网络视图，Docker提供了5种容器网络试图:
```
1. none模式，不为容器配置任何网络功能
docker run -itd --net=none ......
该模式启动的容器仅有lo环回接口，可以手动为容器配置网络

2. container模式
docker run -itd --net=container:container_id ......
改模式和一个已运行的容器共享Network Namespace

3. host模式
docker run -itd --net=host ......
该模式下运行的容器有完整的权限可以操纵主机的协议栈、路由表、防火墙，被认为是不安全的

4. bridge模式(默认的模式)
docker run -itd --net=bridge ......
或者
docker run -itd ......
在桥接模式下，Docker容器与Internet同学，以及不同容器之间的通信，都是通过iptables规则控制的
```

#### 容器卷 与 卷容器
容器内部数据在容器被删除后，数据将丢失，为了能够持久化保存数据，Docker提出了卷(volume)的概念
```
容器卷:

创建数据卷: 此时 容器中 /root/b 被映射到宿主机类似这样的文件中 /var/lib/docker/volumes/beb56816939b54f4b9699e061c6629280ec6543b2183d0cbf8cebc2ff8375357/_data
docker run -itd -v /root/b ....

挂载宿主机目录到容器中: 此时 宿主机中/tmp/a 映射到容器中 /root/b，-v 参数必须使用绝对路径，路径不存在时，docker将自动创建
/root/b如果存在将会被覆盖，
docker run -itd -v /tmp/a:/root/b ....

以只读的方式挂载一个数据卷
docker run -itd -v /tmp/a:/root/b:ro ....

docker inspect 容器名/容器ID   // 可以查看容器卷在宿主机中的挂载位置
```
```
卷容器:

创建 卷容器
docker create -v /dbdata --name dbdata image_name /bin/true

多个容器使用同一个卷容器
docker run -itd --volumes-from dbdata --name db1 image_name
docker run -itd --volumes-from dbdata --name db2 image_name
也可以使用
docker run -itd --volumes-from db1 --name db3 image_name
即便删除db1 db2 甚至初始化该数据卷的dbdata，该数据卷也不会被删除，
只有在删除最后一个使用该数据卷的容器时显式的指定docker rm -v $CONTAINER 才会删除该数据卷
```

#### Docker API
```
类似于elasticsearch一样，Docker Daemon提供的是Restful API让Docker client与之通信

如果没有特殊配置，docker会监听本机一个unix socket，默认为unix://var/run/docker.sock

测试:
echo -e "GET /images/json HTTP/1.0\r\n" | nc -U /var/run/docker.sock

配置监听端口
docker -d -H unix://var/run/docker.sock -Htcp://0.0.0.0:5678

PING接口:
curl -X GET http//localhost:5678/_ping
OK
```

#### Docker安全
```
1. Docker容器的安全性:容器是否会危害到host或者其它容器
由于容器和host共用内核，受攻击面特别大，没法确保不可能由容器入侵到host;
共用内核还可能因一个容器应用导致Linux内核崩溃，那么整个系统都会崩溃，在这方面虚拟机就相对好多了;
目前Cgroup对系统资源的限制已经比较完善，但是Namespace的隔离还不够完善，只有PID、mount、network、UTS、IPC、user这几种
对未隔离的内核资源，容器访问时可能影响到host或者其它容器
比如 procfs很多借口都没有被隔离，这也就是procfs只被只读的方式挂载
比如 syslog也没有被隔离，因此容器内部可以看到容器外进程产生的内核syslog

2. 镜像的安全性: 如何确保下载下来的镜像可信，未被篡改过
3. Docker Daemon 的安全性: 如何确保发送给daemon的命令是由可信用户发起的
```

#### 安全策略
1. 限制CPU使用率
2. 限制内存使用量
3. 限制块设备IO
4. 限制ulimit

#### 容器操作
```
创建容器
docker run -itd --net=host --name my_mysql -p 3306 -v /a:/b mysql

停止容器
docker stop my_mysql

启动容器
docker start my_mysql

重启容器
docker restart my_mysql

进入容器
docker exec -it my_mysql bash
```

