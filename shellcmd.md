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
## useradd、userdel 添加、删除用户

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
## 创建多用户脚本
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
## 修改用户信息
* usermod 修改用户账户的字段，还可以指* 定主要组以及附加组的所属关系  
* passwd 修改已有用户的密码  
* chpasswd 从文件中读取登录名密码对，并* 更新密码  
* chage 修改密码的过期日期  
* chfn 修改用户账户的备注信息  
* chsh 修改用户账户的默认登录shell   

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

## env、printenv、set 打印环境变量
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
## unset 定义与取消用户变量

```
[huawei@10 postdb]$ my_variable="I am Global now"
[huawei@10 postdb]$ echo $my_variable
I am Global now
[huawei@10 postdb]$ unset my_variable
[huawei@10 postdb]$ echo $my_variable
```
## export 导出变量到父shell
```
$ my_variable="I am Global now"
$ export my_variable
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
# 外部命令与内建命令

## 外部命令
也称为文件系统命令，是存在于 bash shell 之外的程序。它们并不是 shell 程序的一部分。外部命令程序通常位于/bin、/usr/bin、/sbin 或/usr/sbin 中。  
ps 就是一个外部命令。可以使用 which 和 type 命令找到它。
```
[huawei@n148 playground]$ ll $(which ps)
-rwxr-xr-x 1 root root 100112 Oct  1  2020 /usr/bin/ps
[huawei@n148 playground]$ type ps
ps is hashed (/usr/bin/ps)
```
当外部命令执行时，会创建出一个子进程。这种操作被称为衍生（forking）。外部命令 ps 很方便显示出它的父进程以及自己所对应的衍生子进程。观察下面ps-f的ppid即可找到父进程号
```
[huawei@n148 playground]$ ps -f
UID         PID   PPID  C STIME TTY          TIME CMD
huawei    69608  69599  0 09:03 pts/6    00:00:00 -bash
huawei    69623  69608  0 11:08 pts/6    00:00:00 bash
huawei    74347  69623  0 11:12 pts/6    00:00:00 sleep 3000
huawei    86232  69623  0 11:24 pts/6    00:00:00 ps -f
```
当进程必须执行衍生操作时，它需要花费时间和精力来设置新子进程的环境。所以说，外部命令多少还是有代价的。父子进程可以通过信号进行通信
## 内建命令
和外部命令的区别在于内建命令不需要使用子进程来执行。它们已经和 shell 编译成了一体，作为 shell 工具的组成部分存在。不需要借助外部程序文件来运行
```
[huawei@n148 playground]$ type cd
cd is a shell builtin
[huawei@n148 playground]$ type exit
exit is a shell builtin
```
因为既不需要通过衍生出子进程来执行，也不需要打开程序文件，内建命令的执行速度要更快，效率也更高。要注意，有些命令有多种实现。两种实现略有不同。要查看命令的不同实现，使用 type 命令的-a 选项。命令 type -a 显示出了每个命令的两种实现。注意，which 命令只显示出了外部命令文件。对于有多种实现的命令，如果想要使用其外部命令实现，直接指明对应的文件就可以了。例如，要使用外部命令 pwd，可以输入/bin/pwd
```
[huawei@n148 playground]$ type -a echo
echo is a shell builtin
echo is /usr/bin/echo
[huawei@n148 playground]$ which echo
/usr/bin/echo
[huawei@n148 playground]$ type -a pwd
pwd is a shell builtin
pwd is /usr/bin/pwd
[huawei@n148 playground]$ which pwd
/usr/bin/pwd
```
# history 历史记录
# 日期时间 date
```
[huawei@n148 ~]$ date +%F
2021-10-11
[huawei@n148 ~]$ date +%T
11:48:22
[huawei@n148 ~]$ DATE=`date +%F | sed 's/-//g'``date +%T | sed 's/://g'`
[huawei@n148 ~]$ echo $DATE
20211011114836
[huawei@n148 ~]$ date +%Y%m%d%H%M%S
20211011115018
[huawei@n148 ~]$ echo ./reslog-"`date +%F_%T`".txt
./reslog-2021-10-11_11:52:44.txt

```
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
几种方法
```
[huawei@10 bin]$ touch f1 f2 f3
[huawei@10 bin]$ >2
[huawei@n148 ~]$ echo "hello, world" > regular_file
```
## 文件信息
确定文件或目录等的文件类型
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
# alias 别名
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

fdisk、fsck 

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
grep的全称为： Global search Regular Expression and Print out the line。全称中的”Global search”为全局搜索之意。全称中的”Regular Expression”表示正则表达式。
–color=auto 或者 –color：表示对匹配到的文本着色显示

-i：在搜索的时候忽略大小写

-n：显示结果所在行号

-c：统计匹配到的行数，注意，是匹配到的总行数，不是匹配到的次数

-o：只显示符合条件的字符串，但是不整行显示，每个符合条件的字符串单独显示一行

-v：输出不带关键字的行（反向查询，反向匹配）

-w：匹配整个单词，如果是字符串中包含这个单词，则不作匹配

-Ax：在输出的时候包含结果所在行之后的指定行数，这里指之后的x行，A：after

-Bx：在输出的时候包含结果所在行之前的指定行数，这里指之前的x行，B：before

-Cx：在输出的时候包含结果所在行之前和之后的指定行数，这里指之前和之后的x行，C：context

-e：实现多个选项的匹配，逻辑or关系

-q：静默模式，不输出任何信息，当我们只关心有没有匹配到，却不关心匹配到什么内容时，我们可以使用此命令，然后，使用”echo $?”查看是否匹配到，0表示匹配到，1表示没有匹配到。

-P：表示使用兼容perl的正则引擎。

-E：使用扩展正则表达式，而不是基本正则表达式，在使用”-E”选项时，相当于使用egrep。
## 查找、不分区大小写-i、行号-n、匹配总数-c、仅显示关键字-o
```
[huawei@n148 reg]$ cat testgrep 
zsy test
zsythink

www.zsything.net
TEST 123
Zsy's articles
grep Grep
abc
abc123bac

[huawei@n148 reg]$ grep "TEST" testgrep 
TEST 123
[huawei@n148 reg]$ grep -i "TEST" testgrep 不分区大小写
zsy test
TEST 123
[huawei@n148 reg]$ grep -in "TEST" testgrep 行号
1:zsy test
5:TEST 123
[huawei@n148 reg]$ grep -ic  "TEST" testgrep 匹配总数
2
[huawei@n148 reg]$ grep -i -o "TEST" testgrep 仅显示关键字
test
TEST
```

## 显示结果上下文，上n行 -Bn，下n行 -An
可以2个都使用
```
[huawei@n148 reg]$ cat testgrep1
姓名：朱双印
年龄：18
颜值：自认为爆表，其实很low

姓名：王尼玛
年龄：30
颜值：带着头套，看不到脸

