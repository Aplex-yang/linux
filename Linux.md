用Linux 下的dnw
1. 把linux下的dnw应用程序放到/bin目录下
再执行 sudo chmod +x /bin/dnw
sudo chmod +s /bin/dnw
2.如果使用vmware，让vmware位于前台，然后再用USB线连接PC和开发板USB DEVICE 接口
用lsusb命令确认VMWARE LINUX已经识别出UBOOT的USB设备

下载程序：

/bin/dnw ~/u-boot-1.1.6/u-boot.bin

使用3.4.5的编译器
tar xjf arm-linux-gcc-3.4.5-glibc-2.3.6.tar.bz2
设置PATH,进入bin文件夹内
1、sudo vim /etc/environment 

2、PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin"
3、export PATH = /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin


sudo命令用来以其他身份来执行命令，预设的身份为root

0、解压缩
1、打补丁  patch -p？（1） < ..补丁文件（/u-boot-1.1.6_jz2440.patch）  //忽略掉斜杠的文件名补丁
2、配置  make 100ask24x0_config   ///----mkconfig 100ask24x0 arm arm920t 100ask24x0 NULL s3c24x0
3、编译  make

1、读flash+写flash
2、初始化SDRAM
3、启动内核


u-boot 功能：启动内核

1、从flash中读出内核  2、初始化SDRAM 3、启动内核

读到SDRAM里面去，所以就要初始化SDRAM、初始化时钟，关看门狗//硬件初始化  开发功能  烧写flash ---网卡 USB 串口 为开发方便增加的功能 是一个单片机程序

分析配置过程：

@$

$@ 表示规则的目标文件名
例 

$(@:_config=)   100ask24x0


ln –s 源文件 目标文件

>  ---新建一个文件
>>  --把内容追加进去

分析编译过程：

编译过程：

OBJS  = cpu/$(CPU)/start.o    $(CPU)=arm920t
OBJS  = cpu/arm920t/start.o

LIBS  = lib_generic/libgeneric.a
LIBS += board/$(BOARDDIR)/lib$(BOARD).a
LIBS += cpu/$(CPU)/lib$(CPU).a


$(obj)u-boot:		depend version $(SUBDIRS) $(OBJS) $(LIBS) $(LDSCRIPT)
		
UNDEF_SYM=`$(OBJDUMP) -x $(LIBS) |sed  -n -e 's/.*\(__u_boot_cmd_.*\)/-u\1/p'|sort|uniq`;\
		
cd $(LNDIR) && $(LD) $(LDFLAGS) $$UNDEF_SYM $(__OBJS) \
			
--start-group $(__LIBS) --end-group $(PLATFORM_LIBS) \
			
-Map u-boot.map -o u-boot

UNDEF_SYM=`arm-linux-objdump -x lib_generic/libgeneric.a board/100ask24x0/lib100ask24x0.a cpu/arm920t/libarm920t.a 
cpu/arm920t/s3c24x0/libs3c24x0.a lib_arm/libarm.a fs/cramfs/libcramfs.a fs/fat/libfat.a fs/fdos/libfdos.a fs/jffs2/libjffs2.a 
fs/reiserfs/libreiserfs.a fs/ext2/libext2fs.a net/libnet.a 
disk/libdisk.a rtc/librtc.a dtt/libdtt.a drivers/libdrivers.a drivers/nand/libnand.a 
drivers/nand_legacy/libnand_legacy.a drivers/usb/libusb.a drivers/sk98lin/libsk98lin.a 
common/libcommon.a |sed  -n -e 's/.*\(__u_boot_cmd_.*\)/-u\1/p'|sort|uniq`;\

cd /home/book/u-boot-1.1.6 && arm-linux-ld -Bstatic -T /home/book/u-boot-1.1.6/board/100ask24x0/u-boot.lds 
-Ttext 0x33F80000  

