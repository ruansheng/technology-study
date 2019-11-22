## packetdrill(网络协议测试工具)
```
packetdrill是一个非常有用的用于测试网络协议栈的工具，由Google开发，它常用于对网络协议栈进行回归测试，
确保新的功能不会影响原有功能。它支持Linux, FreeBSD, OpenBSD与NetBSD内核。它使用脚本化的语言编写测试语句，预测协议栈输出。
```

### 安装
```
git clone https://github.com/google/packetdrill.git

编译:
make

报错:
make: bison: Command not found
make: *** [parser.o] Error 127

解决:
yum  install -y   bison  bison-devel

报错:
make: flex: Command not found
make: *** [lexer.o] Error 127

解决:
yum -y install flex

报错:
collect2: error: ld returned 1 exit status
make: *** [packetdrill] Error 1

解决:
修改Makefile，去掉第一行末尾的 - static

编译成功

执行用例:
./packetdrill ./tests/linux/packetdrill/socket_err.pkt
./packetdrill ./tests/linux/packetdrill/socket_wrong_err.pkt
./packetdrill  tests/linux/fast_retransmit/fr-4pkt-sack-linux.pkt
```
