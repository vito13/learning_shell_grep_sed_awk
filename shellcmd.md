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
## 创建多用户
```
input="users.csv" 
touch $input
echo "rich,Richard Blum" >> $input
echo "christine,Christine Bresnahan" >> $input
echo "barbara,Barbara Blum" >> $input
echo "tim,Timothy Bresnahan" >> $input

while IFS=',' read -r userid name 
do 
 echo "adding $userid" 
 sudo useradd -c "$name" -m $userid 
done < "$input" 

tail /etc/passwd

while IFS=',' read -r userid name 
do 
 echo "deling $userid" 
 sudo userdel -r "$userid"
done < "$input" 
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

# if

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

- n1 -eq n2 检查n1是否与n2相等
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
# for
## 迭代列表
```
#!/bin/bash 
for test in Alabama Alaska Arizona Arkansas California Colorado 
do 
 echo "The next state is $test" 
done 
echo "The last state we visited was $test" 

打印出
The next state is Alabama
The next state is Alaska
The next state is Arizona
The next state is Arkansas
The next state is California
The next state is Colorado
The last state we visited was Colorado
```
## 字符串内含’
```

# for test in I don't know if this'll work 错误的，需要转义’
for test in I don\'t know if "this'll" work 
do 
 echo "word:$test" 
done

打印出
word:I
word:don't
word:know
word:if
word:this'll
word:work
```
## 字符串内含空格
```
#!/bin/bash 
# for test in Nevada New Hampshire New Mexico New York North Carolina 2个词的需要加引号
for test in Nevada "New Hampshire" "New Mexico" "New York" 
do 
 echo "Now going to $test" 
done

打印出
Now going to Nevada
Now going to New Hampshire
Now going to New Mexico
Now going to New York
```
## 列表是变量
```
list="Alabama Alaska Arizona Arkansas Colorado" 
list=$list" Connecticut" 
for state in $list 
do 
 echo "Have you ever visited $state?" 
done 

打印出
Have you ever visited Alabama?
Have you ever visited Alaska?
Have you ever visited Arizona?
Have you ever visited Arkansas?
Have you ever visited Colorado?
Have you ever visited Connecticut?
```
## 按分隔符迭代文本文件
默认换行分割
```
file="/etc/passwd" 
for state in $(cat $file) 
do 
 echo "Visit beautiful $state" 
done 
```
指定分割符号，换行符、冒号、分号和双引号作为字段分隔符
```
file="/etc/passwd" 
IFSOLD=$IFS
IFS=$'\n':;'\"'
for state in $(cat $file) 
do 
 echo "Visit beautiful $state" 
done 
IFS=$IFSOLD 
```
## 嵌套循环分解/etc/passwd
```
IFS.OLD=$IFS 
IFS=$'\n' 
for entry in $(cat /etc/passwd) 
do 
 echo "Values in $entry –" 
 IFS=: 
 for value in $entry 
 do 
 echo " $value" 
 done 
