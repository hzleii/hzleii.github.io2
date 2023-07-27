---
title: Linux 常用命令
date: 2021-03-13
lastmod: 2021-03-13
categories: ["Linux"]
tags: ["Linux"]
description: Linux 常用命令
---



## cd

```
cd （change directory：英文释义是改变目录）切换目录

cd ../ ;跳到上级目录
cd /opt ;不管现在到那直接跳到指定的opt文件夹中
cd ~ ;切换当前用户的家目录。root用户的家目录就是root目录。
```



## pwd

```
pwd （print working directory：显示当前工作目录的绝对路径）

pwd 
显示当前的绝对路劲
```



## ls

```
ls （ls：list的缩写，查看列表）查看当前目录下的所有文件夹（ls 只列出文件名或目录名）

ls -a ;显示所有文件夹,隐藏文件也显示出来
ls -R ;连同子目录一起列出来
```

## ll

```
ll （ll：list的缩写，查看列表详情）查看当前目录下的所有详细信息和文件夹（ll 结果是详细,有时间,是否可读写等信息）

ll -a ;显示所有文件,隐藏文件也显示出来
ll -R ;连同子目录内容一起列出来
ll -h ;友好展示详情信息,可以看大小
ll -al ;即能显示隐藏文件又能显示详细列表。 
```



## touch

```
touch （touch：创建文件）创建文件

touch test.txt  ;创建test.txt文件
touch /opt/java/test.java ;在指定目录创建test.java文件
```



## mkdir

```
mkdir （mkdir：创建目录） 创建目录

mkdir 文件夹名称 ;在此目录创建文件夹
mkdir /opt/java/jdk ;在指定目录创建文件夹
```



## cat

```
cat （concatenate：显示或把多个文本文件连接起来）查看文件命令（可以快捷查看当前文件的内容）（不能快速定位到最后一页）

cat lj.log ;快捷查看文件命令 
Ctrl + c ;暂停显示文件
Ctrl + d ;退出查看文件命令
```



## more

```
more （more：更多的意思）分页查看文件命令（不能快速定位到最后一页）

回车：向下n行，需要定义，默认为1行。
空格键：向下滚动一屏或Ctrl+F
B：返回上一层或Ctrl+B
q：退出more
```



## less

```
less （lese：较少的意思）分页查看文件命令（可以快速定位到最后一页）

less -m 显示类似于more命令的百分比。
less -N 显示每行的行号。(大写的N)
两参数一起使用如：less -mN 文件名，如此可分页并显示行号。

空格键：前下一页或page down。
回车：向下一行。
b：后退一页 或 page up。
q：退出。
d：前进半页。
u：后退半页
```



## tail

```
tail（尾巴） 查看文件命令（看最后多少行）

tail -10 ;文件名 看最后10行
```



## cp

```
cp（copy单词缩写，复制功能）

cp /opt/java/java.log /opt/logs/ ;把java.log 复制到/opt/logs/下
cp /opt/java/java.log /opt/logs/aaa.log ;把java.log 复制到/opt/logs/下并且改名为aaa.log
cp -r /opt/java /opt/logs ;把文件夹及内容复制到logs文件中
```



## mv

```
mv（move单词缩写，移动功能，该文件名称功能）

mv /opt/java/java.log /opt/mysql/ ;移动文件到mysql目录下
mv java.log mysql.log ;把java.log改名为mysql.log
```



## rm

```
rm（remove：移除的意思）删除文件，或文件夹

-f或--force 强制删除文件或目录。删除文件不包括文件夹的文件	
-r或-R或--recursive 递归处理，将指定目录下的所有文件及子目录一并删除。 
-rf 强制删除文件夹及内容

rm 文件名 ;安全删除命令  （yes删除 no取消）
rm -rf 强制删除文件夹及内容
rm -rf *  删除当前目录下的所有内容。
rm -rf /* 删除Linux系统根目录下所有的内容。系统将完蛋。   
```



## find

```
find （find：找到的意思）查找指定文件或目录

* 表示0~多个任意字符。

find -name 文件名;按照指定名称查找在当前目录下查找文件
find / -name 文件名按照指定名称全局查找文件
find -name '*文件名' ;任意前缀加上文件名在当前目录下查找文件
find / -name '*文件名*' ;全局进行模糊查询带文件名的文件 
```



## vi

```
vi （VIsual：视觉）文本编辑器  类似win的记事本 （操作类似于地下的vim命令，看底下vim 的操作）
```



## vim

```
vim （VI IMproved：改进版视觉）改进版文本编辑器 	（不管是文件查看还是文件编辑 按 Shift + 上或者下可以上下移动查看视角）

输入”vim 文件名” 打开文件，刚刚时是”一般模式”。

一般模式：可以浏览文件内容，可以进行文本快捷操作。如单行复制，多行复制，单行删除，多行删除，（退出）等。
插入模式：可以编辑文件内容。
底行模式：可以进行强制退出操作,不保存   :q!
		 可以进行保存并退出操作       :wq

按下”i”或”a”或”o”键，从”一般模式”，进入”插入模式（编辑模式）”。
在编辑模式下按”Esc” 即可到一般模式
在一般模式下按”:”，冒号进入底行模式。

在一般模式下的快捷键
	dd ;删除一整行
	X ;向前删除  等同于windowns系统中的删除键
	x ;向后删除和大写x相反方向
	Ctrl + f ;向后看一页
	Ctrl + b ;向前看一页
	u ;撤销上一步操作
	/word ;向下查找word关键字  输入:n查找下一个,N查找上一个（不管是哪个查找都是全局查找 只不过n的方向相反）
	?log ;向上查找log关键字  输入:n查找上一个,N查找下一个
	:1,90s/redis/Redis/g ;把1-90行的redis替换为Redis。语法n1,n2s/原关键字/新关键字/g，n1代表其实行,n2代表结尾行,g是必须要的
	:0 ;光标移动到第一行
	:$ ;光标移动到最后一行
	:300 ;光标移动到300行,输入多少数字移动到多少行
	:w  ;保存
	:w! ;强制保存
	:q  ;退出
	:q! ;强制退出
	5dd ;删除后面5行,打一个参数为自己填写
	5x ;删除此光标后面5个字符
	d1G ;删除此光标之前的所有
	d0 ;从光标当前位置删除到此行的第一个位置
	yy ;复制
	p ;在光标的下面进行粘贴
	P ;在光标的上门进行粘贴
```



