��Linux �µ�dnw
1. ��linux�µ�dnwӦ�ó���ŵ�/binĿ¼��
��ִ�� sudo chmod +x /bin/dnw
sudo chmod +s /bin/dnw
2.���ʹ��vmware����vmwareλ��ǰ̨��Ȼ������USB������PC�Ϳ�����USB DEVICE �ӿ�
��lsusb����ȷ��VMWARE LINUX�Ѿ�ʶ���UBOOT��USB�豸

���س���

/bin/dnw ~/u-boot-1.1.6/u-boot.bin

ʹ��3.4.5�ı�����
tar xjf arm-linux-gcc-3.4.5-glibc-2.3.6.tar.bz2
����PATH,����bin�ļ�����
1��sudo vim /etc/environment 

2��PATH="/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin"
3��export PATH = /usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin


sudo�������������������ִ�����Ԥ������Ϊroot

0����ѹ��
1���򲹶�  patch -p����1�� < ..�����ļ���/u-boot-1.1.6_jz2440.patch��  //���Ե�б�ܵ��ļ�������
2������  make 100ask24x0_config   ///----mkconfig 100ask24x0 arm arm920t 100ask24x0 NULL s3c24x0
3������  make

1����flash+дflash
2����ʼ��SDRAM
3�������ں�


u-boot ���ܣ������ں�

1����flash�ж����ں�  2����ʼ��SDRAM 3�������ں�

����SDRAM����ȥ�����Ծ�Ҫ��ʼ��SDRAM����ʼ��ʱ�ӣ��ؿ��Ź�//Ӳ����ʼ��  ��������  ��дflash ---���� USB ���� Ϊ�����������ӵĹ��� ��һ����Ƭ������

�������ù��̣�

@$

$@ ��ʾ�����Ŀ���ļ���
�� 

$(@:_config=)   100ask24x0


ln �Cs Դ�ļ� Ŀ���ļ�

>  ---�½�һ���ļ�
>>  --������׷�ӽ�ȥ

����������̣�

������̣�

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


�������� make 
1����1���ļ� cpu/arm920t/start.s
2�����ӵ�ַ  0x33f80000+board/100ask24x0/u-boot.lds

//board/10024x0/config.mk

ջ���ú�֮��ſ��Ե���C����

��һ�׶Σ�a����SVCģʽ b���ؿ��Ź� c�������ж�  d����ʼ��SDRAM e������ջ f��ʱ�� g������ flash--->SDRAM h����BSS�� 

�ڶ��׶Σ�i������C���� _start_armboot

bootcmd=nand read.jffs2 0x30007FC0 kernel; bootm 0x30007FC0


kernel---����

��NAND�����ںˣ�������� ����kernel����(linuxû�з��������ķ�����ַ)
read.jffs2 0x30007FC0 kernel

������bootm 0x30007FC0



u-boot�ĺ��ľ���  ����---�ں˵��������̼�������ʽ

s=getenv("bootcmd")

u-boot ��flash�϶����ں� ��uImage ----ͷ��+�������ں� ���ص�ַ ��ڵ�ַ

������������ ����������ڵ�ַ�����ں� 

���� do_bootm_linux


�����ں� 1��������������-----��ĳ����ַ��ĳ�ָ�ʽ��������0x30000100  2��������ڵ�ַ ----thekernel

����ID �ں�ʶ��֧�ֵĵ���,ͨ�� ����ID������

u-boot �����ں� 	theKernel (0, bd->bi_arch_number, bd->bi_boot_params);
 
bd->bi_arch_number--����ID 
bd->bi_boot_params--��Щ��������ŵĵ�ַ

sudo vim /etc/environ

export PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin:/usr/games:~/gcc-3.4.5-glibc-2.3.6/bin


���س���
/bin/dnw ~/u-boot-1.1.6/u-boot.bin



1���򲹶�  patch -p? < ..�����ļ�
2������  1 ʹ��Ĭ�����ã��������޸ģ���arh/arm/configs�ҵ����Ƶ������ļ�xxx_defconfig  make xxx_defconfig ---���浽.config  
                                                                                       make menuconfig   ----ȥ��ȡ.config

����ôȥ�ң�ʹ������ find -name "defconfig" * -nR��
         2 ʹ�ó����ṩ�������ļ�  cp config_����  .config  make menuconfig  
��֪�����õ������ ��������� find -name "defconfig"

���÷�����
���ý�����ɣ� ����.config
����� CONFIG_DM9000 = y/m
1��CԴ�룺 CONFIG_DM9000
2��Makefile   drivers/net/Makefile obj-$(CONFIG_DM9000)+= dm9000.o    //��Ŀ¼Makefile $(CONFIG_DM9000)��ʾһ������
3��include/config/auto.conf
4��include/linux/autoconf.h //����CONFIG_DM9000 = y/m ��������Ϊ1