姓名：王尼美
年龄：18
颜值：目测很丑
[huawei@n148 reg]$ grep -B1 "年龄：18" testgrep1
姓名：朱双印
年龄：18
--
姓名：王尼美
年龄：18

[huawei@n148 reg]$ grep -A1 -B1 "年龄：18" testgrep1
姓名：朱双印
年龄：18
颜值：自认为爆表，其实很low
--
姓名：王尼美
年龄：18
颜值：目测很丑

```
## 精准匹配-w、不包含-v、多重条件-e
```
huawei@n148 reg]$ grep "zsy"  testgrep 匹配出很多。。。
zsy test
zsythink
www.zsything.net
123zsy123
[huawei@n148 reg]$ grep -w "zsy"  testgrep 匹配word级别
zsy test
[huawei@n148 reg]$ grep -v "zsy"  testgrep 匹配不包含关键字

TEST 123
Zsy's articles
grep Grep
abc
abc123bac

[huawei@n148 reg]$ grep -e "abc" -e "test"  testgrep 多重匹配
zsy test
abc
abc123bac
```
## 静默执行-q与结果$?
无输出，仅能通过$?获取是否匹配成功
```
[huawei@n148 reg]$ grep -q "test"  testgrep
[huawei@n148 reg]$ echo $?
0
[huawei@n148 reg]$ grep -q "11111111111"  testgrep
[huawei@n148 reg]$ echo $?
1
```
## 简单正则匹配 -P
使用perl的正则样式，标准的是-E
```
[huawei@n148 reg]$ cat testgrep2
sdfasdf
zsything@yeah.net
asdfasdfasdfasdf@
@asdfv
[huawei@n148 reg]$ grep -P "[a-zA-Z0-9-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+"  testgrep2
zsything@yeah.net

```
# 正则 Regular Expression
正则表达式，又称 规则表达式。正则表达式的英语原文为：Regular Expression，常简写为regex、regexp或RE，正则表达式是计算机科学的一个概念。正则表达式通常被用来检索、替换那些符合某个模式(规则)的文本。  
```
^：表示锚定行首，此字符后面的任意内容必须出现在行首，才能匹配。
$：表示锚定行尾，此字符前面的任意内容必须出现在行尾，才能匹配。
^$：表示匹配空行，这里所描述的空行表示”回车”，而”空格”或”tab”等都不能算作此处所的空行。
^abc$：表示abc独占一行时，会被匹配到。
\<或者\b ：匹配单词边界，表示锚定词首，其后面的字符必须作为单词首部出现。
\>或者\b ：匹配单词边界，表示锚定词尾，其前面的字符必须作为单词尾部出现。
\B：匹配非单词边界，与\b正好相反。
*  表示前面的字符连续出现任意次，包括0次。
.  表示任意单个字符。
.* 表示任意长度的任意字符，与通配符中的*的意思相同。
\? 表示匹配其前面的字符0或1次
\+ 表示匹配其前面的字符至少1次，或者连续多次，连续次数上不封顶。
\{n\} 表示前面的字符连续出现n次，将会被匹配到。
\{x,y\} 表示之前的字符至少连续出现x次，最多连续出现y次，都能被匹配到，换句话* 只要之前的字符连续出现的次数在x与y之间，即可被匹配到。
\{,n\} 表示之前的字符连续出现至多n次，最少0次，都会陪匹配到。
\{n,\} 表示之前的字符连续出现至少n次，才会被匹配到.
## 默认匹配，精准匹配，行首^ 行尾$ 空行^$
.  表示匹配任意单个字符
* 表示匹配前面的字符任意次，包括0次
[  ] 表示匹配指定范围内的任意单个字符
[^  ] 表示匹配指定范围外的任意单个字符
 
[[:alpha:]]  表示任意大小写字母
[[:lower:]]  表示任意小写字母
[[:upper:]]  表示任意大写字母
[[:digit:]]  表示0到9之间的任意单个数字（包括0和9）
[[:alnum:]]  表示任意数字或字母
[[:space:]]  表示任意空白字符，包括"空格"、"tab键"等。
[[:punct:]]  表示任意标点符号
 
[0-9]与[[:digit:]]等效
[a-z]与[[:lower:]]等效
[A-Z]与[[:upper:]]等效
[a-zA-Z]与[[:alpha:]]等效
[a-zA-Z0-9]与[[:alnum:]]等效
 
[^0-9]与[^[:digit:]]等效
[^a-z]与[^[:lower:]]等效
[^A-Z]与[^[:upper:]]等效
[^a-zA-Z]与[^[:alpha:]]等效
[^a-zA-Z0-9]与[^[:alnum:]]等效
 
#简短格式并非所有正则表达式解析器都可以识别
\d 表示任意单个0到9的数字
\D 表示任意单个非数字字符
\t 表示匹配单个横向制表符（相当于一个tab键）
\s表示匹配单个空白字符，包括"空格"，"tab制表符"等
\S表示匹配单个非空白字符
```

```
[huawei@n148 reg]$ cat regex
hello world
hello

hi hello

hello ,zsy

[huawei@n148 reg]$ grep 'hello' regex
hello world
hello
hi hello
hello ,zsy
[huawei@n148 reg]$ grep '^hello' regex
hello world
hello
hello ,zsy
[huawei@n148 reg]$ grep 'hello$' regex
hello
hi hello
[huawei@n148 reg]$ grep '^hello$' regex
hello
[huawei@n148 reg]$ grep '^$' regex



[huawei@n148 reg]$
```

## 词首\b 词尾\b 非词首\B 非词尾\B
非词首非词尾即一个字符串里包含了要找的子串
```

[huawei@n148 reg]$ cat -n REG
     1  abchello world
     2  abc helloabc abc
     3  abc abcabchelloabc abc
     4  abchello helloabc hello ahelloa
[huawei@n148 reg]$ grep "\bhello" REG
abc helloabc abc
abchello helloabc hello ahelloa
[huawei@n148 reg]$ grep "hello\b" REG
abchello world
abchello helloabc hello ahelloa
[huawei@n148 reg]$ grep "\bhello\b" REG
abchello helloabc hello ahelloa
[huawei@n148 reg]$ grep "\Bhello" REG
abchello world
abc abcabchelloabc abc
abchello helloabc hello ahelloa
[huawei@n148 reg]$
```

## 匹配指定范围内的连续单个字符
```
\{x\}  表示之前的字符连续出现x次时会被匹配到。  
\{x,y\} 表示之前的字符至少连续出现x次，至多连续出现y次，都可以被匹配到，x与y之间用逗号隔开。  
\{x,\}表示之前的字符至少连续出现x次，或者连续出现次数大于x次，即可被匹配到，上不封顶。  
\{,y\}表示之前的字符至多连续出现y次，或者连续出现次数小于y次，即可被匹配到，最小次数为0次，换句话说，之前的字符连续出现0次到y次，都会被匹配到。  