$UNDEF_SYM cpu/arm920t/start.o 
\--start-group lib_generic/libgeneric.a board/100ask24x0/lib100ask24x0.a 
cpu/arm920t/libarm920t.a cpu/arm920t/s3c24x0/libs3c24x0.a lib_arm/libarm.a 
fs/cramfs/libcramfs.a fs/fat/libfat.a fs/fdos/libfdos.a fs/jffs2/libjffs2.a 
fs/reiserfs/libreiserfs.a fs/ext2/libext2fs.a net/libnet.a disk/libdisk.a 
rtc/librtc.a dtt/libdtt.a drivers/libdrivers.a drivers/nand/libnand.a 
drivers/nand_legacy/libnand_legacy.a drivers/usb/libusb.a drivers/sk98lin/libsk98lin.a 
common/libcommon.a --end-group -L /home/book/gcc-3.4.5-glibc-2.3.6/bin/../lib/gcc/arm-linux/3.4.5 -lgcc \
			-Map u-boot.map -o u-boot


分析编译 make 
1、第1个文件 cpu/arm920t/start.s
2、链接地址  0x33f80000+board/100ask24x0/u-boot.lds

//board/10024x0/config.mk

栈设置好之后才可以调用C函数

第一阶段：a、设SVC模式 b、关看门狗 c、屏蔽中断  d、初始化SDRAM e、设置栈 f、时钟 g、代码 flash--->SDRAM h、清BSS段 

第二阶段：i、调用C函数 _start_armboot

bootcmd=nand read.jffs2 0x30007FC0 kernel; bootm 0x30007FC0


kernel---分区

从NAND读出内核：从那里读 ，从kernel分区(linux没有分区表，关心分区地址)
read.jffs2 0x30007FC0 kernel

启动：bootm 0x30007FC0



u-boot的核心就是  命令---内核的启动流程及启动方式

s=getenv("bootcmd")

u-boot 从flash上读出内核 ：uImage ----头部+真正的内核 加载地址 入口地址

设置启动参数 才能跳到入口地址启动内核 

启动 do_bootm_linux


启动内核 1、设置启动参数-----在某个地址按某种格式保存数据0x30000100  2、跳到入口地址 ----thekernel

机器ID 内核识别支持的单板,通过 机器ID来区分

u-boot 启动内核 	theKernel (0, bd->bi_arch_number, bd->bi_boot_params);
 
bd->bi_arch_number--机器ID 
bd->bi_boot_params--那些参数所存放的地址

sudo vim /etc/environ

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin


下载程序：
/bin/dnw ~/u-boot-1.1.6/u-boot.bin



1、打补丁  patch -p? < ..补丁文件
2、配置  1 使用默认配置，在上面修改，在arh/arm/configs找到相似的配置文件xxx_defconfig  make xxx_defconfig ---保存到.config  
                                                                                       make menuconfig   ----去读取.config

（怎么去找，使用命令 find -name "defconfig" * -nR）
         2 使用厂家提供的配置文件  cp config_厂家  .config  make menuconfig  
不知道配置的情况下 用命令查找 find -name "defconfig"

配置分析：
配置结果生成： 生成.config
配置项： CONFIG_DM9000 = y/m
1、C源码： CONFIG_DM9000
2、Makefile   drivers/net/Makefile obj-$(CONFIG_DM9000)+= dm9000.o    //子目录Makefile $(CONFIG_DM9000)表示一个变量
3、include/config/auto.conf
4、include/linux/autoconf.h //不论CONFIG_DM9000 = y/m 都会设置为1

//linux-2.6.22.6/Documentation/kbuild/makefile.txt 

a.c b.c 

obj - y += a.o b.o  

obj-m += ab.obj
ab - objs := a.o b.o

a.c b.c ----> a.o b.o ---> ab.ko  

3、编译 


内核子目录Makefile 

obj_y+=xxx.o   --会被编译进去内核 
obj_m+=yyy.o   --会被编译成可加载模块 yyy.ko



分析 Makefile :第1个文件---arch/arm/kernel/head.o 、链接脚本---arch/arm/kernel/vmlinux.lds