//linux-2.6.22.6/Documentation/kbuild/makefile.txt 

a.c b.c 

obj - y += a.o b.o  

obj-m += ab.obj
ab - objs := a.o b.o

a.c b.c ----> a.o b.o ---> ab.ko  

3������ 


�ں���Ŀ¼Makefile 

obj_y+=xxx.o   --�ᱻ�����ȥ�ں� 
obj_m+=yyy.o   --�ᱻ����ɿɼ���ģ�� yyy.ko



���� Makefile :��1���ļ�---arch/arm/kernel/head.o �����ӽű�---arch/arm/kernel/vmlinux.lds

make uImageʱ  1��.config����������autoconf.h  .config ����������auto.conf---������makefile����

3������ 


zImage Image xipImage bootpImage uImage: vmlinux  ---uImage ������vmlinux

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


�ںˣ�----����Ŀ�ģ�����Ӧ�ó��򣨶�Ӧ�ó��� ��ҽӸ��ļ�ϵͳ��

1������u-boot �������  arch/arm/kernel/head.o
   0���ж��Ƿ�֧�����CPU
   1���ж��Ƿ�֧���������---����u-boot�����ں�ʱ����Ļ���ID��bd->bi_arch_number�� bd->bi_arch_number = MACH_TYPE_S3C2440
   2������ҳ��
   3��ʹ��MMU
   4������start_kernel���ں˵�һ��C������



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
�ں��������̣�
arch/arm/kernel/head.s


start_kernel
    setup_arch                   //����u-boot�������������
    setup_command_line           //����u-boot�������������
    rest_init
      kernel_init
         prepare_namespace
           mount_root   //�ҽӸ��ļ�ϵͳ  ---�������в���ָ����
         init_post      
                //ִ��Ӧ�ó���
          
�޷����� �ڴ������д����linux �ں˳����е� common-smdk.c���д��������  

u-boot �������� kernel ���ļ�ϵͳ

�����������ļ�ϵͳ
����Ӧ�ó��� ----����Ҫ�ȹҽӸ��ļ�ϵͳ��������

�ں�����������1��Ӧ�ó���
  1��open (/dev/console)------�ն�  printf()��׼�����scanf()��׼���롢err()
	 sys_dup(0)��sys_dup(0)
  2��run_init_process()---����Ӧ�ó��򣬿��������в���ָ����������û��������execute_command��ִ��run_init_process("/sbin/init")
   
if (execute_command) {
		run_init_process(execute_command);
		printk(KERN_WARNING "Failed to execute %s.  Attempting "
					"defaults...\n", execute_command);
	}
	run_init_process("/sbin/init");
	run_init_process("/etc/init");
	run_init_process("/bin/init");
	run_init_process("/bin/sh");  

ps---���Կ����г���	

 
init ���� 
1����ȡ�����ļ�
2�����������ļ�
3��

busyBox:

���ý�����빤������ CROSS
��busybox Դ�����е�Makefile�е�CROSS_COMPILE �м���ǰ׺arm-linux-
CROSS_COMPILE   ?=arm-linux-


�����ļ�һ�����/etc Ŀ¼��

busybox -> init_main
				parse_inittab 
					file = fopen(INITTAB, "r");  //�������ļ� /etc/inittab
					
					new_init_action //1������һ��init_action�ṹ����� 2��

��С���ļ�ϵͳ��
					
1��/dev/console   /dev/null

2��init������busybox			
					
3��/etc/inittab

4�������ļ���ָ����Ӧ�ó���	

5��C��


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
console:askfirst:-/bin/sh   //�ն� -/bin/sh����ʾ��׼���롢��׼�������׼����

C�⣺
mkdir /home/book/work/first_fs/lib  				//�ȴ���lib��
cd /home/book/gcc-3.4.5-glibc-2.3.6/arm-linux/lib	//���뵽C����
cp *.so* /home/book/work/first_fs/lib -d  			//����C�⵽libĿ¼��

���Ƹ��ļ�ϵͳ��ps����

mkdir proc
//mount -t proc none /proc  //�ֶ�����

vim etc/inittab
::sysinit:/etc/init.d/rcS
mkdir etc/init.d
vim etc/init.d/rcS

mount -t proc none /proc

chmod +x etc/init.d/rcS   //���Ͽ�ִ�е�����

etc/init.d/rcS
mount -a
vim etc/fstab
# device 	mount-point  type 	options		dump	fsck	order

	proc  	/proc		 proc	defaults	0         0
	
# �鿴��ǰ���ظ��ļ�ϵͳ 

udev //�Զ����� /dev/�豸�ڵ� mdev ��udev�ļ򻯰汾

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

�ļ�ϵͳɾ�� nand erase root

����/��дyaffsӳ���ļ�

mkyaffs2image first_fs first_fs.yaffs2



���������ܣ�

