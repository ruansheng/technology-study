###初衷
日常开发中看nginx日志比较麻烦，需要登录到nginx机器上，用tail -f 查看，如果能够收集到ELK里面，就不用每次登录到机器上面了

前期调研过收集日志的组件，常用的logstash和filebeat，logstash是依赖于jvm的，比较占用内存，网上看了评论说filebeat占用内存比较少，并且很高效



###安装filebeat
filebeat是kibana官方开发的一个组件，在kibanba的官网可以找到下载地址

https://www.elastic.co/downloads/beats/filebeat

看平台而下载，我下载的是linux 64bit tar.gz

下载完成解压即可:

drwxr-x--- 2 root root 4096 Nov 6 18:56 data
-rw-r--r-- 1 root root 68549 Sep 26 20:41 fields.yml
-rwxr-xr-x 1 root root 33903123 Sep 26 20:43 filebeat
-rw-r--r-- 1 root root 66260 Sep 26 20:41 filebeat.reference.yml
-rw------- 1 root root 7231 Sep 26 20:41 filebeat.yml
drwxr-xr-x 4 root root 4096 Sep 26 20:41 kibana
-rw-r--r-- 1 root root 13675 Sep 26 20:39 LICENSE.txt
drwxr-xr-x 18 root root 4096 Sep 26 20:41 module
drwxr-xr-x 2 root root 4096 Sep 26 20:41 modules.d
-rw-r--r-- 1 root root 148778 Sep 26 20:39 NOTICE.txt
-rw-r--r-- 1 root root 802 Sep 26 20:43 README.md

其中上面的filebeat就是二进制可执行文件

###配置文件
filebeat.inputs:
- paths:
- /usr/local/nginx/logs/access.log
tail_files: true
fields:
topic: bp_api_nginx_access_log
- paths:
- /usr/local/nginx/logs/error.log
tail_files: true
fields:
topic: bp_api_nginx_error_log

output.kafka:
hosts: ["xxx.xxx.xxx.xxx:9092", "xxx.xxx.xxx.xxx:9092", "xxx.xxx.xxx.xxx:9092"]
topic: "%{[fields][topic]}"



###启动fliebeat
nohup /usr/local/filebeat/filebeat -e -c bp_api_filebeat.yml >/dev/null 2>&1 &

这样日志就被实时收集到kafka中了