make uImage时  1、.config被创建生成autoconf.h  .config 被创建生成auto.conf---被顶层makefile包含

3、编译 


zImage Image xipImage bootpImage uImage: vmlinux  ---uImage 依赖于vmlinux

vmlinux: $(vmlinux-lds) $(vmlinux-init) $(vmlinux-main) $(kallsyms.o) FORCE

vmlinux-init := $(head-y) $(init-y)

head-y		:= arch/arm/kernel/head$(MMUEXT).o arch/arm/kernel/init_task.o

init-y		:= init/
init-y		:= $(patsubst %/, %/built-in.o, $(init-y))=init/built-in.o

vmlinux-main := $(core-y) $(libs-y) $(drivers-y) $(net-y)

core-y		:= usr/vmlinux-all  := $(vmlinux-init) $(vmlinux-main)

core-y		+= kernel/ mm/ fs/ ipc/ security/ crypto/ block/
core-y		:= $(patsubst %/, %/built-in.o, $(core-y))
core-y		:=usr/built-in.o kernel/built-in.o mm/built-in.o fs/built-in.o ipc/built-in.o security/built-in.o crypto/built-in.o block/built-in.o
libs-y		:= lib/
libs-y1		:= $(patsubst %/, %/lib.a, $(libs-y))

libs-y2		:= $(patsubst %/, %/built-in.o, $(libs-y))

libs-y		:= $(libs-y1) $(libs-y2)
libs-y		:=lib/lib.a lib/built-in.o

drivers-y	:= drivers/ sound/
drivers-y	:= $(patsubst %/, %/built-in.o, $(drivers-y))
drivers-y	:=drivers/built-in.o sound/built-in.o

net-y		:= net/
net-y		:= $(patsubst %/, %/built-in.o, $(net-y))
net-y		:= net/built-in.o

vmlinux-lds  := arch/$(ARCH)/kernel/vmlinux.lds


arm-linux-ld -EL  -p --no-undefined -X -o vmlinux 
-T arch/arm/kernel/vmlinux.lds 
arch/arm/kernel/head.o arch/arm/kernel/init_task.o  
init/built-in.o --start-group  
usr/built-in.o  arch/arm/kernel/built-in.o  
arch/arm/mm/built-in.o  arch/arm/common/built-in.o


内核：----最终目的：运行应用程序（而应用程序 则挂接根文件系统）

1、处理u-boot 传入参数  arch/arm/kernel/head.o
   0、判断是否支持这个CPU
   1、判断是否支持这个单板---是由u-boot启动内核时传入的机器ID（bd->bi_arch_number） bd->bi_arch_number = MACH_TYPE_S3C2440
   2、创建页表
   3、使能MMU
   4、跳到start_kernel（内核第一个C函数）



/*
 * Set of macros to define architecture features.  This is built into
 * a table by the linker.
 */
#define MACHINE_START(_type,_name)			\
static const struct machine_desc __mach_desc_##_type	\
 __used							\
 __attribute__((__section__(".arch.info.init"))) = {	\
	.nr		= MACH_TYPE_##_type,		\
	.name		= _name,

#define MACHINE_END				\
};

MACHINE_START(SMDK2410, "SMDK2410") /* @TODO: request a new identifier and switch
				    * to SMDK2410 */
	/* Maintainer: Jonas Dietsche */
	.phys_io	= S3C2410_PA_UART,
	.io_pg_offst	= (((u32)S3C24XX_VA_UART) >> 18) & 0xfffc,
	.boot_params	= S3C2410_SDRAM_PA + 0x100,
	.map_io		= smdk2410_map_io,
	.init_irq	= s3c24xx_init_irq,
	.init_machine	= smdk2410_init,
	.timer		= &s3c24xx_timer,
MACHINE_END
内核启动流程：
arch/arm/kernel/head.s


start_kernel
    setup_arch                   //解析u-boot传入的启动参数
    setup_command_line           //解析u-boot传入的启动参数
    rest_init
      kernel_init
         prepare_namespace
           mount_root   //挂接根文件系统  ---被命令行参数指定的
         init_post      
                //执行应用程序
          
