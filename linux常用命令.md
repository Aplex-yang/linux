ifconfig 查看网卡
sudo ifconfig eth1 192.168.1.19  //设置网卡
sudo ifconfig eth1 down //关网卡
sudo ifconfig eth1 up //开网卡
查找名为a.c的文件，find -name "a.c"
在当前目录下搜含有abc字样的文件 grep "abc" * -nR

rm a.c //删除文件
rm -rf 目录 //删除目录
gcc -o hello hello.c a.c //命令行

Makefile 规则

目标 ：依赖1 依赖2  
TAB键	命令		//命令能被执行的条件 1、目标不存在 2、依赖已更新

hello : hello.c a.c
	gcc -o hello hello.c a.c
	
gcc 命令选项

gcc -o hello.o -c hello.c
-c 编译不链接 
简化前：
hello : hello.o a.o
	gcc -o hello hello.o a.o
hello.o : hello.c
	gcc -o hello.o -c hello.c
a.o	: a.c
	gcc -o a.o -c a.c
简化后：

hello : hello.o a.o
	gcc -o $@ $^
%.o : %.c
	gcc -o $@ -c $<
clean :
	rm *.o hello

linux删除目录很简单，很多人还是习惯用rmdir，不过一旦目录非空，就陷入深深的苦恼之中，现在使用rm -rf命令即可。
直接rm就可以了，不过要加两个参数-rf 即：rm -rf 目录名字
-r 就是向下递归，不管有多少级目录，一并删除
-f 就是直接强行删除，不作任何提示的意思

删除文件夹实例：
rm -rf /var/log/httpd/access
将会删除/var/log/httpd/access目录以及其下所有文件、文件夹

删除文件使用实例：
rm -f /var/log/httpd/access.log
将会强制删除/var/log/httpd/access.log这个文件



rmdir命令用来删除空目录

tab键命令：

1、只需输入文件或目录名的前几个字符，然后按TAB键，如无相重的，
完整的文件名立即自动在命令行出现；如有相重的，再按一下TAB键，
系统会列出当前目录下所有以这几个字符开头的名字。 
2. 在命令行下，只需输入例如m，再连续按两次TAB键，系统将列出所有以m开头的命令，
(包括自定义的Bshell命令函数)，对查找某些记不清楚的命令特有用。
例如输入ftp，将查到ftp，ftpcount，ftpwho，ftpshut等本不熟悉的命令。

1.压缩命令：

　　命令格式：tar  -czvf   压缩文件名.tar.gz   被压缩文件名

  可先切换到当前目录下。压缩文件名和被压缩文件名都可加入路径。

2.解压缩命令：

　　命令格式：tar  -xzvf   压缩文件名.tar.gz

　　解压缩后的文件只能放在当前的目录。
后缀为tar
    tar -xf 压缩文件名.tar

3.ls -l 命令

linux drwxr-xr-x
第一位表示文件类型。d是目录文件，l是链接文件，-是普通文件，p是管道
第2-4位表示这个文件的属主拥有的权限，r是读，w是写，x是执行。
第5-7位表示和这个文件属主所在同一个组的用户所具有的权限。
第8-10位表示其他用户所具有的权限。
如：
drwxr-xr-x   user1  group1       filename
表示filename是个目录，user1拥有读写执行的权限，和user1所在同一个group1组里的用户拥有只读和执行权限，
剩下其他用户拥有只读和执行权限。
4.wget命令用于使用命令行下载网络文件，格式为：“wget[参数] 下载地址”
参数 作用
-b   后台下载模式
-O   下载到指定目录
-t   最大尝试次数

5.ifconfig 用于获取网卡配置与网络状态等信息
6.echo命令用于在终端显示字符串或变量
7.uname 命令用于查看系统内核版本等信息，“uname [-a]”
8.cd !$ 代表上一条命令的参数
9.touch 命令用于创建空白文件与修改文件时间
10.mkdir 用于创建空白的文件夹
11.echo 命令用于向窗口输出文本
12. sudo i 进入root 最高权限
13. CTRL+D 最高root 最高权限
14. ./为运行文件的操作，被运行的文件名为go
15. sudo find / -name 文件名
16.设置好共享名后，进入Ubuntu系统，打开终端，先执行命令，在挂载点目录添加“bdshare”目录，
接着执行"mount -t vboxsf BaiduShare /mnt/bdshare/",就能完成共享文件夹的设置。

17.cat /etc/issue 或者 sudo lsb_release -a 可以查看当前正在运行的 Ubuntu 的版本号
18 uname -r 查看内核版本号
19.tar xjf xxxx.tar.bz2
20.tar xzf xxxx.tar.gz

21. init-y := init/
init-y := $(patsubst %/, %/built-in.o, $(init-y))
分析：第二句是一个makefile的函数，%/就代表init/，
整句的意思：init-y等于init/目录下所有的文件，
这些所有的文件会被编译为一个built-in.o文件。
22.1·1111
## 一、文件名称