done
```
## 用通配符遍历目录
```
for file in /home/huawei/pl*gr*nd/* 
do 
 if [ -d "$file" ] 
 then 
 echo "$file is a directory" 
 elif [ -f "$file" ] 
 then 
 echo "$file is a file" 
 fi 
done 
```
## 查找可执行文件
```
IFS=: 
for folder in $PATH 
do 
 echo "$folder:" 
 for file in $folder/* 
 do 
 if [ -x $file ] 
 then 
 echo " $file" 
 fi 
 done 
done
```
## C语言风格
```
for (( a=1, b=10; a <= 10; a++, b-- )) 
do 
 echo "$a - $b" 
done 
```
# while
## 单条件
```
var1=10
while [ $var1 -gt 0 ] 
do 
 echo $var1 
 var1=$[ $var1 - 1 ] 
done
```
## 多条件
while条件多句，以最后一句返回0为t。先打印，后比较，不要连在一行。
```
var1=10
while echo $var1 
[ $var1 -ge 0 ] 
do 
 echo "This is inside the loop" 
 var1=$[ $var1 - 1 ] 
done 
```
# until
条件为!0才循环
## 单条件
```
var1=100 
until [ $var1 -eq 0 ] 
do 
 echo $var1 
 var1=$[ $var1 - 25 ] 
done 

[huawei@n148 playground]$ sh ./t.sh 
100
75
50
25
```
## 多条件
until条件多句，以最后一句返回!0为t。先打印，后比较，不要连在一行。
```
var1=100 
until echo $var1 
[ $var1 -eq 0 ] 
do 
 echo Inside the loop: $var1 
 var1=$[ $var1 - 25 ] 
done 

[huawei@n148 playground]$ sh ./t.sh 
100
Inside the loop: 100
75
Inside the loop: 75
50
Inside the loop: 50
25
Inside the loop: 25
0
```
# 循环输出done重定向
## 重定向输出到文件
```
for file in /home/huawei/*
do 
 if [ -d "$file" ] 
 then
  echo "$file is a directory"
 else
  echo "$file is a file"
 fi 
done > output.txt
```
```
for (( a = 1; a < 10; a++ )) 
do 
 echo "The number is $a" 
done > test23.txt 
echo "The command is finished." 
```
## 重定向输出到命令
```
for state in "North Dakota" Connecticut Illinois Alabama Tennessee 
do 
 echo "$state is the next place to go" 
done | sort 
echo "This completes our travels" 
```
## 重定向输入
```
```
# break
## 退出for
```
for var1 in 1 2 3 4 5 6 7 8 9 10 
do 
 if [ $var1 -eq 5 ] 
 then 
 break 
 fi 
 echo "Iteration number: $var1" 
done 
echo "The for loop is completed" 
```
## 退出while
```
var1=1 
while [ $var1 -lt 10 ] 
do 
 if [ $var1 -eq 5 ] 
 then 
 break 
 fi 
 echo "Iteration: $var1" 
 var1=$[ $var1 + 1 ] 
done 
echo "The while loop is completed" 
```
## 跳出单层
```
for (( a = 1; a < 4; a++ )) 
do 
 echo "Outer loop: $a" 
 for (( b = 1; b < 100; b++ )) 
 do 
 if [ $b -eq 5 ] 
 then 
 break 
 fi 
  echo " Inner loop: $b" 
 done 
done 
```
## 跳出多部
break后面的2就是跳出2层
```
for (( a = 1; a < 4; a++ )) 
do 
 echo "Outer loop: $a" 
 for (( b = 1; b < 100; b++ )) 
 do 
 if [ $b -gt 4 ] 
 then 
 break 2 
 fi 
 echo " Inner loop: $b" 
 done 
done 
```
# continue
## 单层
```
for (( var1 = 1; var1 < 15; var1++ )) 
do 
 if [ $var1 -gt 5 ] && [ $var1 -lt 10 ] 
 then 
 continue 
 fi 
 echo "Iteration number: $var1" 
done 
```
## 多层
```
for (( a = 1; a <= 5; a++ )) 
do 
 echo "Iteration $a:" 
 for (( b = 1; b < 3; b++ )) 
 do 
 if [ $a -gt 2 ] && [ $a -lt 4 ] 
 then 
  continue 2 
 fi 
 var3=$[ $a * $b ] 
 echo " The result of $a * $b is $var3" 
 done 
done 
```
# 命令行参数
## 1个整数参数
```
factorial=1 
for (( number = 1; number <= $1 ; number++ )) 
do 
 factorial=$[ $factorial * $number ] 
done 
echo The factorial of $1 is $factorial 
```
## 2个整数参数
```
total=$[ $1 * $2 ] 
echo The first parameter is $1. 
echo The second parameter is $2. 
echo The total value is $total. 
```
## 1个带有空格的字符串参数
单引号、双引号都行，如果没有空格就不用加引号了
```
echo Hello $1, glad to meet you. 

[huawei@n148 playground]$ sh ./t.sh 'hua wei'
Hello hua wei, glad to meet you.
```
## 多于9个参数
要这样${10}才行
```
total=$[ ${10} * ${11} ] 
echo The tenth parameter is ${10} 
echo The eleventh parameter is ${11} 
echo The total is $total 

[huawei@n148 playground]$ sh ./t.sh 1 2 3 4 5 6 7 8 9 10 11 12 13
The tenth parameter is 10
The eleventh parameter is 11
The total is 110
```
## 脚本名称
basename可以获取不带路径的名称，然后根据不同的脚本名称调用不同的逻辑
```
name=$(basename $0) 
if [ $name = "addem" ] 
then 
 total=$[ $1 + $2 ] 
elif [ $name = "multem" ] 
then 
 total=$[ $1 * $2 ] 
fi 
echo 
echo The calculated value is $total 
```
## 检查是否有参数
使用-n判断￥1长度为非0
```
if [ -n "$1" ] 
then 
 echo Hello $1, glad to meet you. 
else 
 echo "Sorry, you did not identify yourself. " 
fi 
```
## 参数总个数
$#代表总个数
```
if [ $# -ne 2 ] 
then 
 echo Usage: test9.sh a b 
else 
 total=$[ $1 + $2 ] 
 echo The total is $total 
fi 
echo The last parameter was ${!#}
```
## 最末参数
使用${!#}表示,代码见参数总个数
## 所有参数
```
count=1 
for param in "$*"  # 所有参数连在一起的字符串
do 
 echo "\$* Parameter #$count = $param" 
 count=$[ $count + 1 ] 
done 
echo
count=1 
for param in "$@"  # 单独处理每个参数
do 
 echo "\$@ Parameter #$count = $param" 
 count=$[ $count + 1 ] 
done 

[huawei@n148 playground]$ t.sh  a b c d
$* Parameter #1 = a b c d  

$@ Parameter #1 = a  
$@ Parameter #2 = b
$@ Parameter #3 = c
$@ Parameter #4 = d
```
## 移出参数shift
按顺序移出
```
count=1 
while [ -n "$1" ] 
do 
 echo "Parameter #$count = $1" 
 count=$[ $count + 1 ] 
 shift 
done 

[huawei@n148 playground]$ t.sh  a b c d
Parameter #1 = a
Parameter #2 = b
Parameter #3 = c
Parameter #4 = d
```
移出指定位置参数
```
echo "The original parameters: $*" 
shift 2 
echo "Here's the new first parameter: $1" 

[huawei@n148 playground]$ t.sh  a b c d
The original parameters: a b c d
Here's the new first parameter: c
```
## 分解选项与参数
--表示选项列表结束。之后，脚本就可以放心地将剩下的命令行参数当作参数，而不是选项来处理  
具体代码进化过程见Linux命令行与shell脚本编程大全.第3版 14.4.1

```
echo 
while [ -n "$1" ] 
do 
 case "$1" in 
 -a) echo "Found the -a option";; 
 -b) param="$2" 
 echo "Found the -b option, with parameter value $param" 
 shift ;; 
 -c) echo "Found the -c option";; 
 --) shift 
 break ;; 
 *) echo "$1 is not an option";; 
 esac 
 shift 
done 
# 
count=1 
for param in "$@" 
do 
 echo "Parameter #$count: $param" 
 count=$[ $count + 1 ] 
done 

[huawei@n148 playground]$ t.sh  -a -b test1 -d
Found the -a option
Found the -b option, with parameter value test1
-d is not an option
```
此例无法分解多个选项合在一起的样式
## getopt & getopts
# 输入read
## read到一个变量
所有内容都到这个变量里
```
read -p "Please enter your age: " age 
days=$[ $age * 365 ] 
echo "That makes you over $days days old! " 
```
## read到多个变量
第一个空格前的到第一个变量，剩余的都在第二个变量里
```
read -p "Enter your name: " first last 
echo "Checking data for $last, $first…" 

[huawei@n148 playground]$ t.sh
Enter your name: a b c d e
Checking data for b c d e, a…
```
## read到REPLY环境变量里
REPLY环境变量会保存输入的所有数据，可以在shell脚本中像其他变量一样使用
```
read -p "Enter your name: " 
echo 
echo Hello $REPLY, welcome to my program. 

[huawei@n148 playground]$ t.sh
Enter your name: a b c
Hello a b c, welcome to my program.
```
## read计时
-t选项指定了read命令等待
输入的秒数。当计时器过期后，read命令会返回一个非零退出状态码
```
if read -t 5 -p "Please enter your name: " name 
then 
 echo "Hello $name, welcome to my script" 
else 
 echo 
 echo "Sorry, too slow! " 
fi 
```
## read无需回车
将-n选项和值1一起使用，告诉read命令在接受单个字符后退出。只要按下单个字符
回答后，read命令就会接受输入并将它传给变量，无需按回车键
```
read -n1 -p "Do you want to continue [Y/N]? " answer 
case $answer in 
Y | y) echo 
 echo "fine, continue on…";; 
N | n) echo 
 echo OK, goodbye 
 exit;; 
esac 
echo "This is the end of the script" 
```
## 密码形式read
-s选项可以避免在read命令中输入的数据出现在显示器上（实际上，数据会被显示，只是
read命令会将文本颜色设成跟背景色一样）
```
echo 
echo "Is your password really $pass? " 
```
## read文件每一行
while循环会持续通过read命令处理文件中的行，直到read命令以非零退出状态码退出
```
count=1 
cat users.csv | while read line 
do 
 echo "Line $count: $line" 
 count=$[ $count + 1] 
done 
echo "Finished processing the file" 
```
