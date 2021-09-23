# 安装软件

## yum基本操作

http://blog.chinaunix.net/uid-346158-id-2131252.html

## 查看是否安装了某个库

[huawei@n148 tutorial]$ rpm -qa | grep boost

## 查看某个库安装位置

[root@linuxftp243 ~]# rpm -ql httpd-2.4.6-90.el7.centos.x86_64

# 操作系统GUI

## centos图形界面的开启和关闭

https://www.cnblogs.com/beyang/p/8513215.html

# 用户、组、密码

## 当前在线人数
```
who | wc -l
```
使用脚本文件方式
```
[huawei@10 ~]$ cd playground/
[huawei@10 playground]$ cat>nusers
who|wc -l（ctrl+d表示结束文件录入，即end of file）
[huawei@10 playground]$ ll
total 4
-rw-rw-r--. 1 huawei huawei 10 Aug 30 07:39 nusers
[huawei@10 playground]$ chmod +x nusers
[huawei@10 playground]$ ll
total 4
-rwxrwxr-x. 1 huawei huawei 10 Aug 30 07:39 nusers
[huawei@10 playground]$ ./nusers
3
[huawei@10 playground]$

```
## 模拟输入密码
输入时候是不显示的，/dev/tty此特殊文件用于获取用户输入
```
#! /bin/sh -
printf "enter new password:\n"
stty -echo
read pass</dev/tty
printf "enter again:\n"
read pass2</dev/tty
stty echo
printf "1:%s\n2:%s\n" $pass $pass2
```
## 添加、删除、修改用户

```
创建
[huawei@n148 ~]$ sudo useradd -m test
[huawei@n148 ~]$ ls -al /home/
total 20
drwxr-xr-x. 13 root       root        172 Sep 23 10:28 .
dr-xr-xr-x. 17 root       root       4096 Jul  2 14:15 ..
drwx------   3 test       test         78 Sep 23 10:28 test
drwxr-xr-x.  3 root       root         23 Jul 16  2020 work
可以使用-D指定默认参数，上例未涉及

删除，-r会删除子目录
[huawei@n148 ~]$ sudo userdel -r test

```

usermod 修改用户账户的字段，还可以指定主要组以及附加组的所属关系
passwd 修改已有用户的密码
chpasswd 从文件中读取登录名密码对，并更新密码
chage 修改密码的过期日期
chfn 修改用户账户的备注信息
chsh 修改用户账户的默认登录shell 

以上详见Linux命令行与shell脚本编程大全.第3版7.1.5

## 组的创建于修改

Linux命令行与shell脚本编程大全.第3版 7.3

# 环境与PATH

系统环境变量基本上都是使用全大写字母，以区别于普通用户的环境变量
## 加入path非永久
```
[huawei@10 ~]$ PATH=$PATH:bin
```
## 永久的方法
放入.profile、.bashrc都可以，这样每次启动都自动执行  
shell会按照按照下列顺序，运行第一个被找到的文件，余下的则被忽略：
* $HOME/.bash_profil
* $HOME/.bash_login
* $HOME/.profile

$HOME/.bashrc该文件通常通过其他文件运行

可以把自己的alias设置放在$HOME/.bashrc启动文件中，使其效果永久化

## 打印环境变量
```
[huawei@10 bin]$ env
[huawei@10 bin]$ printenv

[huawei@10 postdb]$ printenv HOME
/home/huawei

[huawei@10 postdb]$ echo $HOME
/home/huawei

[huawei@10 postdb]$ set
[huawei@10 bin]$ export -p
```
命令env、printenv和set之间的差异很细微。set命令会显示出全局变量、局部变量以及用户定义变量。它还会按照字母顺序对结果进行排序。env和printenv命令同set命令的区别在于前两个命令不会对变量排序，也不会输出局部变量和用户定义变量。在这种情况下，env和printenv的输出是重复的。不过env命令有一个printenv没有的功能，这使得它要更有用一些。
## 所有环境变量
Linux命令行与shell脚本编程大全.第3版 第111页
## 定义与取消用户变量