## |

```
| 管道命令（把多个命令组合起来使用）

管道命令的语法：命令1 | 命令2 | 命令3。
```



## grep

```
grep （grep ：正则表达式）正则表达式，用于字符串的搜索工作(模糊查询)。不懂可以先过

单独使用：
grep String test.java ；在test.java文件中查找String的位置，返回整行
一般此命令不会单独使用下面列几个常用的命令（地下通过管道命令组合起来使用）

ps aux|grep java ；查找带java关键字的进程
ll |grep java ；查找带java关键字的文件夹及文件
```



## yum install -y lrzsz

```
yum install -y lrzsz 命令（实现win到Linux文件互相简单上传文件）

#（实际上就是在Linux系统中下载了一个插件）下了了此安装包后就可以实现win系统到linux之间拉文件拉文件
#等待下载完了就可以输入：

rz  从win系统中选择文件上传到Linux系统中

sz  文件名 选择Linux系统的文件复制到win系统中
```



## tar

```
tar （解压  压缩  命令）

常用的组合命令：
-z 是否需要用gzip压缩。
-c 建立一个压缩文件的参数指令(create) –压缩
	-x 解开一个压缩文件的参数指令(extract) –解压  
	-v 压缩的过程中显示文件(verbose)
	-f 使用档名，在f之后要立即接档中(file)
	常用解压参数组合：zxvf
	常用压缩参数组合：zcvf 

解压命令：
tar -zxvf redis-3.2.8.tar.gz ；解压到当前文件夹
tar -zxvf redis-3.2.8.tar.gz -C /opt/java/ ；解压到指定目录

压缩命令：（注意 语法有点反了，我反正每次都搞反）
tar -zcvf redis-3.2.8.tar.gz  redis-3.2.8/  ;语法 tar -zcvf  压缩后的名称  要压缩的文件
tar -zcvf 压缩后的文件（可指定目录）  要压缩的文件（可指定目录） 
```



## ps

```
ps （process status：进程状态，类似于windows的任务管理器）

常用组合：ps -ef     标准的格式查看系统进程
	  ps -aux    BSD格式查看系统进程
	  ps -aux|grep redis  BSD格式查看进程名称带有redis的系统进程（常用技巧）
//显示进程的一些属性,需要了解（ps aux）
USER     //用户名
PID		 //进程ID号,用来杀死进程的
%CPU     //进程占用的CPU的百分比
%MEM     //占用内存的的百分比
VSZ      //该进程使用的虚拟內存量（KB）
RSS      //该进程占用的固定內存量（KB）
STAT     //进程的状态
START    //该进程被触发启动时间
TIME     //该进程实际使用CPU运行的时间
```



## clear

```
clear  清屏命令。（强迫症患者使用）

kill 命令用来中止一个进程。（要配合ps命令使用，配合pid关闭进程）
（ps类似于打开任务管理器，kill类似于关闭进程）
	kill -5 进程的PID ;推荐,和平关闭进程
	kill -9 PID ;不推荐,强制杀死进程
```



## ifconfig

```
ifconfig命令

用于查看和更改网络接口的地址和参数，包括IP地址、网络掩码、广播地址，使用权限是超级用户。（一般是用来查看的，很少更改）
如果此命令输入无效，先输入yum -y install net-tools
ifconfig
```



## ping

```
ping （用于检测与目标的连通性）语法：ping ip地址

测试：
1、在Windows操作系统中cmdipconfig，查看本机IP地址：
2、再到LInux系统中输入 ping ip地址
（公司电脑，我就不暴露Ip了,没图片  自己去试）
按Ctrl + C 可以停止测试。
```



## free

```
free 命令 （显示系统内存）

#显示系统内存使用情况，包括物理内存、交互区内存(swap)和内核缓冲区内存。
-b 以Byte显示内存使用情况
-k 以kb为单位显示内存使用情况
-m 以mb为单位显示内存使用情况
-g 以gb为单位显示内存使用情况
-s<间隔秒数> 持续显示内存
-t 显示内存使用总合
```



## top

```
top 命令

#显示当前系统正在执行的进程的相关信息，包括进程 ID、内存占用率、CPU 占用率等
-c 显示完整的进程命令
-s 保密模式
-p <进程号> 指定进程显示
-n <次数>循环显示次数
```



## netstat

```
netstat 命令

#Linux netstat命令用于显示网络状态。
#利用netstat指令可让你得知整个Linux系统的网络情况。
#语法：
netstat [-acCeFghilMnNoprstuvVwx][-A<网络类型>][--ip]
```



## file

```
file （可查看文件类型）

file 文件名
```



## 重启linux

```
Linux centos 重启命令：reboot
```



## 关机linux

```
Linux centos 关机命令：halt
```



## 同步时间命令

```
ntpdate ntp1.aliyun.com
```



## 更改为北京时间命令

```
rm -rf /etc/localtime
ln -s /usr/share/zoneinfo/Asia/Shanghai /etc/localtime
```



## 查看时间命令：

```
date
```



