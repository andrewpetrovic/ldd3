# �����ҵ�һ������Linux�ں� hello.c

```C
#include <linux/init.h>
#include <linux/module.h>
MODULE_LICENSE("Dual BSD/GPL");

static int hello_init(void){
       printk(KERN_ALERT "Hello, world\n");
       return 0;
}
static void hello_exit(void){

       printk(KERN_ALERT "Goodbye, cruel world\n");
}

module_init(hello_init);
module_exit(hello_exit);
```

�ҵ�һ�ζ�����Щʲô��?�ܼ�,�ͳ��˳���һ��Linuxģ���̼ܹ�
��Linux�豸�����Ŀ���,����С�����HelloWorld,��������USB,���Ǵ�����Androidƽ̨��RFģ���շ���Ϣ,�����õ���Linuxģ����.

��Linuxģ������,�����κ�ģ�����,����Ҫ�ҵ���module_init��module_exit��������. 
- module_init��Ĳ�����ģ�鱻װ��ʱҪ���õĺ���,����ģ������;
- module_exit��Ĳ���,����ģ�鱻ж��ʱҪ���õĺ���,������Ҫ���ж��ģ��ǰ��ȫ����������.

���Զ���hello.c,��������,װ��ģ��ʱ����hello_init;ж��ģ��ʱ,����hello_exit

## ����ģ��
����hello.c,��Ҫָ���ں�Դ����(���ں˴������õ�),���ṩһ��Makefile�ļ�.

### Makefile �ļ�
Makefileֻ��Ҫһ�仰:
```
obj-m := hello.o
```
### ʹ��Make����ģ���Լ�������̷���
``` shell
make -C /usr/src/linux-2.6.10 M=`pwd` modules
```
����˵��:
- -Cָ��֮ǰ�ᵽ��Linux�ں�Դ������·��
���·����Linux�ں˶����Makefile������Ϊ�����ں˹���ϵͳ�����
- Mָ��moduleĿ�������·��
M=�����Ƿ�����(ESC������)�����ǵ�����,�����pwd��ʾ��pwd����ִ�еĽ��(��hello.c����·��)��ֵ��M

ִ���������,���Կ���������hello.ko,��helloģ��

��ô��һ��ֱ�ӽ�����KO�ļ��Ӹ�...

�����ٿ��������־,�Բ�,����Ǵ��һ�����ȭ��ֱ�Ӷ���KO��,��Make����̫ǿ����
``` shell
$ make -C /usr/src/linux-2.6.10 M=`pwd` modules
make[1]: Entering directory `/usr/src/linux-2.6.10'
 CC [M] /home/ldd3/src/misc-modules/hello.o
 Building modules, stage 2.
 MODPOST
 CC /home/ldd3/src/misc-modules/hello.mod.o
 LD [M] /home/ldd3/src/misc-modules/hello.ko 
make[1]: Leaving directory `/usr/src/linux-2.6.10'
```
��̶̵�һ˲�ͷ�������ô��,��������ô������?

�ٿ�һ��LDD3 ch02 s4 , ��������ôһ��: "���ں˹���ϵͳ���������µĹ���",����������Щʲô...

������,�ҵ��˶�ȫ���̵�����,
���� 
obj-m := hello.o������һ��ģ��Ҫ��Ŀ���ļ�hello.o����
����
makefile,����Ҫ�ڸ�����ں˹���ϵͳ�Ļ����б�����
����
�������ʼ�Ǹı�����Ŀ¼����-Cѡ���ṩ��Ŀ¼��,������ᷢ���ں˵Ķ���makefile,M=ѡ��ʹmakefile����ͼ����Ŀ��ǰ,�ص�ģ��Դ��Ŀ¼
����
�ȵ�����

### ����Makefile�ļ�
���� make hello.c���ģ�黹��, �����Ժ�Ҫmake�����ģ��, ����Ҫһ��һ���ظ���дmakefile,���ظ�����ô���ӵ�make������?��Ϊһ������Щ�������д����ĳ���Գ,�𰸵�Ȼ��: no
����LDD3�ṩ��һ���ȽϺõ�Makefileģ��:
```shell

# To build modules outside of the kernel tree, we run "make"
# in the kernel source tree; the Makefile these then includes this
# Makefile once again.
# This conditional selects whether we are being included from the
# kernel Makefile or not.
ifeq ($(KERNELRELEASE),)

	# Assume the source tree is where the running kernel was built
	# You should set KERNELDIR in the environment if it's elsewhere
	KERNELDIR ?= /lib/modules/$(shell uname -r)/build
	# The current directory is passed to sub-makes as argument
	PWD := $(shell pwd)

modules:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) modules

modules_install:
	$(MAKE) -C $(KERNELDIR) M=$(PWD) modules_install

clean:
	rm -rf *.o *~ core .depend .*.cmd *.ko *.mod.c .tmp_versions

.PHONY: modules modules_install clean

else
	# called from kernel build system: just declare what our modules are
	obj-m := hello.o
endif
```
���makefile�ṩ�⼸������:
- make ���� make modules: ���� obj-mָ����ģ��
- make modules_install: ���벢��װģ��
- make clean: �����������������ִ��ִ�к����ʱ�ļ�.�����Ҫ����make modules_install,��ҪsuȨ��
- make .PHONY:ִ��һ��make modules_install,Ȼ��������ʱ�ļ�

## ����ģ��
����ģ��İ�װ/ж����ҪsuȨ��
### ��װģ��
��װģ��ʹ��insmod����,��hello.koΪ��:
```shell
sudo insmod hello.ko
```

### ж��ģ��
ж��ģ��ʹ��rmmod����,��hello.koΪ��:
```shell
sudo rmmod hello
```
### �鿴���
ʹ��dmsg�鿴�����־
```shell
desg
```
���Կ��������˸ղŰ�װģ��ʱhello_init��ӡ�ġ�Hello, world�����ֶ���һ����䡱Goodbye, cruel world������仰����ģ��ж�غ���hello_exit������ӡ�ġ�

## �ܽ�
��ѧϰLDD3��ʼ��ʽ����ϵͳѧϰLinux�ں�֮��.�������˽���Linuxģ���̵ĸ���,ѧϰ��Linuxģ���̼ܹ��Լ����غ�ж�صķ���,������makefileģ������ɱ��빤��.�о�C���Լ�����Ҫ������ʦ��,���Ҷ���makefile����û�и���.���帴ϰC�����Լ�ѧϰmakefile��д����ѧϰLDD3�����зǳ���Ҫ����.