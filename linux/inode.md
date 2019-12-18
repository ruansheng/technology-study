## inode
```
每个文件都会有inode号码，相当于mysql 数据库里的表id，用来查找数据的。每个文件名都会对应一个inode号码，我们打开一个文件的时候实际经历了以下步骤：
1.通过文件名去找对应的inode号码
2.通过inode号码获取inode信息
3.通过inode信息查找数据所在的block，读取数据
```

### inode内容
```
inode文件元信息包含以下内容:
1.文件的字节数
2.文件拥有者的User ID
3.文件的Group ID
4.文件的读、写、执行权限
5.文件的时间戳，共有三个：ctime指inode上一次变动的时间，mtime指文件内容上一次变动的时间，atime指文件上一次打开的时间。
6.链接数，即有多少文件名指向这个inode
7.文件数据block的位置
```

### 查inode信息
```
查文件inode信息
#stat mbox
16777220 10266133 -rw------- 1 ruansheng staff 0 1678 "Jun 26 09:40:31 2019" "Mar 21 19:00:46 2019" "Mar 21 19:00:46 2019" "Mar 21 19:00:46 2019" 4194304 8 0 mbox

查看硬盘的inode总数和已经使用数量
#df -i
Filesystem    512-blocks      Used Available Capacity iused               ifree %iused  Mounted on
/dev/disk1s1   489620264 248829856 222677840    53% 1733863 9223372036853041944    0%   /
devfs                676       676         0   100%    1170                   0  100%   /dev
/dev/disk1s4   489620264  16777472 222677840     8%       8 9223372036854775799    0%   /private/var/vm
map -hosts             0         0         0   100%       0                   0  100%   /net
map auto_home          0         0         0   100%       0                   0  100%   /home

查看文件/目录的inode号码
#ls -i mbox
10266133 mbox
```

### 特殊作用
```
由于inode号码与文件名分离，这种机制导致了一些Unix/Linux系统特有的现象。
1. 有时，文件名包含特殊字符，无法正常删除。这时，直接删除inode节点，就能起到删除文件的作用。
2. 移动文件或重命名文件，只是改变文件名，不影响inode号码。
3. 打开一个文件以后，系统就以inode号码来识别这个文件，不再考虑文件名。因此，通常来说，系统无法从inode号码得知文件名。
```