```
[huawei@10 postdb]$ my_variable="I am Global now"
[huawei@10 postdb]$ echo $my_variable
I am Global now
[huawei@10 postdb]$ unset my_variable
[huawei@10 postdb]$ echo $my_variable
```
# 打印
## 八进制打印每个字符 od
```
[huawei@10 bin]$ od -a -b nusers
0000000   #   !  sp   /   b   i   n   /   s   h  sp   -  nl   w   h   o
        043 041 040 057 142 151 156 057 163 150 040 055 012 167 150 157
0000020   |   w   c  sp   -   l  nl
        174 167 143 040 055 154 012
0000027

```
# Shell文件
## Sha-Bang(#!)
要在脚本文件第一行，用于指定用哪个shell执行此脚本
```
#! /bin/sh -
who|wc -l
```
## 关于end of file
* 录入时刻的ctrl+d代表文件尾  
* 读取/dev/null也会返回文件尾
## 调试信息
* 可以被执行时刻加入-x
```
[huawei@10 bin]$ sh -x nusers
```
* 可以在脚本文件中加入set -x即开启，set +x即关闭
# 目录处理
## 创建多层
```
$ mkdir -p New_Dir/Sub_Dir/Under_Dir 
```
# 文件处理

## 权限

umask、chmod、chown、chgrp

Linux命令行与shell脚本编程大全.第3版 7.2

## 复制
```
$ cp -R Scripts/ Mod_Scripts
```
## 创建
```
[huawei@10 bin]$ touch 1
[huawei@10 bin]$ >2
[huawei@10 bin]$ ll
total 12
-rw-rw-r--. 1 huawei huawei   0 Aug 30 13:20 1
-rw-rw-r--. 1 huawei huawei   0 Aug 30 13:21 2
```
## 文件信息
```
$ file my_file 
```
## 文件类型
分辨内建命令、别名等等
```
[huawei@10 postdb]$ type source
source is a shell builtin
[huawei@10 postdb]$ type ll
ll is aliased to `ls -l --color=auto'
[huawei@10 postdb]$ type date
date is /usr/bin/date
```
## 文件别名
```
[huawei@10 postdb]$ alias
alias egrep='egrep --color=auto'
alias fgrep='fgrep --color=auto'
alias grep='grep --color=auto'
alias l.='ls -d .* --color=auto'
alias ll='ls -l --color=auto'
alias ls='ls --color=auto'
alias perlll='eval `perl -Mlocal::lib`'
alias vi='vim'
alias which='alias | /usr/bin/which --tty-only --read-alias --show-dot --show-tilde'

创建别名
$ alias li='ls -li' 
```
## 文件内容
cat，more，less，tail，head
# 文本处理

## 文件内容转换 tr
转换大小写、删除指定字符、去重等等
## 按列裁剪内容 cut
https://blog.csdn.net/yangshangwei/article/details/52563123
按空格切
```
[huawei@10 bin]$ cat data
#nam age sex
zhang 18 f
li 20 m
liu 80 f
zhao 33 m
wang 29 m
[huawei@10 bin]$ cut -d ' ' -f 3 ./data
sex
f
m
f
m
m
[huawei@10 bin]$
```
按冒号裁剪字段1和5
```
[huawei@10 bin]$ cut -d: -f 1,5 /etc/passwd
root:root
bin:bin
daemon:daemon
adm:adm
lp:lp
sync:sync
```
裁剪字段6
```
[huawei@10 bin]$ cut -d: -f 6 /etc/passwd
/root
/bin
/sbin
/var/adm
/var/spool/lpd
/sbin
```
裁剪前10列
```