无分区表 在代码里边写死（linux 内核程序中的 common-smdk.c里边写死分区表）  

u-boot 环境参数 kernel 根文件系统

三、构建根文件系统
启动应用程序 ----首先要先挂接根文件系统才能启动

内核怎样启动第1个应用程序
  1、open (/dev/console)------终端  printf()标准输出、scanf()标准输入、err()
	 sys_dup(0)、sys_dup(0)
  2、run_init_process()---启动应用程序，可以命令行参数指定启动程序，没有命令行execute_command则执行run_init_process("/sbin/init")
   
if (execute_command) {
		run_init_process(execute_command);
		printk(KERN_WARNING "Failed to execute %s.  Attempting "
					"defaults...\n", execute_command);
	}
	run_init_process("/sbin/init");
	run_init_process("/etc/init");
	run_init_process("/bin/init");
	run_init_process("/bin/sh");  

ps---可以看运行程序	

 
init 程序 
1、读取配置文件
2、解析配置文件
3、

busyBox:

设置交叉编译工具链： CROSS
在busybox 源代码中的Makefile中的CROSS_COMPILE 中加入前缀arm-linux-
CROSS_COMPILE   ?=arm-linux-


配置文件一般放在/etc 目录下

busybox -> init_main
				parse_inittab 
					file = fopen(INITTAB, "r");  //打开配置文件 /etc/inittab
					
					new_init_action //1、创建一个init_action结构，填充 2、

最小根文件系统：
					
1、/dev/console   /dev/null

2、init本身，即busybox			
					
3、/etc/inittab

4、配置文件里指定的应用程序	

5、C库


The BusyBox build process is similar to the Linux kernel build:

  make menuconfig     # This creates a file called ".config"
  make                # This creates the "busybox" executable
  make install        # or make CONFIG_PREFIX=/path/from/root install
  

  
/dev/console
/dev/null 

mkdir dev 
cd dev 
mknod console c 5 1

/etc/inittab

mkdir /etc
vim /etc/inittab
console:askfirst:-/bin/sh   //终端 -/bin/sh：表示标准输入、标准输出、标准错误

C库：
mkdir /home/book/work/first_fs/lib  				//先创建lib库
cd /home/book/gcc-3.4.5-glibc-2.3.6/arm-linux/lib	//进入到C库中
cp *.so* /home/book/work/first_fs/lib -d  			//复制C库到lib目录下

完善根文件系统：ps命令

mkdir proc
//mount -t proc none /proc  //手动挂载

vim etc/inittab
::sysinit:/etc/init.d/rcS
mkdir etc/init.d
vim etc/init.d/rcS

mount -t proc none /proc

chmod +x etc/init.d/rcS   //加上可执行的属性

etc/init.d/rcS
mount -a
vim etc/fstab
# device 	mount-point  type 	options		dump	fsck	order

	proc  	/proc		 proc	defaults	0         0
	
# 查看当前挂载根文件系统 

udev //自动创建 /dev/设备节点 mdev 是udev的简化版本

mkdir sys
vim etc/fstab
# device 	mount-point  type 	options		dump	fsck	order

	proc  	/proc		 proc	defaults	0         0
	sysfs   /sys         sysfs  defaults    0         0
	tmpfs   /dev         tmpfs  defaults    0         0

vim etc/init.d/rcS

mkdir /dev/pts 
mount -t devpts devpts /dev/pts
echo /sbin/mdev > /proc/sys/kernel/hotplug
mdev -s

文件系统删除 nand erase root

制作/烧写yaffs映像文件

mkyaffs2image first_fs first_fs.yaffs2



驱动程序框架：

1、写出open()、write()、
2、怎么告诉内核：定义一个结构体file_operations,把这个结构告诉内核register_chrdev(major,name,fileoperation)注册函数
3、谁来调用 注册函数，驱动的入口函数 int first_drv_init(void)  卸载程序用出口函数void first_drv_exit(void)
4、修饰 module_init();