[huawei@n148 reg]$ cat regex.txt
a a
aa
a aa
bb
bbb
c cc ccc
dddd d dd ddd
ab abc abcc
ef eef eeef
[huawei@n148 reg]$ grep -n "a\{2\}" regex.txt
2:aa
3:a aa
[huawei@n148 reg]$ grep -n "b\{2\}" regex.txt 匹配连续2个b
4:bb
5:bbb
[huawei@n148 reg]$ grep -n "\<b\{2\}\>" regex.txt 精准匹配b开头b结尾连续2个b
4:bb
[huawei@n148 reg]$ grep -n "d\{2,4\}" regex.txt 匹配连续2~4次连续d
7:dddd d dd ddd
[huawei@n148 reg]$ grep -n "d\{2,\}" regex.txt 匹配连续2~无限次连续d
7:dddd d dd ddd
[huawei@n148 reg]$ grep -n "abc\{,2\}" regex.txt 匹配连续0~2次c
8:ab abc abcc
```
## 通配任意字符 . *

```
*表示之前的字符连续出现任意次数（包括0次）
.表示匹配任意单个字符
使用 .* 表示任意长度的任意字符，与通配符中的*所表达的意思一样

[huawei@n148 reg]$ grep -n "e*f" regex.txt	匹配n次e后接f的
9:ef eef eeef
[huawei@n148 reg]$ grep -n "d*" regex.txt 匹配n次d的，包含0次所以匹配了所有行
1:a a
2:aa
3:a aa
4:bb
5:bbb
6:c cc ccc
7:dddd d dd ddd
8:ab abc abcc
9:ef eef eeef

[huawei@n148 reg]$ grep -n "ee." regex.txt 匹配ee开头后跟一个任意字符
9:ef eef eeef
[huawei@n148 reg]$ grep -n "ee.." regex.txt 匹配ee开头后跟儿个任意字符
9:ef eef eeef

[huawei@n148 reg]$ grep -n "a.*" regex.txt 匹配a开头的行
1:a a
2:aa
3:a aa
8:ab abc abcc

```
## 匹配前字符0~1次、匹配前字符1~无限从
```
\? 表示匹配其前面的字符0或1次，换句话说，就是前面的字符要么没有，要么有一个。
\+ 表示匹配其前面的字符至少1次，换句话说，就是前面的字符必须有至少一个。

[huawei@n148 reg]$ grep -n "abc\?" regex.txt
8:ab abc abcc
[huawei@n148 reg]$ grep -n "abc\+" regex.txt
8:ab abc abcc
```
## 字母、大写字母、小写字母、数字、空白、符号
```
[[:alpha:]]  表示任意大小写字母
[[:lower:]]  表示任意小写字母
[[:upper:]]  表示任意大写字母
[[:digit:]]  表示0到9之间的任意单个数字（包括0和9）
[[:alnum:]]  表示任意数字或字母
[[:space:]]  表示任意空白字符，包括”空格”、”tab键”等。
[[:punct:]]  表示任意标点符号

[huawei@n148 reg]$ cat reg1
a
a6d
a89&
a7idai8
abcd
aBdC
aBCD
a123
a1a3
[huawei@n148 reg]$ grep -n "a[[:lower:]]\{3\}" reg1 匹配a后面3个小写字母
5:abcd
[huawei@n148 reg]$ grep -n "a[a-z]\{3\}" reg1 匹配a后面3个小写字母
5:abcd
[huawei@n148 reg]$ grep -n "a[[:upper:]]\{3\}" reg1 匹配a后面3个大写字母
7:aBCD
[huawei@n148 reg]$ grep -n "a[A-Z]\{3\}" reg1 匹配a后面3个大写字母
7:aBCD
[huawei@n148 reg]$ grep -n "a[0-9]\{3\}" reg1 匹配a后面3个数字
8:a123
```
## 匹配指定范围内的任意单个字符[]
```
[ ] 表示匹配指定范围内的任意单个字符，换句话说，就是字符与方括号[ ]内的任意一个字符相同，就可以被匹配到。
[^ ]表示匹配指定范围外的任意单个字符，注意，它与[ ]的含义正好相反。
[^0-9]与[^[:digit:]]等效
[^a-z]与[^[:lower:]]等效
[^A-Z]与[^[:upper:]]等效
[^a-zA-Z]与[^[:alpha:]]等效
[^a-zA-Z0-9]与[^[:alnum:]]等效

[huawei@n148 reg]$ cat reg2
bc
bd
be
bf
bg

[huawei@n148 reg]$ grep -n "b[ceg]" reg2
1:bc
3:be
5:bg


[huawei@n148 reg]$ cat reg3
c3
cB
cC
cd
cE
c$
c#
[huawei@n148 reg]$ grep -n "c[Bd#3]" reg3
1:c3
2:cB
4:cd
7:c#
[huawei@n148 reg]$ grep -n "c[^3BCDE]" reg3
4:cd
6:c$
7:c#
[huawei@n148 reg]$ grep -n "c[^0-9]" reg3 匹配e后跟字符不是数字的
2:cB
3:cC
4:cd
5:cE
6:c$
7:c#

```

## 分组、后向引用
用于匹配连续n次的字符串

```
\( \) 表示分组，我们可以将其中的内容当做一个整体，分组可以嵌套。
\(ab\) 表示将ab当做一个整体去处理。
\1 表示引用整个表达式中第1个分组中的正则匹配到的结果。
\2 表示引用整个表达式中第2个分组中的正则匹配到的结果。

[huawei@n148 reg]$ cat reg6
hello
helloo
hellooo
hellohello
[huawei@n148 reg]$ grep "\(hello\)\{2\}" reg6
hellohello
[huawei@n148 reg]$ cat reg6
hello
helloo
hellooo
hellohello
abefefabefef
[huawei@n148 reg]$ grep "\(hello\)\{2\}" reg6	
hellohello
[huawei@n148 reg]$ grep "\(ab\(ef\)\{2\}\)\{2\}" reg6
abefefabefef

[huawei@n148 reg]$ cat reg6
hello
helloo
hellooo
hellohello
abefefabefef
Hello world Hello
Hiiii world Hiiii
Hello world Hiiii
Hello world Hiiii -- Hiiii
Hiiii world Hello -- Hello

[huawei@n148 reg]$ grep "\(hello\)\{2\}" reg6	匹配2个连续的hello
hellohello
[huawei@n148 reg]$ grep "\(ab\(ef\)\{2\}\)\{2\}" reg6	匹配2个连续的abefef，其中abefef部分类似嵌套
abefefabefef
[huawei@n148 reg]$ grep "H.\{4\} world H.\{4\}" reg6	匹配H开头后跟4个任意字符空格world空格再H开头后跟4个任意字符，此方法比较啰嗦。。。
Hello world Hello
Hiiii world Hiiii
Hello world Hiiii
Hello world Hiiii -- Hiiii
Hiiii world Hello -- Hello
[huawei@n148 reg]$ grep "\(H.\{4\}\) world \1" reg6	向后引用\1就代表前面匹配的内容，找出aba式样的字符串
Hello world Hello
Hiiii world Hiiii
[huawei@n148 reg]$ grep "\(H.\{4\}\) world \(H.\{4\}\) -- \2" reg6	使用\2对应的第二个分组
Hello world Hiiii -- Hiiii
Hiiii world Hello -- Hello