[huawei@10 bin]$ ll|cut -c 1-10
total 12
-rw-rw-r--
-rwxrwxr-x
-rwxrwxr-x
```
## 排序sort
```
按数字反序
[huawei@10 postdb]$  du -sh *|sort -nr
560K    configure
432K    config.log
252K    config
112M    tmp_install
98M     thirdparty
84K     configure.in
79M     dest
64K     INSTALL
40K     config.status
12M     doc
12M     contrib
8.0K    GNUmakefile.in
8.0K    GNUmakefile
4.0K    README
4.0K    Makefile
4.0K    HISTORY
4.0K    corebuild.sh
4.0K    COPYRIGHT
4.0K    configure_release.sh
4.0K    configure_debug.sh
4.0K    build_release.sh
4.0K    build_debug.sh
1.3G    src

基于第三列排序
$ sort -t ':' -k 3 -n /etc/passwd 
root:x:0:0:root:/root:/bin/bash 
bin:x:1:1:bin:/bin:/sbin/nologin 
daemon:x:2:2:daemon:/sbin:/sbin/nologin 
adm:x:3:4:adm:/var/adm:/sbin/nologin 
lp:x:4:7:lp:/var/spool/lpd:/sbin/nologin 
sync:x:5:0:sync:/sbin:/bin/sync 
shutdown:x:6:0:shutdown:/sbin:/sbin/shutdown 
halt:x:7:0:halt:/sbin:/sbin/halt 
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin 
news:x:9:13:news:/etc/news: 
uucp:x:10:14:uucp:/var/spool/uucp:/sbin/nologin 
operator:x:11:0:operator:/root:/sbin/nologin 
```
## 去重uniq
输入得是sort后的数据
```
[huawei@10 bin]$ cat data
#nam age sex
zhang 18 f
li 20 m
liu 80 f
zhao 33 m
wang 29 m
[huawei@10 bin]$ cut -d ' ' -f 3 ./data
sex
f
m
f
m
m
[huawei@10 bin]$ cut -d ' ' -f 3 ./data | sort|uniq -c
      2 f
      3 m
      1 sex
[huawei@10 bin]$ cut -d ' ' -f 3 ./data | sort|uniq
f
m
sex
[huawei@10 bin]$ cut -d ' ' -f 3 ./data | sort|uniq -d
f
m
[huawei@10 bin]$ cut -d ' ' -f 3 ./data | sort|uniq -u
sex

```
## 排版fmt
可以执行每行总列数，并不会切割单词
```
[huawei@10 bin]$ cat ./data|fmt -w 20
#nam age sex zhang
18 f li 20 m liu
80 f zhao 33 m wang
29 m
[huawei@10 bin]$ cat ./data|fmt
#nam age sex zhang 18 f li 20 m liu 80 f zhao 33 m wang 29 m
```
## 统计行数-l、字数-w、字节数-c
```
[huawei@10 bin]$ cat data |wc
      6      18      61
```
# 链接文件
## 软链接

```
[huawei@10 bin]$ ln -s data sdata
[huawei@10 bin]$ ll
total 12
-rw-rw-r--. 1 huawei huawei   0 Aug 30 13:20 1
-rw-rw-r--. 1 huawei huawei   0 Aug 30 13:21 2
drwxrwxr-x. 3 huawei huawei  15 Aug 30 13:26 a
drwxrwxr-x. 4 huawei huawei  25 Aug 30 13:27 a1
-rw-rw-r--. 1 huawei huawei  61 Aug 30 11:47 data
-rwxrwxr-x. 1 huawei huawei  23 Aug 30 07:48 nusers
-rwxrwxr-x. 1 huawei huawei 163 Aug 30 08:28 pass
lrwxrwxrwx. 1 huawei huawei   4 Aug 30 13:29 sdata -> data
[huawei@10 bin]$ ls -i *data
403352130 data  403352132 sdata
编号不同即非同一文件
```
## 硬链接
```
[huawei@10 bin]$ ln  data hdata
[huawei@10 bin]$ ls -li *data
403352130 -rw-rw-r--. 2 huawei huawei 61 Aug 30 11:47 data
403352130 -rw-rw-r--. 2 huawei huawei 61 Aug 30 11:47 hdata
403352132 lrwxrwxrwx. 1 huawei huawei  4 Aug 30 13:29 sdata -> data