cat /proc/devices  //罗列这个内核目前支持的设备

insmod first_drv.ko    //加载驱动
mknod /dev/xxx c 111 0 //创建设备节点
lsmod  				   //罗列驱动
rmmod first_drv        //卸载驱动

1、驱动程序：自动分配主设备号、也可以手动指定
2、应用程序：自动创建 udev mdev  根据系统信息创建设备节点
定义下面两个变量：
static struct class 



 
kill -9 PID //kill 发信号   PID 接收信号  关信号
kill -10 PID  //开信号 
kill -USR1 PID //1、注册信号处理函数，2、谁发？3、发给谁，4、怎么发

写一个点LED驱动：

1、框架
2、完善，硬件的操作----1、看原理图，确定控制引脚 2、看2440手册 3、写代码 单片机直接操作物理地址 linux驱动用ioremp映射虚拟地址

copy_from_user(&val, buf, count);  //copy_to_user();	


./seconddrvtest &  程序后台运行
top   linux 查看后台运行占用内存 任务管理器

request_irp(irq, handle, irqflags, devname, dev_id)  //注册中断服务函数
feee_irq(irq, dev_id)  //卸载中断程序

cat /proc/interrupts

exec 5</dev/buttons //打开
exec 5<&-            //关闭 

ps  

man 命令  按下q 回车就可以退出

字符设备驱动程序的异步通知：
为了使设备支持异步通知机制，驱动程序中涉及以下3项工作：
1、支持F_SETOWN命令，能在这个控制命令处理中设置filp->f_cwner为对应进程ID
	不过此项工作已由内核完成，设备驱动无须处理。
2、支持F_SETFL命令的处理，每当FASYNC标志改变时，驱动程序中的fasync()函数将得以执行
	驱动中应该实现fasyncc()函数
3、在设备资源可获得时，调用kill_fasync()函数激发相应的信号 

fcntl(fd, F_SETOWN, getpid());  //告诉内核，发给谁

Oflags = fcntl(fd, F_GETFL);
fcntl(fd, F_SETFL, Oflags | FASYNC);//改变fasync标记，最终会调用到驱动的faync > fasync_helper :初始化释放fasync_struct

1、原子操作
原子操作指的是在执行过程中不会被别的代码路径所中断的操作。
常用原子操作函数举例：
atomic_t v = ATOMIC_INIT(0);   //定义原子变量v并初始化为0
atomic_read(atomic_t *v);      //返回原子变量的值
void atomic_inc(atomic_t *v);  //原子变量增加1
void atomic_dec(atomic_t *v);  //原子变量减少1
int atomic_dec_and_test(atomic_t *v);  //自减操作后测试其是否为0，为0则返回true,否则返回false

2.信号量
信号量(semaphore)是用于保护临界区的一种常用方法，只有得到信号量的进程才能执行临界区代码。
当获取不到信号量时，进程进入休眠等待状态。

定义信号量
struct semaphore sem;
初始化信号量
void sema_init(struct semaphore *sem, int val);
void init_MUTEX(struct semaphore *sem);//初始化为0

static DECLARE_MUTEX(button_lock);   //互斥锁

获得信号量
void down(struct semaphore *sem);
int down_interruptible(struct semaphore *sem);
int down_trylock(struct semaphore *sem);

释放信号量
void up(struct semaphore *sem);

3.阻塞
阻塞操作是指在执行设备操作时若不能获得资源则挂起进程，直到满足可操作的条件后再进行操作。
被挂起的进程进入休眠状态，被从调度器的运行队列移走，直到等待的条件被满足。

非阻塞操作：进程在不能进行设备操作时并不能挂起，它或者放弃，或者不停地查询，直至可以进行操作为止。
fd = open("...", O_RDWR | c);


编译设备树：

linux 3.0 以后的内核才出现设备树： arch/arm/boot/dts/目录
节点 属性

dts被编译成dtb二进制文件  

cd proc   cd tree-device  

struct device_node


## 二、更新文件

















 