1��д��open()��write()��
2����ô�����ںˣ�����һ���ṹ��file_operations,������ṹ�����ں�register_chrdev(major,name,fileoperation)ע�ắ��
3��˭������ ע�ắ������������ں��� int first_drv_init(void)  ж�س����ó��ں���void first_drv_exit(void)
4������ module_init();


cat /proc/devices  //��������ں�Ŀǰ֧�ֵ��豸

insmod first_drv.ko    //��������
mknod /dev/xxx c 111 0 //�����豸�ڵ�
lsmod  				   //��������
rmmod first_drv        //ж������

1�����������Զ��������豸�š�Ҳ�����ֶ�ָ��
2��Ӧ�ó����Զ����� udev mdev  ����ϵͳ��Ϣ�����豸�ڵ�
������������������
static struct class 



 
kill -9 PID //kill ���ź�   PID �����ź�  ���ź�
kill -10 PID  //���ź� 
kill -USR1 PID //1��ע���źŴ�������2��˭����3������˭��4����ô��

дһ����LED������

1�����
2�����ƣ�Ӳ���Ĳ���----1����ԭ��ͼ��ȷ���������� 2����2440�ֲ� 3��д���� ��Ƭ��ֱ�Ӳ��������ַ linux������iorempӳ�������ַ

copy_from_user(&val, buf, count);  //copy_to_user();	


./seconddrvtest &  �����̨����
top   linux �鿴��̨����ռ���ڴ� ���������

request_irp(irq, handle, irqflags, devname, dev_id)  //ע���жϷ�����
feee_irq(irq, dev_id)  //ж���жϳ���

cat /proc/interrupts

exec 5</dev/buttons //��
exec 5<&-            //�ر� 

ps  

man ����  ����q �س��Ϳ����˳�

�ַ��豸����������첽֪ͨ��
Ϊ��ʹ�豸֧���첽֪ͨ���ƣ������������漰����3�����
1��֧��F_SETOWN�������������������������filp->f_cwnerΪ��Ӧ����ID
	��������������ں���ɣ��豸�������봦��
2��֧��F_SETFL����Ĵ���ÿ��FASYNC��־�ı�ʱ�����������е�fasync()����������ִ��
	������Ӧ��ʵ��fasyncc()����
3�����豸��Դ�ɻ��ʱ������kill_fasync()����������Ӧ���ź� 

fcntl(fd, F_SETOWN, getpid());  //�����ںˣ�����˭

Oflags = fcntl(fd, F_GETFL);
fcntl(fd, F_SETFL, Oflags | FASYNC);//�ı�fasync��ǣ����ջ���õ�������faync > fasync_helper :��ʼ���ͷ�fasync_struct

1��ԭ�Ӳ���
ԭ�Ӳ���ָ������ִ�й����в��ᱻ��Ĵ���·�����жϵĲ�����
����ԭ�Ӳ�������������
atomic_t v = ATOMIC_INIT(0);   //����ԭ�ӱ���v����ʼ��Ϊ0
atomic_read(atomic_t *v);      //����ԭ�ӱ�����ֵ
void atomic_inc(atomic_t *v);  //ԭ�ӱ�������1
void atomic_dec(atomic_t *v);  //ԭ�ӱ�������1
int atomic_dec_and_test(atomic_t *v);  //�Լ�������������Ƿ�Ϊ0��Ϊ0�򷵻�true,���򷵻�false

2.�ź���
�ź���(semaphore)�����ڱ����ٽ�����һ�ֳ��÷�����ֻ�еõ��ź����Ľ��̲���ִ���ٽ������롣
����ȡ�����ź���ʱ�����̽������ߵȴ�״̬��

�����ź���
struct semaphore sem;
��ʼ���ź���
void sema_init(struct semaphore *sem, int val);
void init_MUTEX(struct semaphore *sem);//��ʼ��Ϊ0

static DECLARE_MUTEX(button_lock);   //������

����ź���
void down(struct semaphore *sem);
int down_interruptible(struct semaphore *sem);
int down_trylock(struct semaphore *sem);

�ͷ��ź���
void up(struct semaphore *sem);

3.����
����������ָ��ִ���豸����ʱ�����ܻ����Դ�������̣�ֱ������ɲ������������ٽ��в�����
������Ľ��̽�������״̬�����ӵ����������ж������ߣ�ֱ���ȴ������������㡣

�����������������ڲ��ܽ����豸����ʱ�����ܹ��������߷��������߲�ͣ�ز�ѯ��ֱ�����Խ��в���Ϊֹ��
fd = open("...", O_RDWR | c);


�����豸����

linux 3.0 �Ժ���ں˲ų����豸���� arch/arm/boot/dts/Ŀ¼
�ڵ� ����

dts�������dtb�������ļ�  

cd proc   cd tree-device  

struct device_node


## ���������ļ�

















 