编号相同是同一文件
```
# 磁盘

## 分区

fdisk

Linux命令行与shell脚本编程大全.第3版 第八章

## 挂在与卸载
mount、umount
## 剩余空间
df磁盘情况、du目录情况
```
[huawei@10 bin]$ df -h
Filesystem               Size  Used Avail Use% Mounted on
devtmpfs                 1.9G     0  1.9G   0% /dev
tmpfs                    1.9G  8.0K  1.9G   1% /dev/shm
tmpfs                    1.9G  9.6M  1.9G   1% /run
tmpfs                    1.9G     0  1.9G   0% /sys/fs/cgroup
/dev/mapper/centos-root   50G  7.5G   43G  15% /
/dev/sda1               1014M  186M  829M  19% /boot
/dev/mapper/centos-home  146G  3.5G  142G   3% /home
tmpfs                    379M   44K  379M   1% /run/user/1000
/dev/sr0                  59M   59M     0 100% /run/media/huawei/VBox_GAs_6.1.26

[huawei@10 postdb]$  du -sh *
4.0K    build_debug.sh
4.0K    build_release.sh
252K    config
432K    config.log
40K     config.status
560K    configure
4.0K    configure_debug.sh
84K     configure.in
4.0K    configure_release.sh
12M     contrib
4.0K    COPYRIGHT
4.0K    corebuild.sh
79M     dest
12M     doc
8.0K    GNUmakefile
8.0K    GNUmakefile.in
4.0K    HISTORY
64K     INSTALL
4.0K    Makefile
4.0K    README
1.2G    src
98M     thirdparty
112M    tmp_install
```
# GREP
```
[huawei@10 postdb]$ grep hua /etc/passwd
huawei:x:1000:1000:huawei:/home/huawei:/bin/bash

取反
[huawei@10 postdb]$ grep -v hua /etc/passwd
root:x:0:0:root:/root:/bin/bash
bin:x:1:1:bin:/bin:/sbin/nologin
daemon:x:2:2:daemon:/sbin:/sbin/nologin
adm:x:3:4:adm:/var/adm:/sbin/nologin

行号
[huawei@10 postdb]$ grep -n hua /etc/passwd
48:huawei:x:1000:1000:huawei:/home/huawei:/bin/bash

匹配总数
[huawei@10 postdb]$ grep -c hua /etc/passwd
1

多个条件
[huawei@10 postdb]$ grep -e  hua -e root  /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
huawei:x:1000:1000:huawei:/home/huawei:/bin/bash

```
# SED
# AWK

# 压缩与归档
https://www.cnblogs.com/h2zZhou/p/10425590.html

# 数组变量

# 打印

## echo

## printf

# 命令替换

shell脚本中最有用的特性之一就是可以从命令输出中提取信息，并将其赋给变量，两种方式如下

```
[huawei@n148 ~]$ a=`date`
[huawei@n148 ~]$ echo $a
Thu Sep 23 11:43:08 CST 2021
[huawei@n148 ~]$ b=$(date)
[huawei@n148 ~]$ echo $b
Thu Sep 23 11:43:21 CST 2021
```

# 管道与重定向

## 输出、输入重定向

## 管道

# 数学运算

## expr

## 方括号

## 浮点计算bc

# 退出

## $?

一个成功结束的命令的退出状态码是0。如果一个命令结束时有错误，退出状态码就是一个正数值。

```
$ date %t 
date: invalid date '%t' 
$ echo $? 
1 
```



## exit

可以在脚本里指定退出码用于外界的返回判断

```
$ cat test13 
#!/bin/bash 
# testing the exit status 
var1=10 
var2=30 
var3=$[$var1 + $var2] 
echo The answer is $var3 
exit 5 
```

# if语句

https://www.cnblogs.com/liudianer/p/12071476.html

## if then

```
read a
read b
if (( $a == $b ))
then
  echo "a=b"