```
![相后引用](https://www.zsythink.net/wp-content/uploads/2017/06/060117_1536_9.png)
![嵌套的分组序号](https://www.zsythink.net/wp-content/uploads/2017/06/060117_1536_13.png)
# SED
# AWK
规则是先模式匹配后执行动作。pattern { action }
## 内建变量
变量 	意义 	默认值
* ARGC 命令行参数的个数 -
* ARGV 命令行参数数组 -
* FILENAME 当前输入文件名 -
* FNR 当前输入文件的记录个数 -
* FS 控制着输入行的字段分割符 " "
* NF 当前记录的字段个数 -
* NR 到目前为止读的记录数量 -
* OFMT 数值的输出格式 "%.6g"
* OFS 输出字段分割符 " "
* ORS 输出的记录的分割符 "\n"
* RLENGTH 被函数 match 匹配的字符串的长度 -
* RS 控制着输入行的记录分割符 "\n"
* RSTART 被函数 match 匹配的字符串的开始
* SUBSEP 下标分割符 "\034"
## 内建算术函数
函数 返回值
* atan2(y,x) y/x 的反正切值, 定义域在 −π 到 π 之间
* cos(x) x 的余弦值, x 以弧度为单位
* exp(x) x 的指数函数, e^x
* int(x) x 的整数部分; 当 x 大于 0 时, 向 0 取整
* log(x) x 的自然对数 (以 e 为底)
* rand() 返回一个随机数 r, 0 ≤ r < 1
* sin(x) x 的正弦值, x 以弧度为单位.
* sqrt(x) x 的方根
* srand(x) x 是 rand() 的新的随机数种子
## 内建字符串函数
函数 描述
* gsub(r,s) 将 $0 中所有出现的 r 替换为 s, 返回替换发生的次数.
* gsub(r,s,t ) 将字符串 t 中所有出现的 r 替换为 s, 返回替换发生的次数
* index(s,t) 返回字符串 t 在 s 中第一次出现的位置, 如果 t 没有出现的话, 返回 0.
* length(s) 返回 s 包含的字符个数
* match(s,r) 测试 s 是否包含能被 r 匹配的子串, 返回子串的起始位置或 0; 设置 RSTART 与 RLENGTH
* split(s,a) 用 FS 将 s 分割到数组 a 中, 返回字段的个数
* split(s,a,fs) 用 fs 分割 s 到数组 a 中, 返回字段的个数
* sprintf(fmt,expr-list) 根据格式字符串 fmt 返回格式化后的 expr-list
* sub(r,s) 将 $0 的最左最长的, 能被 r 匹配的子字符串替换为 s, 返回替换发生的次数.
* sub(r,s,t) 把 t 的最左最长的, 能被 r 匹配的子字符串替换为 s, 返回替换发生的次数.
* substr(s,p) 返回 s 中从位置 p 开始的后缀.
* substr(s,p,n) 返回 s 中从位置 p 开始的, 长度为 n 的子字符串.

## 打印所有内容 $0
$0代表整行
```
[huawei@n148 playground]$ cat emp.data
Beth 4.00 0
Dan 3.75 0
Kathy 4.00 10
Mark 5.00 20
Mary 5.50 22
Susie 4.25 18

[huawei@n148 playground]$ awk '{print}' emp.data
[huawei@n148 playground]$ awk '{print $0}' emp.data
```
## 打印指定列 $1
$1~$n代表列，默认输出是空格间隔，可以单独去设置
```
[huawei@n148 playground]$ awk '{print $2,$1}' emp.data
4.00 Beth
3.75 Dan
4.00 Kathy
```
## 当前行的列数 NF
下面的$NF就是$3，因为只有3列
```
[huawei@n148 playground]$ awk '{print NF, $1, $NF}' emp.data
3 Beth 0
3 Dan 0
```
## 打印计算结果
```
[huawei@n148 playground]$ awk '{print $1, $2*$3}' emp.data
```
## 打印行号 NR
```
[huawei@n148 playground]$ awk '{print NR,$0}' emp.data
1 Beth 4.00 0
2 Dan 3.75 0
```
## 将文本放入输出中
```
[huawei@n148 playground]$ awk '{ print "total pay for", $1, "is", $2 * $3 }' emp.data
total pay for Mark is 100
total pay for Mary is 121
total pay for Susie is 76.5
```
## 使用 printf
printf需要知道值的具体类型才行，print则更像是泛型
```
[huawei@n148 playground]$ awk '{ printf("total pay for %s is $%.2f\n", $1, $2 * $3) }' emp.data
total pay for Mark is $100.00
total pay for Mary is $121.00
total pay for Susie is $76.50

[huawei@n148 playground]$ awk '{ printf("%-8s $%6.2f\n", $1, $2 * $3) }' emp.data
Mark     $100.00
Mary     $121.00
Susie    $ 76.50
```
## 排序
下面的排序局限在只能将key放到第一列
```
[huawei@n148 playground]$ awk '{ printf("%6.2f %s\n", $2 * $3, $0) }' emp.data | sort -n
  0.00 Beth 4.00 0
  0.00 Dan 3.75 0
 40.00 Kathy 4.00 10
 76.50 Susie 4.25 18