fi 
```

## if then else

```
echo -n "input:"
read user
if
#多条指令,这些命令之间相当于“and”（与）
grep $user /etc/passwd >/tmp/null      
who -u | grep $user
then #上边的指令都执行成功,返回值$?为0，0为真，运行then
 echo "$user has logged"
else #指令执行失败，$?为1，运行else                            
 echo "$user has not logged"
fi  
```

## elif

```
#!/bin/sh
SYSTEM=`uname  -s`
if [ $SYSTEM = "Linux" ] ; then
   echo "Linux"
elif
    [ $SYSTEM = "FreeBSD" ] ; then
   echo "FreeBSD"
elif
    [ $SYSTEM = "Solaris" ] ; then
    echo "Solaris"
else
    echo  "What?"
fi
```

## case

```
case $USER in 
rich | huaw) 
 echo "Welcome, $USER" 
 echo "Please enjoy your visit";; 
testing) 
 echo "Special testing account";; 
jessica) 
 echo "Do not forget to log off when you're done";; 
*) 
 echo "Sorry, you are not allowed here";; 
esac 
```

## 整数比较 整数比较 整数比较

n1 -eq n2 检查n1是否与n2相等
n1 -ge n2 检查n1是否大于或等于n2
n1 -gt n2 检查n1是否大于n2
n1 -le n2 检查n1是否小于或等于n2
n1 -lt n2 检查n1是否小于n2
n1 -ne n2 检查n1是否不等于n2

```
value1=10 
value2=11 
# 
if [ $value1 -gt 5 ] 
then 
 echo "The test value $value1 is greater than 5" 
fi 
# 
if [ $value1 -eq $value2 ] 
then 
 echo "The values are equal" 
else 
 echo "The values are different" 
fi 
```

## 字符串比较

str1 = str2 检查str1是否和str2相同
str1 != str2 检查str1是否和str2不同
str1 < str2 检查str1是否比str2小
str1 > str2 检查str1是否比str2大
-n str1 检查str1的长度是否非0 
-z str1 检查str1的长度是否为0

```
testuser=huawei
# 
if [ $USER = $testuser ] 
then 
 echo "Welcome $testuser" 
fi

val1=Testing 
val2=testing 
if [ $val1 \> $val2 ] 
then 
 echo "$val1 is greater than $val2" 
else 
 echo "$val1 is less than $val2" 
fi 

val1=testing 
val2='' 
# 
if [ -n $val1 ] 
then 
 echo "The string '$val1' is not empty" 
else 
 echo "The string '$val1' is empty" 
fi 
# 
if [ -z $val2 ] 
then 
 echo "The string '$val2' is empty" 
else 
 echo "The string '$val2' is not empty" 
fi 
# 
if [ -z $val3 ] 
then 
 echo "The string '$val3' is empty" 
else 
 echo "The string '$val3' is not empty" 
fi
```

## 文件比较

Linux命令行与shell脚本编程大全.第3版 第12章

检查目录、检查对象是否存在、检查文件、检查是否可读可写可执行、是否空文件、文件主人和组，日期时间等

## ((   ))

用于任意的数学赋值或比较表达式

```
val1=10 
# 
if (( $val1 ** 2 > 90 )) 
then 
 (( val2 = $val1 ** 2 )) 
 echo "The square of $val1 is $val2" 
fi
```

## [[   ]]

字符串比较，可以定义一个正则表达式

```
if [[ $USER == hua* ]] 
then 
 echo "Hello $USER" 
else 
 echo "Sorry, I do not know you" 
fi
```