100.00 Mark 5.00 20
121.00 Mary 5.50 22
```
## 模式（条件判断）

```
[huawei@n148 playground]$ awk '$2>=5 {print}' emp.data
Mark 5.00 20
Mary 5.50 22
[huawei@n148 playground]$ awk '$2*$3>=50 {print}' emp.data
Mark 5.00 20
Mary 5.50 22
Susie 4.25 18
[huawei@n148 playground]$ awk '$1=="Susie" {print}' emp.data
Susie 4.25 18
```
## 页眉与页脚 BEGIN & END
注意 print "" 打印一个空行, 它与一个单独的 print 并不相同, 后者打印当前行.
```
[#!/bin/bash 
BEGIN {
  printf("%10s %6s %5s %s", "COUNTRY", "AREA", "POP", "CONTINENT")
  printf("\n\n")
}
{ 
  printf("%10s %6d %5d %s\n", $1, $2, $3, $4)
  area = area + $2
  pop = pop + $3
}
END { printf("\n%10s %6d %5d\n", "TOTAL", area, pop) }

[huawei@n148 playground]$ awk -f t.sh countries
   COUNTRY   AREA   POP CONTINENT

      USSR   8649   275 Asia
    Canada   3852    25 North
     China   3705  1032 Asia
       USA   3615   237 North
    Brazil   3286   134 South
     India   1267   746 Asia
    Mexico    762    78 North
    France    211    55 Europe
     Japan    144   120 Asia
   Germany     96    61 Europe
   England     94    56 Europe

     TOTAL  25681  2819
```

## 计数
if $3>15 则emp+1
```
[huawei@n148 playground]$ awk '$3>15 {emp=emp+1} END{print emp}' emp.data                                              3
```

## 求和与平均值
前提是总数不为0
```
[huawei@n148 playground]$ awk '{pay=pay+$2*$3} END{printf("total:%.3f\naver:%.3f\n",pay,pay/NR)}' emp.data
total:337.500
aver:56.250
```
## 找最大
max效果，只有当至少有一行的$2是正数时, 程序才是正确的
```
[huawei@n148 playground]$ awk '$2>maxrate{maxrate=$2;name=$1} END{printf("highest rate:%.3f\nname:%s\n",maxrate,name)}' emp.data
highest rate:5.500
name:Mary
```
## 连接字符串
join效果
```
[huawei@n148 playground]$ awk '{names=names $1 " "} END{print names}' emp.data
Beth Dan Kathy Mark Mary Susie
```
## 字符串length
```
[huawei@n148 playground]$ awk '{print $1, length($1)}' emp.data
Beth 4
Dan 3
Kathy 5
Mark 4
```
## 统计行数、词数、字符数
与wc效果相同
```
[huawei@n148 playground]$ awk '{nc=nc+length($0)+1;nw=nw+NF} END{printf("%d lines,%d words,%d chars\n",NR,nw,nc)}' emp.data
6 lines,18 words,77 chars
```
## if else
```
#!/bin/bash 
$2>4 {n=n+1;pay=pay+$2*$3}
END {
  if (n>0)
    print n, "emps, total:", pay, "ave:" pay/n
  else
    print "no emps than $6/hour"
}


[huawei@n148 playground]$ awk -f t.sh emp.data
3 emps, total: 297.5 ave:99.1667
```
## while & for
```
#!/bin/bash 
BEGIN{
  i=1
  while(i<=5){
    printf("%d\n",i)
    i=i+1
  }
}
{
  print
}
END{
  for(a=1;a<5;a=a+1){
    printf("%d\n",a)
  }
}

[huawei@n148 playground]$ awk -f t.sh emp.data
1
2
3
4
5
Beth 4.00 0
Dan 3.75 0
Kathy 4.00 10
Mark 5.00 20
Mary 5.50 22
Susie 4.25 18
1
2
3
4
```
## 一维数组
向一个NR个元素的array依次赋值，然后循环输出，注意这里没使用[0]，是从1开始使用的
```
#!/bin/bash 
{
  line[NR]=$0
}
END{
  for(a=NR;a>0;a=a-1){
    print line[a]
  }
}

[huawei@n148 playground]$ awk -f t.sh emp.data
Susie 4.25 18
Mary 5.50 22
Mark 5.00 20
Kathy 4.00 10
Dan 3.75 0
Beth 4.00 0
```
## 文件名常量 FILENAME、前N行 FNR
打印前5行，且加上文件名
```
[huawei@n148 playground]$ awk 'FNR <= 5 { print FILENAME ": " $0 }' countries
countries: USSR 8649 275 Asia
countries: Canada 3852 25 North America
countries: China 3705 1032 Asia
countries: USA 3615 237 North America
countries: Brazil 3286 134 South America
```
## 简单正则样式
```
[huawei@n148 playground]$ awk '/Asia/ { print }' countries
USSR 8649 275 Asia
China 3705 1032 Asia
India 1267 746 Asia
Japan 144 120 Asia
[huawei@n148 playground]$
```
## 改变值
使用逻辑比较，正则加gsub替换的两种方式
```
[huawei@n148 playground]$ awk  '$4 == "Asia" { $4 = "yazhou" } { print }' countries
USSR 8649 275 yazhou
Canada 3852 25 North America
China 3705 1032 yazhou
USA 3615 237 North America
Brazil 3286 134 South America
India 1267 746 yazhou
Mexico 762 78 North America
France 211 55 Europe
Japan 144 120 yazhou
Germany 96 61 Europe
England 94 56 Europe

[huawei@n148 playground]$ awk '{ gsub(/Asia/, "xxx"); print }' countries
USSR 8649 275 xxx
Canada 3852 25 North America
China 3705 1032 xxx
USA 3615 237 North America
Brazil 3286 134 South America
India 1267 746 xxx
Mexico 762 78 North America
France 211 55 Europe
Japan 144 120 xxx
Germany 96 61 Europe
England 94 56 Europe
```

# cat，more，less，tail，head 查看文件内容
# wc 统计行数-l、字数-w、字节数-c
```
[huawei@10 bin]$ cat data |wc
      6      18      61
[huawei@n148 playground]$ who | wc -l
```
# tr 转换大小写、删除指定字符、去重等等

# sort 排序
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
# uniq 去重
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
# cut 按列裁剪内容 
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
# fmt 排版
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

# seq 生成序列、join效果
```
[huawei@n148 playground]$ seq 5
1
2
3
4
5
[huawei@n148 playground]$ seq 3 5
3
4
5
[huawei@n148 playground]$ seq 1 2 5
1
3
5
[huawei@n148 playground]$ seq -s::: 1 2 5
1:::3:::5
[huawei@n148 playground]$ seq -s::: -w 1 2 111   w的作用是以最大值位数为size对其它小的数字前补0
001:::003:::005:::007:::009:::011:::013:::015:::017:::019:::021:::023:::025:::027:::029:::031:::033:::035:::037:::039:::041:::043:::045:::047:::049:::051:::053:::055:::057:::059:::061:::063:::065:::067:::069:::071:::073:::075:::077:::079:::081:::083:::085:::087:::089:::091:::093:::095:::097:::099:::101:::103:::105:::107:::109:::111
[huawei@n148 playground]$ seq -f "---%08g---" 1 18
---00000001---
---00000002---
---00000003---
---00000004---
---00000005---
---00000006---
---00000007---
---00000008---
---00000009---
---00000010---
---00000011---
---00000012---
---00000013---
---00000014---
---00000015---
---00000016---
---00000017---
---00000018---
```
# find 文件查找
https://www.cnblogs.com/derek1184405959/p/11100469.html
# tar 压缩与归档
下面案例演示按日期创建子目录，然后读取配置并将其内所有文件归档
```

[huawei@n148 playground]$ cat Files_To_Backup
/home/huawei/playground/src
/home/huawei/playground/shtool-2.0.8
/home/huawei/playground/member.csv

#!/bin/bash 
# set -x
# 创建目录
BASEDEST=/home/huawei/playground
DAY=$(date +%d) 
MONTH=$(date +%m) 
TIME=$(date +%k%M) 
mkdir -p $BASEDEST/$MONTH/$DAY 

FILE=archive$TIME.tar.gz 
CONFIG_FILE=$BASEDEST/Files_To_Backup 
DESTINATION=$BASEDEST/$MONTH/$DAY/$FILE 
echo "dest file:$DESTINATION"

# 检查存在 
if [ -f $CONFIG_FILE ] 
then
 echo "find config file:$CONFIG_FILE"
else 
 echo "$CONFIG_FILE does not exist." 
 exit 
fi 

# 读取配置里的每个文件名
exec 0< $CONFIG_FILE
count=1 
while read line 
do
 echo "$count:\t$line"
 if [ -e $line ]
 then
  FILE_LIST="$FILE_LIST $line"
 else
  echo "$line, does not exist."
 fi
 count=$[ $count + 1 ]
done

# 打包
tar -czf $DESTINATION $FILE_LIST 2> /dev/null 
echo "Archive completed" 
echo "Resulting archive file is: $DESTINATION" 
exit 

```
# 数组变量

# 打印

## echo

## printf
## od 八进制打印每个字符
```
[huawei@10 bin]$ od -a -b nusers
0000000   #   !  sp   /   b   i   n   /   s   h  sp   -  nl   w   h   o
        043 041 040 057 142 151 156 057 163 150 040 055 012 167 150 157
0000020   |   w   c  sp   -   l  nl
        174 167 143 040 055 154 012
0000027

```
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

# stdio与重定向
* 文件描述符0 STDIN 标准输入
* 文件描述符1 STDOUT 标准输出
* 文件描述符2 STDERR 标准错误
## 标准输入STDIN
在使用输入重定向符号（<）时，Linux会用重定向指定的文件来替换标准输入文件描述符
## 文件作为STDIN "<"
将<后面的文件作为程序的输入源，如读取整个文件
```
[huawei@n148 playground]$ cat <t.sh 
```
读取文件每一行
```
#!/bin/bash 
# redirecting file input 
exec 0< users.csv
count=1 
while read line 
do 
 echo "Line #$count: $line" 
 count=$[ $count + 1 ] 
done
```
## 标准输出STDOUT
STDOUT文件描述符代表shell的标准输出
## STDOUT重定向到文件 ">" & ">>"
将>后面的文件作为程序的输出目标，">"会覆写文件，">>"是追加模式
```
$ ls -l > test2
$ ls -l >> test2
```
## 多操作结果一次重定向到STDOUT "{...} >"
猜测也可重定向stderr，为测试。。。
```
#!/bin/bash 
{
	cat testfile
	cat testerror
} > 1.txt
```
## 标准错误STDERR
默认情况下，STDERR文件描述符会和STDOUT文件描述符指向同样的地方（尽管分配给它们
的文件描述符值不同）。但STDERR并不会随着STDOUT的重定向而发生改变
## STDERR重定向到文件 "2>"
只重定向错误，此时错误输出到了指定文件里，stdout还是显示器
```
[huawei@n148 playground]$ ls -al test badtest test2 2> test5 
-rw-rw-r-- 1 huawei huawei 0 Sep 26 11:21 test
[huawei@n148 playground]$ less test5
ls: cannot access badtest: No such file or directory
ls: cannot access test2: No such file or directory
```
## 重定向脚本的STDERR "2>”
将脚本中所有输出到STDERR的内容输出到文件里
```
$ cat test8 
#!/bin/bash 
# testing STDERR messages 
echo "This is an error" >&2 
echo "This is normal output" 

$ ./test8 2> test9 
```
## STDOUT与STDERR各自重定向 "1>" & "2>"
可以用这种方法将脚本的正常输出和脚本生成的错误消息分离开来。这样就可以轻松地识别出错误信息，再不用在成千上万行正常输出数据中翻腾了
```
[huawei@n148 playground]$ ls -al test badtest test2 2> test5 1>test6
[huawei@n148 playground]$ less test6
-rw-rw-r-- 1 huawei huawei 0 Sep 26 11:21 test
```
## STDOUT与STDERR重定向到一起 "&>"
```
[huawei@n148 playground]$ ls -al test badtest test2 &> test8
[huawei@n148 playground]$ cat test8
ls: cannot access badtest: No such file or directory
ls: cannot access test2: No such file or directory
-rw-rw-r-- 1 huawei huawei 0 Sep 26 11:21 test
```
效果同上，上面的是简写方式
```
[huawei@n148 playground]$ ls -al test badtest test2 > file 2>&1
[huawei@n148 playground]$ cat file
ls: cannot access badtest: No such file or directory
ls: cannot access test2: No such file or directory
-rw-rw-r-- 1 huawei huawei 0 Sep 26 11:21 test
```

## 使用exec进行重定向
作用类似文件句柄，
Linux命令行与shell脚本编程大全.第3版 15.2.2
## 重定向done ">" & "|"
done输出到文件
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
重定向输出到命令
```
for state in "North Dakota" Connecticut Illinois Alabama Tennessee 
do 
 echo "$state is the next place to go" 
done | sort 
echo "This completes our travels" 
```
# 管道
## 无名管道
```
[huawei@n148 ~]$ ps -ef | grep postdb
huawei    27545  27462 99 11:05 pts/0    00:01:48 postdb -D /home/huawei/hwwork/postdb/src/test/regress/./tmp_check/data -F -c listen_addresses= -k /tmp/pg_regress-assAwh
huawei    28309 127651  0 11:06 pts/3    00:00:00 grep --color=auto postdb
huawei   130061      1 16 10:39 ?        00:04:27 /home/huawei/hwwork/postdb/dest/bin/postdb
```
## 命名管道
实际上是一个文件，会阻塞，a写进后如果b不读取出则a会阻塞，b读取但a没写入则b也会阻塞，见下例
```
$ mkfifo fifo_test    #通过mkfifo命令创建一个有名管道
$ echo "fewfefe" > fifo_test
#试图往fifo_test文件中写入内容，但是被阻塞，要另开一个终端继续下面的操作
$ cat fifo_test        #另开一个终端，记得，另开一个。试图读出fifo_test的内容
fewfefe
```
# 数学运算

## expr与shell的计算方式
expr命令可以实现数值运算、数值或字符串比较、字符串匹配、字符串提取、字符串长度计算等功能。它还具有几个特殊功能，判断变量或参数是否为整数、是否为空、是否为0等。
https://www.cnblogs.com/f-ck-need-u/p/7231832.html
```
#!/bin/bash 
a=3
b=4
echo "expr:"
echo `expr $a + $b`
echo `expr $a - $b`
echo `expr $a \* $b`
echo `expr $a / $b`
echo `expr $a % $b`
echo "shell:"
echo $(($a+$b))
echo $(($a-$b))
echo $(($a*$b))
echo $(($a/$b))
echo $(($a%$b))
c=$(($a**$b))
echo $((($c+$b)*$a))
```
## bc 浮点计算
https://www.cnblogs.com/f-ck-need-u/p/7231870.html  
scale是控制小数点位数
```
#!/bin/bash 
a=3.1
b=4.2
echo "bc"
echo "scale=5;$a+$b" |bc
echo "scale=5;$a-$b" |bc
echo "scale=5;$a*$b" |bc
echo "scale=5;$a/$b"|bc
echo "scale=5;$a%$b"|bc
```
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
# 字符串
## 长度
```
#!/bin/bash 
var="get the length of me"
echo ${var}     # 这里等同于$var
echo ${#var}
expr length "$var"
echo $var | awk '{printf("%d\n", length($0));}'
echo -n $var |  wc -c
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
## true与false
非逻辑值，不要认为是1和0，都是内置命令
```
$ if true;then echo "YES"; else echo "NO"; fi
YES

$ help true false
true: true
     Return a successful result.
false: false
     Return an unsuccessful result.
$ type true false
true is a shell builtin
false is a shell builtin
```
false返回的是1，true返回的是0，这就是main返回0作为成功的原因。。。
```
$ true
$ echo $?
0
$ false
$ echo $?
1
```
## 与&& 或|| 非!
```
$ if true && true;then echo "YES"; else echo "NO"; fi
YES
$ if true || true;then echo "YES"; else echo "NO"; fi
YES
$ if ! false;then echo "YES"; else echo "NO"; fi
YES

如果ping通www.lzu.edu.cn，那么打印连通信息
$ ping -c 1 www.lzu.edu.cn -W 1 && echo "=======connected======="
```
判断字符串是否为空
```
var3="  "
if [ ! $var3 ] 
then 
 echo "The string '$var3' is empty" 
else 
 echo "The string '$var3' is not empty" 
fi
```
判断变量是否定义来给默认值
```
[huawei@n148 playground]$ [ ! "$a1" ] && a1='aaa'
[huawei@n148 playground]$ echo $a1
aaa
[huawei@n148 playground]$ [ "$a2" ] || a2='bbb'
[huawei@n148 playground]$ echo $a2
bbb

```

## 整数比较 整数比较 整数比较
* n1 -eq n2 检查n1是否与n2相等
* n1 -ge n2 检查n1是否大于或等于n2
* n1 -gt n2 检查n1是否大于n2
* n1 -le n2 检查n1是否小于或等于n2
* n1 -lt n2 检查n1是否小于n2
* n1 -ne n2 检查n1是否不等于n2
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
字符串变量要使用引号"$var"，避免变量为空语法失败，另外【】的左右都要有空格。如
```
$ if [ "$str" = "test" ]; then echo "YES"; else echo "NO"; fi
NO
```
* str1 = str2 检查str1是否和str2相同
* str1 != str2 检查str1是否和str2不同
* str1 < str2 检查str1是否比str2小
* str1 > str2 检查str1是否比str2大
* -n str1 检查str1的长度是否非0 
* -z str1 检查str1的长度是否为0

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

## 数学赋值 ((   ))

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

## 字符串正则比较[[   ]]

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
# 特殊文件
## dev/null
重定向到dev/null的任何数据都会被丢掉，
不会显示。
```
[huawei@n148 playground]$ ls -al badfile test16 2> /dev/null 
```
也可以用于清空文件
```
[huawei@n148 playground]$ cat /dev/null > testfile 
[huawei@n148 playground]$ less testfile
```
## dev/tty
## dev/zero
# 临时文件/tmp
## mktemp
创建临时文件，-t则会创建在/tmp中，-d则是创建目录
```
[huawei@n148 playground]$ mktemp testing.XXXXXX
testing.yUp2GA
[huawei@n148 playground]$ mktemp testing.XXXXXX
testing.AoKmfm
[huawei@n148 playground]$ mktemp testing.XXXXXX
testing.CtYRDQ
[huawei@n148 playground]$ ls
addem                dirlist-usr-sbin.txt  s      test6      testfile        testing.yUp2GA
dirlist-bin.txt      emp.data              src    test7      testing.AoKmfm  testout
dirlist-sbin.txt     multem                test   test8      testing.CtYRDQ  t.sh
dirlist-usr-bin.txt  output.txt            test5  testerror  testing.gurWe9  users.csv

[huawei@n148 playground]$ mktemp -t test.XXXXXX 
/tmp/test.rUUqyP

[huawei@n148 playground]$ mktemp -t -d dir.XXXXXX
/tmp/dir.BK96BS
```
创建临时文件并将文件名赋给$tempfile变量。作为文件描述符3的输出重定向文件。在将临时文件名显示在STDOUT之后，向临时文件中写入了几行文本，然后关闭了文件描述符。最后，显示出临时文件的内容，并用rm命令将其删除。
```
tempfile=$(mktemp test19.XXXXXX) 
exec 3>$tempfile 
echo "This script writes to temp file $tempfile" 
echo "This is the first line" >&3 
echo "This is the second line." >&3 
echo "This is the last line." >&3 
exec 3>&- 
echo "Done creating temp file. The contents are:" 
cat $tempfile 
rm -f $tempfile 2> /dev/null 
```
## /tmp
# tee
将从STDIN过来的数据同时发往两处。一处是STDOUT，另一处是tee命令行所指定的文件名，默认会覆盖文件，追加则-a
```
[huawei@n148 playground]$ date | tee testfile 
Sun Sep 26 15:16:27 CST 2021
[huawei@n148 playground]$ cat testfile 
Sun Sep 26 15:16:27 CST 2021
[huawei@n148 playground]$ who | tee -a  testfile 
huawei   pts/2        2021-09-18 14:29 (10.100.100.53)
huawei   :0           2021-09-15 15:52 (:0)
huawei   pts/3        2021-09-15 15:53 (:0)
huawei   pts/1        2021-09-18 11:51 (10.100.100.53)
huawei   pts/7        2021-09-24 11:43 (10.100.100.56)
huawei   pts/10       2021-09-26 10:53 (10.100.100.54)
huawei   pts/8        2021-09-24 09:45 (10.100.100.56)
huawei   pts/11       2021-09-26 13:16 (10.100.100.54)
[huawei@n148 playground]$ cat testfile 
Sun Sep 26 15:16:27 CST 2021
huawei   pts/2        2021-09-18 14:29 (10.100.100.53)
huawei   :0           2021-09-15 15:52 (:0)
huawei   pts/3        2021-09-15 15:53 (:0)
huawei   pts/1        2021-09-18 11:51 (10.100.100.53)
huawei   pts/7        2021-09-24 11:43 (10.100.100.56)
huawei   pts/10       2021-09-26 10:53 (10.100.100.54)
huawei   pts/8        2021-09-24 09:45 (10.100.100.56)
huawei   pts/11       2021-09-26 13:16 (10.100.100.54)
```
# 进程
## 查看进程id
```
[huawei@n148 ~]$ pidof node
105163 86437 86315 86304 86235 86191
```
## 查看进程的内存映像
使用pidof获取进程号后可以如下（一切皆文件。。。）
```
[huawei@n148 130061]$ cat /proc/130061/maps
00400000-00e53000 r-xp 00000000 fd:02 169222059                          /home/huawei/hwwork/postdb/dest/bin/postdb
01052000-01053000 r--p 00a52000 fd:02 169222059                          /home/huawei/hwwork/postdb/dest/bin/postdb
01053000-01064000 rw-p 00a53000 fd:02 169222059                          /home/huawei/hwwork/postdb/dest/bin/postdb
01064000-01067000 rw-p 00000000 00:00 0
01e0b000-01ebb000 rw-p 00000000 00:00 0                                 
```
## 获取优先级，调整优先级renice
```
[huawei@n148 ~]$  ps -e -o "%p %c %n" | grep postdb
 13520 postdb            0
130061 postdb            0
[huawei@n148 ~]$ renice 1 -p 18052
18052 (process ID) old priority 0, new priority 1
[huawei@n148 ~]$  ps -e -o "%p %c %n" | grep postdb
 18052 postdb            1
130061 postdb            0
```
# 信号
## 捕获信号 trap
# 后台运行
## 终端断开就停止
在末尾加上&即可，每次启动新作业时，Linux系统都会为其分配一个新的作业号和PID。通过ps命令，可以看到所有脚本处于运行状态。

```
[huawei@n148 playground]$ sh t.sh &
```
最好是将后台运行的脚本的STDOUT和STDERR进行重定向，避免与前台的输出混在一起，并且此种方式在终端断开后进程也就都退出了
## 终端断开也运行 nohup
```
nohup sh shell.sh &
查看日志：tail -f nohup.out
```
其实还可以tmux
## coproc 协程
# 作业控制
## 查看作业 jobs
```
#!/bin/bash 
# Test job control 
echo "Script Process ID: $$" 
count=1 
while [ $count -le 10 ] 
do 
 echo "Loop #$count" 
 sleep 2 
 count=$[ $count + 1 ] 
done 
echo "End of script..." 

```
参数-l可以显示出进程号，+代表默认（当前），-代表下一个
```
[huawei@n148 playground]$ jobs -l
[2]  81375 Stopped                 sh t.sh
[3]- 83412 Stopped                 sh t.sh
[4]+ 85642 Stopped                 sh t.sh
```
## 重启作业到后台 bg
## 重启作业到前台 fg

## at、atq、atrm
# 函数
## 简单使用
```
function func1 { 
 echo "This is an example of a function" 
} 
count=1 
while [ $count -le 5 ] 
do 
 func1 
 count=$[ $count + 1 ] 
done 
echo "This is the end of the loop" 
func1 
echo "Now this is the end of the script" 
```
## 返回值 $?
可以使用默认返回值，但不是最好的方案
```
func1() { 
 echo "trying to display a non-existent file" 
 ls -l badfile 
} 
echo "testing the function: " 
func1 
echo "The exit status is: $?"

[huawei@n148 playground]$ t.sh 
testing the function: 
trying to display a non-existent file
ls: cannot access badfile: No such file or directory
The exit status is: 2
```
使用return返回计算结果，但限制得是0~255，这个也不好
```
function dbl { 
 read -p "Enter a value: " value 
 echo "doubling the value" 
 return $[ $value * 2 ] 
} 
dbl 
echo "The new value is $?" 
```
最佳方案，用echo返回给调用方，可以返回浮点值和字符串值
```
function dbl { 
 read -p "Enter a value: " value 
 echo $[ $value * 2 ] 
} 
result=$(dbl) 
echo "The new value is $result" 
```
## 传递参数
实参是代码中的案例
```
function addem { 
 if [ $# -eq 0 ] || [ $# -gt 2 ] 
 then 
 echo -1 
 elif [ $# -eq 1 ] 
 then 
 echo $[ $1 + $1 ] 
 else 
 echo $[ $1 + $2 ] 
 fi
} 
echo -n "Adding 10 and 15: " 
value=$(addem 10 15) 
echo $value 
echo -n "Let's try adding just one number: " 
value=$(addem 10) 
echo $value 
echo -n "Now trying adding no numbers: " 
value=$(addem) 
echo $value 
echo -n "Finally, try adding three numbers: " 
value=$(addem 10 15 20) 
echo $value 

[huawei@n148 playground]$ t.sh
Adding 10 and 15: 25
Let's try adding just one number: 20
Now trying adding no numbers: -1
Finally, try adding three numbers: -1
```
实参是外部敲进去的案例
```
function func7 { 
 echo $[ $1 * $2 ] 
} 
if [ $# -eq 2 ] 
then 
 value=$(func7 $1 $2)  # 注意此处要手动再次传递进去，否则出错
 echo "The result is $value" 
else 
 echo "Usage: badtest1 a b" 
fi 

[huawei@n148 playground]$ t.sh 10 1112
The result is 11120
```
## 函数内局部变量 local
```
function func1 { 
 local temp=$[ $value + 5 ] 
 result=$[ $temp * 2 ] 
} 
temp=4 
value=6 
func1 
echo "The result is $result" 
if [ $temp -gt $value ] 
then 
 echo "temp is larger" 
else 
 echo "temp is smaller" 
fi 

[huawei@n148 playground]$ t.sh
The result is 22
temp is smaller
```
## 向函数传数组参数
## 从函数返回数组
## 递归
```
function factorial { 
 if [ $1 -eq 1 ] 
 then 
 echo 1 
 else 
 local temp=$[ $1 - 1 ] 
 local result=$(factorial $temp) 
 echo $[ $result * $1 ] 
 fi 
} 
read -p "Enter value: " value 
result=$(factorial $value) 
echo "The factorial of $value is: $result" 

[huawei@n148 playground]$ t.sh
Enter value: 5
The factorial of 5 is: 120
```
## 库函数
类似include的效果，使用source进行导入
```
库文件
[huawei@n148 playground]$ cat func
function addem { 
 echo $[ $1 + $2 ] 
} 
function multem { 
 echo $[ $1 * $2 ] 
} 
function divem { 
 if [ $2 -ne 0 ] 
 then 
 echo $[ $1 / $2 ] 
 else 
 echo -1 
 fi 
}

脚本文件
[huawei@n148 playground]$ cat t.sh
. ./func	导入时候确保路径正确无误
value1=10 
value2=5 
result1=$(addem $value1 $value2) 
result2=$(multem $value1 $value2) 
result3=$(divem $value1 $value2) 
echo "The result of adding them is: $result1" 
echo "The result of multiplying them is: $result2" 
echo "The result of dividing them is: $result3" 

调用成功
[huawei@n148 playground]$ t.sh
The result of adding them is: 15
The result of multiplying them is: 50
The result of dividing them is: 2
```