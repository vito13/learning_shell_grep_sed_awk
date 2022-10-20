# 安装软件

## 查看centos版本

cat /etc/redhat-release

## 重装yum

https://www.joshua317.com/article/228

重装yum也会伴随着重装python2。。。所以需要先删除python

```
#强制清除已安装的程序及其关联
sudo rpm -qa|grep python|sudo xargs rpm -e --allmatches --nodeps

#删除所有残余文,xargs，允许你对输出执行其他某些命令
sudo whereis python |sudo xargs rm -frv


#验证删除，返回无结果说明清除干净
whereis python
```

删除现有的yum
```
#强制清除已安装的程序及其关联
sudo rpm -qa|grep yum|sudo xargs rpm -e --allmatches --nodeps

#删除所有残余文,xargs，允许你对输出执行其他某些命令
sudo whereis yum |sudo xargs rm -frv

#验证删除，返回无结果说明清除干净
whereis yum
```

安装yum

```
rpm -Uvh --replacepkgs python*.rpm
rpm -Uvh --replacepkgs rpm-python*.rpm yum*.rpm --nodeps --force
```

验证

```
# python
Python 2.7.5 (default, Aug  4 2017, 00:39:18) 
[GCC 4.8.5 20150623 (Red Hat 4.8.5-16)] on linux2
Type "help", "copyright", "credits" or "license" for more information.
>>> 


# yum --version
3.4.3
  Installed: rpm-4.11.3-48.el7_9.x86_64 at 2021-12-28 02:19
  Built    : CentOS BuildSystem <http://bugs.centos.org> at 2021-11-24 16:33
  Committed: Michal Domonkos <mdomonko@redhat.com> at 2021-11-01

  Installed: yum-3.4.3-154.el7.centos.noarch at 2022-01-10 10:42
  Built    : CentOS BuildSystem <http://bugs.centos.org> at 2017-08-05 19:13
  Committed: CentOS Sources <bugs@centos.org> at 2017-08-01

  Installed: yum-plugin-fastestmirror-1.1.31-42.el7.noarch at 2022-01-10 10:42
  Built    : CentOS BuildSystem <http://bugs.centos.org> at 2017-08-11 10:23
  Committed: Valentina Mukhamedzhanova <vmukhame@redhat.com> at 2017-03-21
```
## 换源

```
备份老文件
sudo mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup
下载新文件
wget -O /etc/yum.repos.d/CentOS-Base.repo https://mirrors.aliyun.com/repo/Centos-7.repo
生成缓存
yum makecache 
```

## 查看是否安装了某个库

[huawei@n148 tutorial]$ rpm -qa | grep boost

## 查看某个库安装位置

[root@linuxftp243 ~]# rpm -ql httpd-2.4.6-90.el7.centos.x86_64

# 安装工作环境

## 安装虚拟机
```
1 安装vbox，下载centos iso
2 vbox新建虚拟机。先改设置，4g内存，磁盘容量200g，添加第二块网卡hostonly（第一块默认nat）
3 安装centos系统，英文，别选最小，选开发，挑带有dev之类的checkbox，网络界面打开2个网卡的开关
4 建立一个用户，尽量别只用root
5 系统装完后常用的gcc之类都有，可以上网，
6 ifconfig查看enp0s8有没有ip，如没有则sudo dhclient enp0s8，再次ifconfig就能有ip
  在vbox主ui菜单“管理-》主机网络管理器“中会看到此ip在其对应范围内
7 moba建立连接，用上面的ip端口，用户名和pw即可
8 在虚拟机里使用git clone下载代码，可以使用http地址进行clone
9 vscode添加ssh连接(ssh config文件里不要忘记写port),此刻可以看到远程代码了，不用着急编译因为缺东西
10 下载cmake.sh并sh之进行解压
11 安装perl的tap包用于pg代码的build，yum install perl-IPC-Run-0.92-2.el7.noarch
12 编写env.sh如下：主要是为了加入cmake与pg的bin目录到path
sudo dhclient enp0s8
export PDPORT=65432
export PDDATA=/home/huawei/pgdata/new 
export PATH=/home/huawei/hwwork/postdb/dest/bin:/home/huawei/work/cmake-3.21.1-linux-x86_64/bin:$PATH
export LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/home/huawei/hwwork/postdb/dest/lib 
13 往.bashrc里添加一行：   . ~/.env.sh
14 重连远程，使环境变量生效
15 sh ./configure_debug.sh,   sh ./corebuild.sh,     make clean,  make -j8,   make install完毕

```
## 连接远程桌面
```
1 使用ssh链接上后
2 [gordon@h12 ~]$ vncserver -list 可以查看当前
3 [gordon@h12 ~]$ vncserver 可以启动一个vnc，下面的h12:9即为地址(vncserver -kill :4是关闭)
New 'h12:9 (gordon)' desktop is h12:9
Starting applications specified in /home/gordon/.vnc/xstartup
Log file is /home/gordon/.vnc/h12:9.log
4 建立vnc连接，使用上面生成的地址，172.16.122.12:9，端口5900连接即可开启远程物理机桌面
5 在菜单的system tool里启动远程物理机的vm，启动liu_vm_n8启动虚拟机，如果启动不了就是盘还没有挂上，还需挂盘
6 挂盘：sudo mount -t auto /dev/data/data1  /data    此句在cat /etc/fstab的最后一句
7 挂盘后直接启动虚拟机，
	进入/data/gordon/liu_vm_n8， 执行[gordon@h12 liu_vm_n8]$ vmrun  -T ws start liu_vm_n8.vmx nogui
	下面的操作不用执行了，有个脚本可以代替vnc，
	文件位置在/data/gordon/vm12.sh。
	内容是vmrun -T ws $1 /data/gordon/liu_vm_n8/liu_vm_n8.vmx nogui
	如果vnc里已经开启了vm的gui界面则上述的命令行会执行失败，那还是改用gui点启动才行
```
## pandoc、mdbook、latex
```
如果下面执行命令：yum install cargo.x86_64 如果提示No package cargo.x86_64 available.
可以执行下面命令： curl https://sh.rustup.rs -sSf | sh

安装步骤，建议以管理员登入进行操作
$ yum install -y ca-certificates
$ yum install cargo.x86_64
$ cargo install mdbook

如果mdbook由于rust版本过高而安装失败，需要重新安装rust
$ yum remove rust
$ curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

添加mdbook到PATH中
$ vi ~/.bash_profile
PATH=$PATH:$HOME/bin:$HOME/.cargo/bin
$. ~/.bash_profile



如果perl低于5.30需要更新，否则跳过此步
$ yum remove perl
$ wget https://www.cpan.org/src/5.0/perl-5.36.0.tar.gz
$ tar zxvf perl-5.36.0.tar.gz
$ cd perl-5.36.0
$ ./Configure -des -Dprefix=/usr/local/perl -Dusethreads -Uversiononly
$ make -j 3
$ make -j 3 install
设置环境参数
$ vi ~/.bashrc
加入如下内容
LOCAL_APP=$HOME/perl5/
# local perl edition
LOCAL_PERL_EDITION=$LOCAL_APP/perl
export PERL5LIB=$LOCAL_PERL_EDITION/lib
export PATH=$LOCAL_PERL_EDITION/bin:$PATH
# local perl lib
LOCAL_PERL_LIB=$LOCAL_APP/perl5
export PERL_LOCAL_LIB_ROOT="$PERL_LOCAL_LIB_ROOT:$LOCAL_PERL_LIB"
export PERL_MB_OPT="--install_base $LOCAL_PERL_LIB"
export PERL_MM_OPT="INSTALL_BASE=$LOCAL_PERL_LIB"
export PERL5LIB=$LOCAL_PERL_LIB/lib/perl5:.:$PERL5LIB
export PATH=$LOCAL_PERL_LIB/bin:$PATH
保存并刷新
$ . ~/.bashrc



下面如果安装perl必备中执行yum install perl-App-cpanminus.noarch导致perl回到旧版本,
请使用以下命令 sudo wget http://xrl.us/cpanm -O /usr/bin/cpanm; sudo chmod +x /usr/bin/cpanm代替上面的命令。


安装perl必备
$ wget https://cpan.metacpan.org/authors/id/A/AN/ANDK/CPAN-2.34.tar.gz
$ tar zxvf CPAN-2.34.tar.gz
$ cd CPAN-2.34
$ perl Makefile.PL
$ make && make test
$ make install
$ yum install perl-App-cpanminus.noarch
换国内源
$ perl -MCPAN -e shell
cpan[1]> o conf urllist push http://mirror.lzu.edu.cn/CPAN/
cpan[2]> o conf commit
cpan[3]> q
安装perl包
$ cpanm Modern::Perl 
$ cpanm File::Find::Rule
$ cpanm Path::Class
$ cpanm Mojo::File
$ cpanm File::Remove
$ cpanm Regexp::Common
$ cpanm Regexp::Common::Markdown
$ cpanm Lingua::Han::PinYin



安装python3
$ yum --exclude=kernel* update -y
$ yum groupinstall -y 'Development Tools'
$ yum install -y gcc openssl-devel bzip2-devel libffi-devel
$ wget https://www.python.org/ftp/python/3.6.8/Python-3.6.8.tgz
$ tar zxvf Python-3.6.8.tgz
$ cd Python-3.6.8
$ ./configure prefix=/usr/local/python3 --enable-optimizations
$ make -j 3
$ make install
添加到path中
$ vi ~/.bash_profile
PATH=$PATH:$HOME/bin:$HOME/.cargo/bin:/usr/local/python3/bin/
$. ~/.bash_profile


# 安装pandoc
$ wget https://github.com/jgm/pandoc/releases/download/2.10/pandoc-2.10-linux-amd64.tar.gz
$ tar zxvf pandoc-2.10-linux-amd64.tar.gz
添加到path中
$ vi ~/.bash_profile
PATH=$PATH:$HOME/bin:$HOME/.cargo/bin:/usr/local/python3/bin:/root/Downloads/pandoc-2.10/bin
$. ~/.bash_profile



安装latex
$ wget https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/Images/texlive2022.iso
$ locate texlive | xargs rm -rf
$ mount -o loop texlive2022.iso /mnt/
$ cd /mnt 
$ ./install-tl
按“I”进行安装，完毕后，可检查版本号： 
$ latex -version
$ cd ~
$ umount /mnt/
添加到path中
$ vi ~/.bashrc 
PATH=/usr/local/texlive/2022/bin/x86_64-linux:$PATH; export PATH
MANPATH=/usr/local/texlive/2022/texmf-dist/doc/man:$MANPATH; export MANPATH
INFOPATH=/usr/local/texlive/2022/texmf-dist/doc/info:$INFOPATH; export INFOPATH
$ . ~/.bashrc 
换国内源进行更新
$ tlmgr option repository https://mirrors.tuna.tsinghua.edu.cn/CTAN/systems/texlive/tlnet
$ tlmgr update --self --all --reinstall-forcibly-removed
```
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
## 踢掉昨天以前的多余用户
```
[huawei@n148 ~]$ cat ti.sh
#! /bin/sh
LD_IFS="$IFS"
IFS='
'
curdate=`date +%Y-%m-%d`
t1=`date -d "$curdate" +%s`

for user in $(who)
do
        IFS=' '
        arr=($user)
        t2=`date -d "${arr[2]}" +%s`
        if [ $t1 -gt  $t2 ]; then
                sudo pkill -kill -t ${arr[1]}
                echo "kill ${arr[1]}"
        fi
done
IFS="$OLD_IFS"
```
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

## 导出为环境变量 export 
导出变量到父shell
```
$ my_variable="I am Global now"
$ export my_variable
```
## 取消变量 unset
是让变量不存在了，而不是让其值为空
```
[huawei@10 postdb]$ my_variable="I am Global now"
[huawei@10 postdb]$ echo $my_variable
I am Global now
[huawei@10 postdb]$ unset my_variable
[huawei@10 postdb]$ echo $my_variable
[huawei@10 postdb]$

注意下面的区别。第2个是將變量 A 設定為"空值"(null  value)
[huawei@n148 ~]$ unset A
[huawei@n148 ~]$ echo $A

[huawei@n148 ~]$ A=
[huawei@n148 ~]$ echo $A

[huawei@n148 ~]$
```
# shell与sh文件
## 何为shell？
http://bbs.chinaunix.net/thread-218853-2-1.html

在Linux的预设系统中，通常可以找到好几种不同的shell。大部分的Linux操作系统的预设shell都是bash，其原因大致如下两种：
* 自由软件
* 功能强大 bash是gnu project最成功的产品之一，自推出以来深受广大Unix用户的喜爱， 且也逐渐成为不少组织的系统标准。
## 查看本机的shell种类


```
[huawei@n148 ~]$ cat /etc/shells
/bin/sh
/bin/bash
/usr/bin/sh
/usr/bin/bash
/bin/tcsh
/bin/csh
/usr/bin/tmux
```

## shell prompt(PS1) 与 Carriage Return(CR) 的关系
http://bbs.chinaunix.net/forum.php?mod=viewthread&tid=218853&page=2#pid1467910

提示符(prompt)的格式或因不同的版本而各有不同， 在Linux上，只需留意最接近游标的一个提示符号，通常是如下两者之一：
* $: 给一般用户账号使用
* #: 给root(管理员)账号使用

shell prompt的意思很简单： 告诉shell使用者，您现在可以输入命令行了。CR（Carriage Return, 由Enter键产生）的意思是使用者告诉shell：老兄，你可以执行的我命令行了。严格来说： 所谓的命令行， 就是在shell prompt与CR之间所输入的文字。而 cursor 是指示鍵盤在命令行所輸入的位置，使用者每輸入一個鍵，cursor 就往後移動一格，直到碰到命令行讀進 CR(Carriage Return，由 Enter 鍵產生)字符為止。

## 命令行的标准格式
分为下面3部分
```
command-name options argument  
```
<font color=#FF000 size=5>**shell会依据IFS(Internal Field Seperator) 将 command line 所输入的文字先拆解为字段(word). 然后再针对特殊的字符(meta)做处理，最后再重组整行command line。**  </font>

IFS是shell预设使用的字段位分隔符号，可以由一个及多个如下按键组成：
* 空白键(White Space)
* 表格键(Tab)
* 回车键(Enter)

命令的名称(command-name)可以从如下途径获得：
* 明確路逕所指定的外部命令
* 命令別名(alias)
* 自定功能(function)
* shell 內建命令(built-in)
* $PATH 之下的外部命令

每一個命令行均必需含用命令名稱，這是不能缺少的
## 命令行中meta字符
command line的每一个charactor, 分为如下两种：
* literal：也就是普通的纯文字，对shell来说没特殊功能；
* meta: 对shell来说，具有特定功能的特殊保留元字符。

除了常用的IFS与CR, 常用的meta还有：
```
meta   作用
=      设定变量
$      作变量或运算替换(请不要与shell prompt混淆)
>      输出重定向(重定向stdout)
<      输入重定向(重定向stdin)
|      命令管道
&      重定向file descriptor或将命令至于后台(bg)运行
()     将其内部的命令置于nested subshell执行，或用于运算或变量替换
{}     将期内的命令置于non-named function中执行，或用在变量替换的界定范围
;      在前一个命令执行结束时，而忽略其返回值，继续执行下一个命令
&&     在前一个命令执行结束时，若返回值为true，继续执行下一个命令
||     在前一个命令执行结束时，若返回值为false，继续执行下一个命令
!      执行histroy列表中的命令
...
```
假如我们需要在command line中将这些保留元字符的功能关闭的话， 就需要quoting处理了。常用的quoting有以下三种方法：
* hard quote：''(单引号)，凡在hard quote中的所有meta均被关闭；
* soft quote：""(双引号)，凡在soft quote中大部分meta都会被关闭，但某些会保留(如$);
* escape: \ (反斜杠)，只有在紧接在escape(跳脱字符)之后的单一meta才被关闭；

## “ ” 与 ‘ ’ 和 \ 的差异
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
# 打印

## echo
echo将argument送出到标准输出(stdout),通常是在监视器(monitor)上输出。
```
“[huawei@n148 ~]$ echo”输出只有一个空白行，然后又回到了shell prompt上了。 这是因为echo在预设上，在显示完argument之后，还会送出以一个换行符号 (new-line charactor). 但是上面的command echo并没有任何argument，那结果就只剩一个换行符号。 若你要取消这个换行符号， 可以利用echo的-n 选项

[huawei@n148 ~]$ echo

[huawei@n148 ~]$ echo -n
[huawei@n148 ~]$

[huawei@n148 ~]$ echo -e "a\tb\tc\nd\te\tf"
a       b       c
d       e       f

[huawei@n148 ~]$ echo -e "\x61\x09\x62\x09\x63\x0a\x64\x09\x65\x09\x66"
a       b       c
d       e       f

[huawei@n148 ~]$ echo -ne "a\tb\tc\nd\te\bf\a"
a       b       c
d       f[huawei@n148 ~]$

```

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
# 分解路径文件名
```
[huawei@n148 ~]$ p=/home/huawei/hwwork/postdb/src/test/ATL3/main.sh
[huawei@n148 ~]$ echo ${p##*/}
main.sh
[huawei@n148 ~]$ echo ${p##*.}
sh
[huawei@n148 ~]$ echo ${p%/*}
/home/huawei/hwwork/postdb/src/test/ATL3

```
# 权限

umask、chmod、chown、chgrp

Linux命令行与shell脚本编程大全.第3版 7.2  

改变文件夹的所有者
```
[huawei@10 postdb]$ sudo chown -R huawei:huawei /home/huawei/work/postdb/
```
# 文件操作 

## 文件查找 find、locate
https://www.cnblogs.com/derek1184405959/p/11100469.html
```
[huawei@10 postdb]$ locate pg_config_ext.h
/home/huawei/work/postdb/dest/include/pg_config_ext.h
/home/huawei/work/postdb/dest/include/postgresql/server/pg_config_ext.h

[huawei@10 postdb]$ find -name  pg_config_ext.h
./dest/include/postgresql/server/pg_config_ext.h
./dest/include/pg_config_ext.h

```
## 复制 cp
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
## 正则匹配行首、行尾、指定行内容、空行
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
# 正则 Regular Expression
正则表达式，又称 规则表达式。正则表达式的英语原文为：Regular Expression，常简写为regex、regexp或RE，正则表达式是计算机科学的一个概念。正则表达式通常被用来检索、替换那些符合某个模式(规则)的文本。  
```
标注正则表达式（非扩展）
#################常用符号#################
.   表示任意单个字符。
*  表示前面的字符连续出现任意次，包括0次。
.* 表示任意长度的任意字符，与通配符中的*的意思相同。
\  表示转义符，当与正则表达式中的符号结合时表示符号本身。
[  ]表示匹配指定范围内的任意单个字符。
[^  ]表示匹配指定范围外的任意单个字符。
 
#################单个字符匹配相关#################
[[:alpha:]]  表示任意大小写字母。
[[:lower:]]  表示任意小写字母。
[[:upper:]]  表示任意大写字母。
[[:digit:]]  表示0到9之间的任意单个数字（包括0和9）。
[[:alnum:]]  表示任意数字或字母。
[[:space:]]  表示任意空白字符，包括"空格"、"tab键"等。
[[:punct:]]  表示任意标点符号。
[^[:alpha:]]  表示单个非字母字符。
[^[:lower:]]  表示单个非小写字母字符。
[^[:upper:]]  表示单个非大写字母字符。
[^[:digit:]]  表示单个非数字字符。
[^[:alnum:]]  表示单个非数字非字母字符。
[^[:space:]]  表示单个非空白字符。
[^[:punct:]]  表示单个非标点符号字符。
[0-9]与[[:digit:]]等效。
[a-z]与[[:lower:]]等效。
[A-Z]与[[:upper:]]等效。
[a-zA-Z]与[[:alpha:]]等效。
[a-zA-Z0-9]与[[:alnum:]]等效。
[^0-9]与[^[:digit:]]等效。
[^a-z]与[^[:lower:]]等效。
[^A-Z]与[^[:upper:]]等效
[^a-zA-Z]与[^[:alpha:]]等效
[^a-zA-Z0-9]与[^[:alnum:]]等效
#简短格式并非所有正则表达式解析器都可以识别。
\d 表示任意单个0到9的数字。
\D 表示任意单个非数字字符。
\t 表示匹配单个横向制表符（相当于一个tab键）。
\s表示匹配单个空白字符，包括"空格"，"tab制表符"等。
\S表示匹配单个非空白字符。
 
#################次数匹配相关#################
\?  表示匹配其前面的字符0或1次
\+  表示匹配其前面的字符至少1次，或者连续多次，连续次数上不封顶。
\{n\} 表示前面的字符连续出现n次，将会被匹配到。
\{x,y\} 表示之前的字符至少连续出现x次，最多连续出现y次，都能被匹配到，换句话说，只要之前的字符连续出现的次数在x与y之间，即可被匹配到。
\{,n\} 表示之前的字符连续出现至多n次，最少0次，都会陪匹配到。
\{n,\}表示之前的字符连续出现至少n次，才会被匹配到。
 
#################位置边界匹配相关#################
^：表示锚定行首，此字符后面的任意内容必须出现在行首，才能匹配。
$：表示锚定行尾，此字符前面的任意内容必须出现在行尾，才能匹配。
^$：表示匹配空行，这里所描述的空行表示"回车"，而"空格"或"tab"等都不能算作此处所描述的空行。
^abc$：表示abc独占一行时，会被匹配到。
\<或者\b ：匹配单词边界，表示锚定词首，其后面的字符必须作为单词首部出现。
\>或者\b ：匹配单词边界，表示锚定词尾，其前面的字符必须作为单词尾部出现。
\B：匹配非单词边界，与\b正好相反。
 
#################分组与后向引用#################
\( \) 表示分组，我们可以将其中的内容当做一个整体，分组可以嵌套。
\(ab\) 表示将ab当做一个整体去处理。
\1 表示引用整个表达式中第1个分组中的正则匹配到的结果。
\2 表示引用整个表达式中第2个分组中的正则匹配到的结果。
```


## 行首^	行尾$
^ 匹配每一行的开头。只有^出现在正则表达式开头时，它才匹配行的开头  
$ 匹配行的结尾。

```
[huawei@n148 sed]$ sed -n '/^103/ p' employee.txt	匹配103开头的
103,Raj Reddy,Sysadmin
[huawei@n148 sed]$ sed -n '/r$/ p' employee.txt		匹配r结尾的
102,Jason Smith,IT Manager
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


在行首与行尾追加内容
[huawei@n148 sed]$ sed -n 's/^/@@@/ p' employee.txt
@@@101,John Doe,CEO
@@@102,Jason Smith,IT Manager
@@@103,Raj Reddy,Sysadmin
@@@104,Anand Ram,Developer
@@@105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n 's/$/@@@/ p' employee.txt
101,John Doe,CEO@@@
102,Jason Smith,IT Manager@@@
103,Raj Reddy,Sysadmin@@@
104,Anand Ram,Developer@@@
105,Jane Miller,Sales Manager@@@

```
## 任意单个 .  任意n个 *

```
. 匹配除换行符之外的任意单个字符
* 匹配 0 个或多个其前面的字符。如：1* 匹配 0 个或多个 1
使用 .* 表示任意长度的任意字符，与通配符中的*所表达的意思一样


匹配ee开头后跟一个任意字符
[huawei@n148 reg]$ grep -n "ee." regex.txt 
9:ef eef eeef

匹配ee开头后跟儿个任意字符
[huawei@n148 reg]$ grep -n "ee.." regex.txt 
9:ef eef eeef

把 employee.txt 中每行最后两个字符替换为”,Not Defined”:
[huawei@n148 sed]$ sed -n 's/..$/,Not Defined/ p' employee.txt
101,John Doe,C,Not Defined
102,Jason Smith,IT Manag,Not Defined
103,Raj Reddy,Sysadm,Not Defined
104,Anand Ram,Develop,Not Defined
105,Jane Miller,Sales Manag,Not Defined


”J… “同时匹配 employee.txt 文件中的”John “和”Jane “,替换结果如下
[huawei@n148 sed]$ sed -n 's/J... /Jason /p' employee.txt
101,Jason Doe,CEO
105,Jason Miller,Sales Manager

匹配n次e后接f的
[huawei@n148 reg]$ grep -n "e*f" regex.txt
9:ef eef eeef

匹配n次d的，包含0次所以匹配了所有行
[huawei@n148 reg]$ grep -n "d*" regex.txt
1:a a
2:aa
3:a aa
4:bb
5:bbb
6:c cc ccc
7:dddd d dd ddd
8:ab abc abcc
9:ef eef eeef

匹配a开头的行
[huawei@n148 reg]$ grep -n "a.*" regex.txt
1:a a
2:aa
3:a aa
8:ab abc abcc


显示包含 log:并且 log 后面有信息的行，log 和信息之间可能有空格。正则里*后面的点.是必需的，如果没有，sed 只会打印所有包含 log 的行
[huawei@n148 sed]$ cat log.txt
log: input.txt
log:
log: testing resumed
log:
log:output created
[huawei@n148 sed]$ sed -n '/log: *./p' log.txt
log: input.txt
log: testing resumed
log:output created
```
## 匹配0次或1次 ?   匹配1次或多次 +
```
\+ 匹配一次或多次它前面的字符
\? 匹配 0 次或一次它前面的字符

[huawei@n148 sed]$ cat log.txt
log: input.txt
log:
log: testing resumed
log:
log:output created

显示包含 log:并且 log:后面有一个或多个空格的所有行。本例既没有匹配只包含 log:的行，也没有匹配 log:output craeted 这一行，因为 log:后面没有空格。
[huawei@n148 sed]$ sed -n '/log: \+/ p' log.txt
log: input.txt
log: testing resumed


显示包含 log:并且 log:后面有0个或1个空格的所有行：
[huawei@n148 sed]$ sed -n '/log: \?/ p' log.txt
log: input.txt
log:
log: testing resumed
log:
log:output created

```
## 转义符号 \
如果要在正则表达式中搜寻特殊字符(如:*,.)，必需使用\来转义它们。
```
转义这几个:  \.  \*  \?   \+  \\
转义\就要用到单引号；
转义其他符号就要用到双引号；

[huawei@n148 sed]$ sed -n '/127\.0\.0\.1/ p' /etc/hosts
127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4

[huawei@n148 reg]$ cat reg11
bae
a1#
ddd
a-!
ccc
a..
aaaa
a*
ab
a
ab?
ab+
a\
abc
a\\
a\a

[huawei@n148 reg]$ grep "a\.\." reg11	仅匹配a..
a..
[huawei@n148 reg]$ grep "a\*" reg11		仅匹配a*
a*
[huawei@n148 reg]$ grep "ab?" reg11		仅匹配ab?
ab?
[huawei@n148 reg]$ grep "ab+" reg11		仅匹配ab+
ab+
[huawei@n148 reg]$ grep 'a\\' reg11		仅匹配a\
a\
a\\
a\a
[huawei@n148 reg]$ grep 'a\\\\' reg11	仅匹配a\\
a\\
```
## 匹配字符集 [  ]
```
[ ] 表示匹配指定范围内的任意单个字符，就是字符与方括号[ ]内的任意一个字符相同，就可以被匹配到。
[^ ]表示匹配指定范围外的任意单个字符，注意，它与[ ]的含义正好相反。
[^0-9]与[^[:digit:]]等效
[^a-z]与[^[:lower:]]等效
[^A-Z]与[^[:upper:]]等效
[^a-zA-Z]与[^[:alpha:]]等效
[^a-zA-Z0-9]与[^[:alnum:]]等效


匹配包含 2、3 或者 4 的行。可以使用连接符-指定一个字符范围。如[0123456789]可以用[0-9]表示，字母可以用[a-z],[A-Z]表示
[huawei@n148 sed]$ sed -n '/[234]/ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
(另一种方式):
[huawei@n148 sed]$ sed -n '/[2-4]/ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer


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

## 或操作符 |
管道符号|用来匹配两边任意一个子表达式。子表达式 1|子表达式 2 匹配子表达式 1 或者子表达式 2
```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

打印包含 101 或者包含 102 的行。需要注意，| 需要用\转义。
[huawei@n148 sed]$ sed -n '/101\|102/ p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager

打印包含数字 2~3 或者包含 105 的行：
[huawei@n148 sed]$ sed -n '/[2-3]\|105/ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager


[huawei@n148 reg]$ cat mail
zsything@zsything.net
1@2
zhuangshuangyin@szything.net
aaa.org
tetregex@163.com
tetregex@163zsy.net
tetregex@zsything.org
tetregex@zsything.edu
tetregex@zsything.ttt
a@1.com
tetregex@163.cccom

匹配com或net结尾的
[huawei@n148 reg]$ grep -E "(com|net)$" mail
zsything@zsything.net
zhuangshuangyin@szything.net
tetregex@163.com
tetregex@163zsy.net
a@1.com
tetregex@163.cccom
```
## 范围量词 精准匹配m次 {m}
```
正则表达式后面跟上{m}标明精确匹配该正则 m 次。

[huawei@n148 sed]$ cat numbers.txt
1
12
123
1234
12345
123456

打印包含任意数字的行(这个命令将打印所有行)
[huawei@n148 sed]$ sed -n '/[0-9]/ p' numbers.txt
1
12
123
1234
12345
123456

打印包含 5 个数字的行,注意这里一定要有开头和结尾符号，即^和$,并且{和}都要用\转义
[huawei@n148 sed]$ sed -n '/^[0-9]\{5\}$/ p' numbers.txt
12345


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
```
## 范围量词 匹配m至n次 {m,n}
正则表达式后面跟上{m,n}表明精确匹配该正则至少 m，最多 n 次。m 和 n 不能是负数，并且要小于 255. 正则表达式后面跟上{m,}表明精确匹配该正则至少 m，最多不限。(同样，如果是{,n}表明最多匹配 n 次，最少一次)。

```
[huawei@n148 sed]$ cat numbers.txt
1
12
123
1234
12345
123456
打印由 3 至 5 个数字组成的行：
[huawei@n148 sed]$ sed -n '/^[0-9]\{3,5\}$/ p' numbers.txt
123
1234
12345

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
[huawei@n148 reg]$ grep -n "d\{2,4\}" regex.txt 匹配连续2~4次连续d
7:dddd d dd ddd
[huawei@n148 reg]$ grep -n "d\{2,\}" regex.txt 匹配连续2~无限次连续d
7:dddd d dd ddd
[huawei@n148 reg]$ grep -n "abc\{,2\}" regex.txt 匹配连续0~2次c
8:ab abc abcc
```

## 词首词尾\b   非词首词尾\B
\b 用来匹配单词开头(\bxx)或结尾(xx\b)的任意字符，因此\bthe\b 将匹配 the,但不匹配 they。\bthe 将匹配 the 或 they。  
非词首非词尾即一个字符串里包含了要找的子串
```

[huawei@n148 sed]$ cat words.txt
word matching using: the
word matching using: thethe
word matching using: they

匹配包含 the 作为整个单词的行。注意：如果没有后面那个\b,将匹配所有行。
[huawei@n148 sed]$ sed -n '/\bthe\b/ p' words.txt
word matching using: the

匹配所有以 the 开头的单词:
[huawei@n148 sed]$ sed -n '/\bthe/ p' words.txt
word matching using: the
word matching using: thethe
word matching using: they


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

## 分组、后向引用（回溯引用）\n
可以给正则表达式分组，以便在后面引用它们
```
\( \) 表示分组，我们可以将其中的内容当做一个整体，分组可以嵌套。
\(ab\) 表示将ab当做一个整体去处理。
\1 表示引用整个表达式中第1个分组中的正则匹配到的结果。
\2 表示引用整个表达式中第2个分组中的正则匹配到的结果。

[huawei@n148 sed]$ cat words.txt
word matching using: the
word matching using: thethe
word matching using: they
只匹配重复 the 两次的行。同理，”\([0-9]\)\1” 匹配连续两个相同的数字，如 11,22,33 …..
[huawei@n148 sed]$ sed -n '/\(the\)\1/ p' words.txt
word matching using: thethe


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


## 手机号、ip地址
```
一个以136开头的11位数字，并且这11个数字作为一个单独的单词存在
\b136[[:digit:]]\{8\}\b	

ip地址则分为3部分解析，显示1~3个数字后接. ->   再是3次重复  ->  再是1~3个数字
[huawei@n148 reg]$ ifconfig |grep "\([0-9]\{1,3\}\.\)\{3\}\([0-9]\{1,3\}\)"
        inet 192.168.88.148  netmask 255.255.255.0  broadcast 192.168.88.255
        inet 127.0.0.1  netmask 255.0.0.0
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255

```
![ip](https://www.zsythink.net/wp-content/uploads/2017/06/060317_0309_4.png)
## 扩展正则 -E
```
扩展正则（跟上面的非扩展有少许差异，去不了部分\更简洁了）
常用符号
.   表示任意单个字符。
*  表示前面的字符连续出现任意次，包括0次。
.* 表示任意长度的任意字符，与通配符中的*的意思相同。
\  表示转义符，当与正则表达式中的符号结合时表示符号本身。
| 表示"或者"之意
[  ]表示匹配指定范围内的任意单个字符。
[^  ]表示匹配指定范围外的任意单个字符。
 
单个字符匹配相关
[[:alpha:]]  表示任意大小写字母。
[[:lower:]]  表示任意小写字母。
[[:upper:]]  表示任意大写字母。
[[:digit:]]  表示0到9之间的任意单个数字（包括0和9）。
[[:alnum:]]  表示任意数字或字母。
[[:space:]]  表示任意空白字符，包括"空格"、"tab键"等。
[[:punct:]]  表示任意标点符号。
[^[:alpha:]]  表示单个非字母字符。
[^[:lower:]]  表示单个非小写字母字符。
[^[:upper:]]  表示单个非大写字母字符。
[^[:digit:]]  表示单个非数字字符。
[^[:alnum:]]  表示单个非数字非字母字符。
[^[:space:]]  表示单个非空白字符。
[^[:punct:]]  表示单个非标点符号字符。
[0-9]与[[:digit:]]等效。
[a-z]与[[:lower:]]等效。
[A-Z]与[[:upper:]]等效。
[a-zA-Z]与[[:alpha:]]等效。
[a-zA-Z0-9]与[[:alnum:]]等效。
[^0-9]与[^[:digit:]]等效。
[^a-z]与[^[:lower:]]等效。
[^A-Z]与[^[:upper:]]等效
[^a-zA-Z]与[^[:alpha:]]等效
[^a-zA-Z0-9]与[^[:alnum:]]等效
 
次数匹配相关
?  表示匹配其前面的字符0或1次
+  表示匹配其前面的字符至少1次，或者连续多次，连续次数上不封顶。
{n} 表示前面的字符连续出现n次，将会被匹配到。
{x,y} 表示之前的字符至少连续出现x次，最多连续出现y次，都能被匹配到，换句话说，只要之前的字符连续出现的次数在x与y之间，即可被匹配到。
{,n} 表示之前的字符连续出现至多n次，最少0次，都会陪匹配到。
{n,}表示之前的字符连续出现至少n次，才会被匹配到。
 
位置边界匹配相关
^：表示锚定行首，此字符后面的任意内容必须出现在行首，才能匹配。
$：表示锚定行尾，此字符前面的任意内容必须出现在行尾，才能匹配。
^$：表示匹配空行，这里所描述的空行表示"回车"，而"空格"或"tab"等都不能算作此处所描述的空行。
^abc$：表示abc独占一行时，会被匹配到。
\<或者\b ：匹配单词边界，表示锚定词首，其后面的字符必须作为单词首部出现。
\>或者\b ：匹配单词边界，表示锚定词尾，其前面的字符必须作为单词尾部出现。
\B：匹配非单词边界，与\b正好相反。
 
分组与后向引用
( ) 表示分组，我们可以将其中的内容当做一个整体，分组可以嵌套。
(ab) 表示将ab当做一个整体去处理。
\1 表示引用整个表达式中第1个分组中的正则匹配到的结果。
\2 表示引用整个表达式中第2个分组中的正则匹配到的结果。
```
```
[huawei@n148 reg]$ ifconfig |grep -E "([0-9]{1,3}\.){3}([0-9]{1,3})"
        inet 192.168.88.148  netmask 255.255.255.0  broadcast 192.168.88.255
        inet 127.0.0.1  netmask 255.0.0.0
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255

```
# SED
```
概念看这里
https://www.cnblogs.com/harrymore/p/8676366.html 
```
sed（Stream Editor）意为流编辑器，是Unix常见的命令行程序。是Bell实验室的 Lee E.McMahon 在1973年到1974年之间开发完成，目前可以在大多数操作系统中使用。sed的出现是作为grep的一个继任者，因为grep只能简单的进行查找和替换，但是考虑还可能会有删除等各种需求，McMahon 开发了一个更具通用性的工具。sed著名的语法规则包括使用 / 进行模式匹配，以及 s/// 来进行替代。与同期存在的工具ed一起，sed的语法影响了后来发展的 ECMAScript 和 Perl。GNU sed 添加了很多特性，包括著名的 in-place editing。

sed是一个“非交互式的”面向字符流的编辑器。能同时处理多个文件多行的内容，可以不对原文件改动，把整个文件输入到屏幕,可以把只匹配到模式的内容输入到屏幕上。还可以对原文件改动，但是不会再屏幕上返回结果。 
https://www.cnblogs.com/ctaixw/p/5860221.html  
https://blog.csdn.net/wdz306ling/article/details/80087889  
sed 默认不会修改原始文件，只是将结果内容输出到标准输出设备。如果要保
持变更，应该使用重定向 > filename.txt
sed 正常流程是读取数据、执行命令、打印输出、重复循环。
## 语法
```
sed [options] script filename
```
* 一般来说，sed是从stdin读取输入，并且将输出写出到stdout，但是filename被指定时，会从指定的文件中获取输入，输出可以重定向到另外的文件中。
* options指的是sed的命令行参数，比较有限，这个后面会说明。
* script是指需要对输入执行的一个或者多个操作指令，一般需要用单引号括起来，这样可以避免shell对特殊字符的处理。sed会依次读取输入文件的每一行到缓存中并应用script中指定的操作指令，因此而带来的变化并不会影响最初的文件（除非option加了-i参数）。如果操作指令很多，为了不影响可读性，可以将其写入文件，并用-f参数指定该文件：sed -f scriptfile filename  

无论是将操作指令通过命令行指定，还是写入到文件中作为一个sed脚本，必须包含至少一个指令，否则用sed就没有意义了。  
每条操作指令由pattern和procedure两部分组成，顾名思义，pattern是匹配的规则，一般为用'/'分隔的正则表达式（也有可能是行号，具体参见Sed命令地址匹配问题总结），而procedure则是一连串编辑命令(action)。 sed的处理流程，简化后是这样的：

* 读入新的一行内容到缓存空间；
* 从指定的操作指令中取出第一条指令，判断是否匹配pattern；
* 如果不匹配，则忽略后续的编辑命令，回到第2步继续取出下一条指令；
* 如果匹配，则针对缓存的行执行后续的编辑命令；完成后，回到第2步继续取出下一条指令；
* 当所有指令都应用之后，输出缓存行的内容；回到第1步继续读入下一行内容；
* 当所有行都处理完之后，结束；
具体流程如图所示：
![sed流程](https://images2018.cnblogs.com/blog/741682/201803/741682-20180330151402856-988020953.png)


```
sed中的编辑命令：
i:插入  添加到匹配行的上一行
a:追加  添加到匹配行的下一行
c:更改  更改匹配的行的内容
d:删除  删除匹配的行
s:替换  替换掉匹配的内容
p:打印匹配行（和-n选项一起合用）
=:用来打印被匹配的行的行号
n:输出  只打印模式匹配的行，否则会输出所有
r,w：读和写编辑命令，r用于将内容读入文件，w用于将匹配内容写入到文件
```

## 打印模式空间所有 p（小）
p打印当前模式空间所有内容，通常使还需要配合-n 选项来屏蔽 sed 的默认输出，否则当执行命令 p 时，每行记录会输出两次
```
打印 employee.txt 文件，每行会输出两次：
[huawei@n148 sed]$ sed 'p' employee.txt
101,Johnnyny Doe,CEO
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
105,Jane Miller,Sales Manager

输出 employee.txt 的内容(和 cat employee.txt 命令作用相同):
[huawei@n148 sed]$ sed -n 'p' employee.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```
## 地址（行号）匹配 1,2cmd

如果在命令前面不指定地址范围，那么默认会匹配所有行。下面的例子，在命令前面指定了地址范围：
* n,m 匹配行号范围，代表第 n 至第 m 行，如果当前行是此行号范围内就执行命令，案例见sed模拟tail的打印尾n行
* 波浪号~指定起点与步长。如  
  1~2 匹配 1,3,5,7,……  
  2~2 匹配 2,4,6,8,……  
  1~3 匹配 1,4,7,10,…..  
  2~3 匹配 2,5,8,11,…..  
* 加号+配合逗号使用， 如 n,+m 表示从第 n 行开始后的 m 行  

```
只打印第 2 行
[huawei@n148 sed]$ sed -n '2p' employee.txt
102,Jason Smith,IT Manager
[huawei@n148 sed]$ sed '2!d' employee.txt
102,Jason Smith,IT Manager
[huawei@n148 sed]$ sed '2q;d' employee.txt
102,Jason Smith,IT Manager


打印2到4行
[huawei@n148 sed]$ sed -n '2,4 p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
[huawei@n148 sed]$  sed '2,4!d' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
[huawei@n148 sed]$ sed -n '/[234]/ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
[huawei@n148 sed]$ sed -n '/[2-4]/ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer


打印文件的最后一行
[huawei@n148 sed]$ sed -n '$p'  1.txt
5.I am funning too

打印第 2 行至最后一行($代表最后一行):
[huawei@n148 sed]$ sed -n '2,$ p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

从第二行开始，每隔两行打印一行，波浪号后面的2表示步长
[huawei@n148 sed]$ sed -n '2~3p' employee.txt
102,Jason Smith,IT Manager
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n '2,${p;n;n}' employee.txt
102,Jason Smith,IT Manager
105,Jane Miller,Sales Manager


打印匹配too的行及其向后一行，如果有多行匹配too，则匹配的每一行都会向后多打印一行
[huawei@n148 sed]$ sed  -n '/too/,+1p'  1.txt
5.I am funning too
6.is you
```

## 模式（内容）匹配 /Key/cmd
上面是使用数字指定地址(或地址范围),也可以使用一个模式(或模式范围)来匹配。模式里可以单行也可以多行（多行案例段落匹配）都可被匹配到
```

sed -n -e '/./p' inputfile # 仅输出包含字符的行（即：非空行）
sed -n -e '/^.$/p' inputfile # 仅输出只包含一个字符的行
sed -En -e '/^.{35}$/p' inputfile # 输出精确包含 35 个字符的行
sed -En -e '/^.{0,35}$/p' inputfile # 输出包含 35 个字符或更少字符的行
sed -En -e '/^.{,35}$/p' inputfile # 输出包含 35 个字符或更少字符的行
sed -En -e '/^.{35,}$/p' inputfile # 输出包含 35 个字符或更多字符的行
sed -En -e '/.{35}/p' inputfile # 你自己指出它的输出内容（这是留给你的测试题）
cat -n inputfile | sed -n '/pulse/p' # 输出包含 “pulse” 的行
cat -n inputfile | sed -n '/pulse/{n;p}' # 输出包含 “pulse” 之后的行
cat -n inputfile | sed -n '/pulse/{n;n;p}'  # 输出包含 “pulse” 的行的下一行的下一行

打印匹配模式”Jane”的行:
[huawei@n148 sed]$ sed -n '/Jane/ p' employee.txt
105,Jane Miller,Sales Manager
匹配到则d不删
[huawei@n148 sed]$ sed '/Jane/!d' employee.txt
105,Jane Miller,Sales Manager


打印不匹配模式”Jane”的行:
[huawei@n148 sed]$ sed -n '/Jane/!p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
[huawei@n148 sed]$ sed '/Jane/d' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer

打印匹配key的上一行:
[huawei@n148 sed]$ sed -n '/103/{g;1!p;};h' employee.txt
102,Jason Smith,IT Manager
1 首先-n关闭每轮自动打印模式内容
2 末尾命令h将模式的内容覆盖到保持中，此命令每轮都执行
3 匹配到103时（首先当前保持中备份的是102）然后g将保持覆盖到模式，执行1!p，即如不是第一行则p打印模式内容

打印匹配key的下一行:
[huawei@n148 sed]$ sed -n '/103/{n;p;}' employee.txt
104,Anand Ram,Developer
1 首先-n关闭每轮自动打印模式内容
2 匹配到103后会执行n，把下一行覆盖到模式中，然后p打印了出来


打印key行号与上下文
[huawei@n148 sed]$ sed -n -e '/103/{=;x;1!p;g;$!N;p;D;}' -e h employee.txt
3
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
1 关闭自动打印模式-n，每轮执行h即模式覆盖到保持里
2 匹配到103则=打印行号，交换保持与模式x，此时模式里是102，保持是103
3 如不是第一行1!p则打印模式（打印了102），然后g保持覆盖到模式，此时模式是103，保持是102
4 非末尾$!N则读入下一行附加到模式。然后p打印出103\n104，再D删除模式里的第一行103，剩余104，用于最后的h备份使用，再下一轮读取105不匹配，再h备份，结束程序


匹配一行中同时包含AAA、BBB、CCC的，顺序无所谓，只有在一行即可
首先匹配AAA，如果未匹配则d清掉模式然后下一轮，如匹配到则在模式中继续匹配BBB，如法炮制
[huawei@n148 sed]$ cat ll.txt
1AAA23
4567
890

AbcdBBBefg
qwe
qwe

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123
[huawei@n148 sed]$ sed '/AAA/!d; /BBB/!d; /CCC/!d' ll.txt
123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123



匹配一行中同时包含AAA、BBB、CCC的，要求按顺序
[huawei@n148 sed]$ sed '/AAA.*BBB.*CCC/!d' ll.txt
123AAA123BBB123CCC123


仅打印大于65个字符的行，这里没打印因为echo字符太少了
“/^.\{65\}”代表每行开头连续65个任意字符才会匹配
[huawei@n148 sed]$ echo 'Chinabitious goals.' | sed -n '/^.\{65\}/p'

仅打印少于65个字符的行
[huawei@n148 sed]$ echo 'Chinabitious goals.' | sed -n '/^.\{65\}/!p'
Chinabitious goals.
[huawei@n148 sed]$ echo 'Chinabitious goals.' | sed '/^.\{65\}/d'
Chinabitious goals.


打印从匹配Jason 的行到第4行的内容，如果前4 行中，没有匹配到 Jason,那么 sed 会打印第 4 行以后匹配到 Jason 的内容
[huawei@n148 sed]$ sed -n '/Jason/,4p' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer

打印从第一次匹配 Raj 的行到最后一行的所有行：
[huawei@n148 sed]$ sed -n '/Raj/,$p' employee.txt
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

打印自匹配 Raj 的行开始到匹配 Jane 的行之间的所有内容：
[huawei@n148 sed]$ sed -n '/Raj/,/Jane/p' employee.txt
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

打印匹配 Jason 的行和其后面的两行:
[huawei@n148 sed]$ sed -n '/Jane/,+2p' employee.txt
105,Jane Miller,Sales Manager


打印匹配you 的行到第3行，也打印后面所有匹配you 的行
[huawei@n148 sed]$ sed  -n  '/you/,3p'  1.txt
2.how are you
3.I am funning thanks
4.and you

打印第一行到匹配too的行
[huawei@n148 sed]$ sed  -n '1,/too/p'  1.txt
1.hello bob
2.how are you
3.I am funning thanks
4.and you
5.I am funning too

只打印第三行到匹配you的行
[huawei@n148 sed]$ sed  -n  '3,/you/p'  1.txt
3.I am funning thanks
4.and you


显示包含log并且log后面有内容的行，'/log: *./p'  “空格*”匹配log:后面n个空格，“.”的作用是后接任意一个字符
[huawei@n148 sed]$ cat log.txt
log: input.txt
log:
log: testing resumed
log:
log:output created
[huawei@n148 sed]$ sed -n '/log: *./p' log.txt
log: input.txt
log: testing resumed
log:output created
```
## 地址+模式 匹配 1,/Key/cmd
上面也有部分是2个组合进行匹配的，这里再细致了解一下  
格式：“行号，/key/ 命令”  
解释：行号用于start，如果大于总行数则认为从第一行就匹配成功，key用于end，如果匹配到则使用此行作为end（会从start的下一行开始匹配，如果start行含有k且后续行没有k，则按k匹配失败），否则失败就认为匹配到了最后一行。


```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

从第n行开始，到成功初次匹配k的行，则捕获到此范围内的行，从头开始可以用0、1效果相同。
[huawei@n148 sed]$ sed -n '1,/Manager/p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
[huawei@n148 sed]$ sed -n '0,/Manager/p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager

未匹配到k，则捕获范围的end则是最后一行
[huawei@n148 sed]$ sed -n '0,/XXX/p' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

```
## 打印模式空间开始到\n P（大）
```
见“合并行”
```
## 清理模式后开启新一轮 d
d命令是删除当前模式空间内容后开启新一轮，不会执行d后面的命令（也不打印）
```
执行完d就下一轮了，后面的np不会被执行到，一共执行了5轮
[huawei@n148 sed]$ seq 10|sed -e 'n;d' -e 'n;p'
1
3
5
7
9

[huawei@n148 reg]$ sed 'd' employee.txt
如果不提供地址范围，sed 默认匹配所有行，因为它匹配了所有行并删除了它们,所以什么都不会输出。所以指定要删除的地址范围更有用

[huawei@n148 reg]$ cat 1.txt
111
222
333
444
555
===================================

删除指定行
[huawei@n148 reg]$ sed '4d' 1.txt
111
222
333
555
===================================

从第一行开始删除，每隔1行就删掉一次，即删除奇数行
[huawei@n148 sed]$ seq 10|sed '1~2d'
2
4
6
8
10

===================================

删除1~2行
[huawei@n148 reg]$ sed '1,2d' 1.txt
333
444
555
===================================

删除1~2之外的所有行
[huawei@n148 reg]$ sed '1,2!d' 1.txt
111
222
===================================

删除最后一行
[huawei@n148 reg]$ sed '$d' 1.txt
111
222
333
444
===================================

删除最后2行
[huawei@n148 sed]$ sed 'N;$!P;$!D;$d' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin

先N读一行到模式此时1\n2，P打出1，D删除1，模式里有2，回到N, 2\n3，P打出2，D删除2，模式里有3，回到N
此时3\n4，P打出3，D删除3，模式里有4，回到N, 4\n5，当前行在5，P失败，D失败，d成功

===================================

删除第 2 行至最后一行:
[huawei@n148 sed]$ sed '2,$ d' employee.txt
101,Johnnyny Doe,CEO

===================================

删除匹配含有3的行
[huawei@n148 reg]$ sed '/3/d' 1.txt
111
222
444
555
===================================

删除从匹配3的行到最后一行
[huawei@n148 reg]$ sed '/3/,$d' 1.txt
111
222
===================================


删除从第一次匹配 Jason 的行至第 4 行：如果开头的 4 行中，没有匹配 Jason 的行，那么上述命令将删除第 4 行以后匹配 Jason的行
[huawei@n148 sed]$ sed '/Jason/,4 d' employee.txt
101,Johnnyny Doe,CEO
105,Jane Miller,Sales Manager



删除匹配2的行及其后面2行
[huawei@n148 reg]$ sed '/2/,+2d' 1.txt
111
555
===================================

删除所有注释与空行
[huawei@n148 sed]$ sed '/^$\|^#/d' /etc/profile

先将注释替换为空行，再执行删除空行，使用了-e
[huawei@n148 sed]$ sed -e 's/#.*// ; /^$/ d' /etc/profile

===================================

删除空行
[huawei@n148 reg]$ sed '/./!d' 1.txt	有内容则保留
[huawei@n148 reg]$ sed '/^$/d' 1.txt
111
222
333
444
555
===================================

[huawei@n148 reg]$ cat test.txt
AAA
#正常的空行

BBBB

#由Tab组成的空行
AAABBBB
      
#由空格组成的空行
CCOCC

#正常的空行
DD
[huawei@n148 reg]# sed '/[[:space:]]/d' test.txt #删除由tab和空格组成的空行
AAA

BBBB
AAABBBB
CCOCC

DD


[huawei@n148 reg]# sed '/^$/d' test.txt #删除空行
AAA
BBBB

AAABBBB

CCOCC
DD


[huawei@n148 reg]# sed -e '/[[:space:]]/d' -e '/^$/d' test.txt #删除各种空行
AAA
BBBB
AAABBBB
CCOCC
DD

删除文件顶部的所有空行。从有字符开始的行直到最后一行保留，其他删除.
[huawei@n148 sed]$  sed '/./,$!d' 2.txt
111
222

333


444



555
===================================
删除文件尾部的所有空行
^\n*$ 用于匹配连续n个空行，匹配到了执行{ }中的内容，貌似如果文件多个连续空行，会一次读进来
$d只有最后一行时候才会删除。否则是N追加新行到模式后b到a重新此轮，循环到把1追加进来后不匹配多空行后执行‘}’则认为是命令完整了‘//{}’（实际是没匹配到啥也不干），打印模式里的内容，重新下一轮，这样可以把连续多空行之前的所有内容都完整打印出来。到最结尾如果是连续多空行到最后一行则$d成功都删掉
[huawei@n148 sed]$ sed -e :a -e '/^\n*$/{$d;N;ba' -e '}' 2.txt



111
222

333


444



555
===================================

删除不匹配2或3的行，/2\|3/ 表示匹配2或3 ，！表示取反
[huawei@n148 reg]$ sed '/2\|3/!d' 1.txt
222
333
===================================

删除1~3行中，匹配内容2的行，1,3表示匹配1~3行，{/2/d}表示删除
[huawei@n148 reg]$ sed '1,3{/2/d}' 1.txt
111
333
444
555
===================================

删除start与end之间的行（包含start行与end行）但如果end在start之前，则仅删除start行与end行
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed '/IT/,/104/d' employee.txt
101,John Doe,CEO
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed '/104/,/IT/d' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin

===================================
删除最后5行
[huawei@n148 sed]$ seq 10|sed -e :a -e '$d;N;2,5ba' -e 'P;D'
1
2
3
4
5

1 N后模式内1\n2，当前行是2在2,5范围内b回到a，重复直到模式里是123456退出循环
2 P打印1，D删除1，模式内23456，回到sed重新开始所有命令，当前行是6
3 N后是234567，当前行是7，不在2，5中，P打印2，D删除2，模式内34567，回到sed重新开始所有命令，当前行是7
4 N后是345678，当前行是8，不在2，5中，P打印3，D删除3，模式内45678，回到sed重新开始所有命令，当前行是8
5 N后是456789，当前行是9，不在2，5中，P打印4，D删除4，模式内56789，回到sed重新开始所有命令，当前行是9
6 N后是5678910，当前行是10，不在2，5中，P打印5，D删除3，模式内5678910，回到sed重新开始所有命令，当前行是10
7 已是最后一行$d被执行，清理模式


[huawei@n148 sed]$ seq 10|sed  -e :a -e '1,5!{P;N;D;};N;ba'
1
2
3
4
5

1 -n不打印，'1,5!{}'则前5行不执行，能够重复到模式里123456，当前行是6，
2 当前行6时候模式是123456，P打1，N追加7到模式，当前行是7，D删除1，回到sed重新开始所有命令
3 当前行7时候模式是234567，P打2，N追加8到模式，当前行是8，D删除2，回到sed重新开始所有命令
4 当前行8时候模式是345678，P打3，N追加9到模式，当前行是9，D删除3，回到sed重新开始所有命令
5 当前行9时候模式是456789，P打4，N追加10到模式，当前行是10，D删除4，回到sed重新开始所有命令
6 当前行10时候模式是5678910，P打5，N失败，(后面是猜测了：D成功，N失败，ba回不回也无所谓了，反正没有新行了

===================================
[huawei@n148 sed]$ cat 2.txt



111
222

333


444



555

只保留多个相邻空行的第一行。并且删除文件顶部的空行
[huawei@n148 sed]$ sed '/./,/^$/!d'     2.txt
111
222

333

444

555

/./,/^$/ 匹配2个k之间的内容，此处是一个行的范围，‘.’匹配有内容的行，^$匹配空行，则此正则匹配有内容的行到下面的第一个空行
！d则不删除，否则就删除了
===================================
只保留多个相邻空行的第一行。并且顶部保留一空行（原文件顶部有空行才会保留）
[huawei@n148 sed]$ sed '/^$/N;/\n$/D' 2.txt

111
222

333

444

555

第一行空匹配^$后N，此时2个空行，匹配\n$后D去除第一个空行，模式里还有1个空行，重来本轮
第2行空匹配^$后N，此时2行，分别是空行\n1，不匹配\n$，打印出空行\n1。
有内容的连续行会被依次打印。再遇空行则重新回到开头处那样处理，5后面再新轮时候会先读入空行，匹配^$,在N失败，结束sed，打印模式里的空行

===================================



删除每个段落中最后1行
[huawei@n148 sed]$ cat data.txt

Guangdong CDC today reminds people with a travel history to
Chaoyang District and Haidian District of Beijing commencing
October 27 to report to the community and cooperate with local
October 27 to report to the community and cooperate with local


The province has stopped the corresponding health management for people
from these three former medium-risk areas (Xilin community, Xicheng
community and Eren community in Erenhot City, Xilin Gol League, Inner Mongolia)

On November 10, Guangdong CDC updated the health management measures for
people coming to Guangdong from key areas.
[huawei@n148 sed]$ sed -n '/^$/{p;h;};/./{x;/./p;}'  data.txt

Guangdong CDC today reminds people with a travel history to
Chaoyang District and Haidian District of Beijing commencing
October 27 to report to the community and cooperate with local


The province has stopped the corresponding health management for people
from these three former medium-risk areas (Xilin community, Xicheng

On November 10, Guangdong CDC updated the health management measures for
说明：
第一行是空，则p打印后h覆盖到保持，此时保持是空行，模式里的空行不匹配第二个正则，进入下一轮
第二行有内容匹配第二个正则，x交换后模式是空，保持有内容，再匹配.则失败不p，进入下一轮
第三行有内容匹配第二个正则，x交换后模式有内容，保持有内容，匹配.成功打印p出原文第一行内容
后面有内容行会一直打印上一行内容，知道遇到空行，则匹配^$成功p打印出模式里的空行，再h将空行覆盖到保持，至此保持里的内容被清了。。。
后面重复上面的分析直到结束
```

## 删除模式的开始到\n后重来本轮 D
清除模式空间中开始到\n，如果清除后模式空间仍有剩余行，则重来本轮。注意D有循环的意思在里面。如果模式空间只有一行则D与d一样
```
读取1，执行N，得出1\n2，执行D删除1，模式2，执行N，得出2\n3，执行D，得出3，依此类推，得出5，执行N，条件失败退出，因无-n参数，故输出5。仅执行了一轮，因为D一直在反复当前轮
[huawei@n148 sed]$ seq 5|sed 'N;D'
5

也可参考“合并行”案例
```
## 把模式空间内容写到文件中 w
可以把当前模式空间的内容保存到文件中。默认情况下模式空间的内容每次都会打印到标准输出，如果要把输出保存到文件同时不显示到屏幕上，还需要使用-n 选项。

```
把 employee.txt 的内容保存到文件 output.txt，同时显示在屏幕上，类似复制了文件。。。
[huawei@n148 sed]$ sed 'w output.txt' employee.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ cat output.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


[huawei@n148 sed]$ cat 1.txt
123
245
789
[huawei@n148 sed]$ cat 2.txt
abc
xyz
qqw
===================================

将1.txt文件的内容写入2.txt文件，如果2.txt文件不存在则创建，如果2.txt存在则覆盖之前的内容，且不在屏幕上显示
[huawei@n148 sed]$ sed  -n  'w 2.txt'   1.txt
[huawei@n148 sed]$ cat 2.txt
123
245
789
===================================

将文件1.txt中的第2行内容写入到文件2.txt
[huawei@n148 sed]$ sed   -n '2w  2.txt'   1.txt
[huawei@n148 sed]$ cat 2.txt
245
===================================

保存第 1 至第 4 行：
[huawei@n148 sed]$ sed -n '1,4 w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer


保存第 2 行起至最后一行：
[huawei@n148 sed]$ sed -n '2,$ w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

只保存奇数行：
[huawei@n148 sed]$ sed -n '1~2 w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
101,Johnnyny Doe,CEO
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager



将1.txt的第1行和最后一行内容写入2.txt
[huawei@n148 sed]$ sed  -n -e '1w  2.txt'  -e '$w 2.txt'   1.txt
[huawei@n148 sed]$ cat 2.txt
123
789
===================================

将1.txt的第1行和最后一行分别写入2.txt和3.txt
[huawei@n148 sed]$ sed  -n -e '1w  2.txt'  -e '$w  3.txt'  1.txt
[huawei@n148 sed]$ cat 2.txt
123
[huawei@n148 sed]$ cat 3.txt
789
===================================

保存匹配 Jane 的行
[huawei@n148 sed]$ sed -n '/Jane/ w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
105,Jane Miller,Sales Manager



将1.txt中匹配abc或123的行的内容，写入到2.txt中
[huawei@n148 sed]$ cat 1.txt
123
abc
xyz
666
123,xyz,abc
[huawei@n148 sed]$ sed  -n  '/abc\|123/w  2.txt'    1.txt
[huawei@n148 sed]$ cat 2.txt
123
abc
123,xyz,abc
===================================

保存第一次匹配 Jason 的行至第 4 行：注意：如果开始的 4 行里没有匹配到 Jason,那么该命令只保存第 4 行以后匹配到 Jason 行
[huawei@n148 sed]$ sed -n '/Jason/,4 w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer




将1.txt中从匹配666的行到最后一行的内容，写入到2.txt中
[huawei@n148 sed]$ sed  -n '/666/,$w 2.txt'   1.txt
[huawei@n148 sed]$ cat 2.txt
666
123,xyz,abc
===================================

保存匹配 Raj 的行至匹配 Jane 的行:
[huawei@n148 sed]$ sed -n '/Raj/,/Jane/ w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager




将1.txt中从匹配xyz的行及其后2行的内容，写入到2.txt中
[huawei@n148 sed]$ cat 1.txt
666
999
xyz
111
333
555
[huawei@n148 sed]$ sed  -n  '/xyz/,+2w  2.txt'     1.txt
[huawei@n148 sed]$ cat 2.txt
xyz
111
333
```



## 插入 i
在指定位置之前插入行
```
[huawei@n148 sed]$ cat employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在 employee.txt 的第 2 行之前插入一行:
[huawei@n148 sed]$  sed '2 i 203,Jack Johnson,Engineer' employee.txt
101,Johnny Doe,CEO
203,Jack Johnson,Engineer
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在 employee.txt 最后一行之前，插入一行
[huawei@n148 sed]$ sed '$ i 108,Jack Johnson,Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
108,Jack Johnson,Engineer
105,Jane Miller,Sales Manager

在匹配 Jason 的行的前面插入两行:
[huawei@n148 sed]$ sed '/Jason/i\
203,Jack Johnson,Engineer\
204,Mark Smith,Sales Engineer' employee.txt
101,Johnny Doe,CEO
203,Jack Johnson,Engineer
204,Mark Smith,Sales Engineer
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```

## 追加 a
在指定位置的后面插入新行。
```

在第 2 行后面追加一行
[huawei@n148 sed]$ sed '2 a 203,Jack Johnson,Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
203,Jack Johnson,Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在 employee.txt 文件结尾追加一行:
[huawei@n148 sed]$ sed '$ a 106,Jack Johnson,Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
106,Jack Johnson,Engineer

在匹配 Jason 的行的后面追加两行:
[huawei@n148 sed]$ sed '/Jason/a 203,Jack Johnson,Engineer\n204,Mark Smith,Sales Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
203,Jack Johnson,Engineer
204,Mark Smith,Sales Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在匹配 Jason 的行的后面追加两行:
[huawei@n148 sed]$ sed '/Jason/a\
203,Jack Johnson,Engineer\
204,Mark Smith,Sales Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
203,Jack Johnson,Engineer
204,Mark Smith,Sales Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

```
## 修改 c
用新行取代旧行
```

用新数据取代第 2 行:
这里命令 c 等价于替换：$ sed '2s/.*/202,Jack,Johnson,Engineer/' employee.txt
[huawei@n148 sed]$ sed '2 c 202,Jack,Johnson,Engineer' employee.txt
101,Johnny Doe,CEO
202,Jack,Johnson,Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


用两行新数据取代匹配 Raj 的行:
[huawei@n148 sed]$ sed '/Raj/c 203,Jack Johnson,Engineer\n204,Mark Smith,Sales Engineer' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
203,Jack Johnson,Engineer
204,Mark Smith,Sales Engineer
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

更改最末行
[huawei@n148 sed]$ sed '$ c end' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
end
```
## 组合a、i、c
* a 在”Jason”后面追加”Jack Johnson”
* i 在”Jason”前面插入”Mark Smith”
* c 用”Joe Mason”替代”Jason”
其实就是修改第2行，并在第二行前面加一行，后面加一行这么简单。。。
```
[huawei@n148 sed]$ cat employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

[huawei@n148 sed]$ sed '/Jason/ {
> a\
> 204,Jack Johnson,Engineer
> i\
> 202,Mark Smith,Sales Engineer
> c\
> 203,Joe Mason,Sysadmin
> }' employee.txt
101,Johnny Doe,CEO
202,Mark Smith,Sales Engineer
203,Joe Mason,Sysadmin
204,Jack Johnson,Engineer
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```
## 打印不可见字符、指定行的长度 l

```
可打印出\t与\n，但实验\t未显示，\n是$
[huawei@n148 sed]$ sed -n 'l' tabfile.txt
fname  First Name$
lname  Last Name$
mname  Middle Name$


在第 n 个字符处使用一个不可见自动折行，但行尾会加\,且原始行尾加了$...
[huawei@n148 sed]$ sed -n 'l 25' employee.txt
101,Johnny Doe,CEO$
102,Jason Smith,IT Manag\
er$
103,Raj Reddy,Sysadmin$
104,Anand Ram,Developer$
105,Jane Miller,Sales Ma\
nager$
```
## 行号 =
简单使用
```
打印所有行号:
[huawei@n148 sed]$ sed = employee.txt
[huawei@n148 sed]$ sed '=' employee.txt
[huawei@n148 sed]$ sed '/./=' employee.txt
1
101,Johnny Doe,CEO
2
102,Jason Smith,IT Manager
3
103,Raj Reddy,Sysadmin
4
104,Anand Ram,Developer
5
105,Jane Miller,Sales Manager

只打印 1,2,3 行的行号
[huawei@n148 sed]$ sed '1,3 =' employee.txt
1
101,Johnny Doe,CEO
2
102,Jason Smith,IT Manager
3
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

打印包含关键字”Jane”的行的行号，同时打印输入文件中的内容：
[huawei@n148 sed]$ sed '/Jane/ =' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
5
105,Jane Miller,Sales Manager


打印匹配Raj的行的行号,即只显示行号但不显示行的内容
[huawei@n148 sed]$ sed -n '/Raj/ =' employee.txt
3

打印匹配Raj的行的行号和内容（可用于查看日志中有error的行及其内容）
[huawei@n148 sed]$ sed -n '/Raj/ {=;p}' employee.txt
3
103,Raj Reddy,Sysadmin

打印文件的总行数，与wc -l一样
-n限制自动打印模式空间中内容,$是最后一行，=是打印行号
[huawei@n148 sed]$ sed -n '$=' employee.txt
5
```
高级应用
```
给文件每一行加上数字行号。用TAB制表符替换空间来保留空白
1 sed = filename的功能是是在每一行前面另加一行，并且显示行号,而不是直接在行首加序号
2 命令N就是把当前行后一行的内容加在当前行后边
3 命令s用\t换\n
[huawei@n148 sed]$ sed = employee.txt|  sed 'N;s/\n/\t/'
1       101,John Doe,CEO
2       102,Jason Smith,IT Manager
3       103,Raj Reddy,Sysadmin
4       104,Anand Ram,Developer
5       105,Jane Miller,Sales Manager

给文件每一行加上数字序号(数字在左边，数字右边内容对齐) 
1 首先“s/^/     /”在行首添加空格
2 后面的还是替换，牙疼懒得分析了...
[huawei@n148 sed]$ sed = employee.txt | sed 'N; s/^/     /; s/ *\(.\{6,\}\)\n/\1  /'
     1  101,John Doe,CEO
     2  102,Jason Smith,IT Manager
     3  103,Raj Reddy,Sysadmin
     4  104,Anand Ram,Developer
     5  105,Jane Miller,Sales Manager

```
## 大小写转换 y
```
把 a 换为 A，b 换为 B，c 换为 C，以此类推:
[huawei@n148 sed]$ sed 'y/abcde/ABCDE/' employee.txt
101,Johnny DoE,CEO
102,JAson Smith,IT MAnAgEr
103,RAj REDDy,SysADmin
104,AnAnD RAm,DEvElopEr
105,JAnE MillEr,SAlEs MAnAgEr

把所有小写字符转换为大写字符:
[huawei@n148 sed]$ sed 'y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/' employee.txt
101,JOHNNY DOE,CEO
102,JASON SMITH,IT MANAGER
103,RAJ REDDY,SYSADMIN
104,ANAND RAM,DEVELOPER
105,JANE MILLER,SALES MANAGER
```
## 操作多个文件
```
在/etc/passwd 中搜索 root 并打印出来：
[huawei@n148 sed]$ sed -n '/root/ p' /etc/passwd
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin

在/etc/group 中搜索 root 并打印出来：
[huawei@n148 sed]$ sed -n '/root/ p' /etc/group
root:x:0:

同时在/etc/passwd 和/etc/group 中搜索 root:
[huawei@n148 sed]$ sed -n '/root/ p' /etc/passwd /etc/group
root:x:0:0:root:/root:/bin/bash
operator:x:11:0:operator:/root:/sbin/nologin
root:x:0:
```

## 退出 q
之前提到，正常的 sed 执行流程是：读取数据、执行命令、打印结果、重复循环。
当 sed 遇到 q 命令，便立刻退出，当前循环中的后续命令不会被执行，也不会继续循环。
注意：q 命令不能指定地址范围(或模式范围),只能用于单个地址(或单个模式)。
```
打印第 1 行后退出:
[huawei@n148 sed]$ sed 'q' employee.txt
101,Johnny Doe,CEO

打印第 3 行后退出，即只打印前 3 行：
[huawei@n148 sed]$ sed '3 q' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin

打印所有行，直到遇到包含关键字 Manager 的行:
[huawei@n148 sed]$ sed '/Manager/ q' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager

```
## 从文件读取数据 r
从另外一个文件读取内容，并在指定的位置打印出来
```
[huawei@n148 sed]$ cat 1.txt
123
245
789
[huawei@n148 sed]$ cat 2.txt
abc
xyz
qqw
===================================

将文件2.txt中的内容，读入1.txt中，会在1.txt中的每一行后都读入2.txt的内容
[huawei@n148 sed]$ sed  'r 2.txt'  1.txt
123
abc
xyz
qqw
245
abc
xyz
qqw
789
abc
xyz
qqw
===================================

在1.txt的第2行之后插入文件2.txt的内容（可用于向文件中插入内容）
[huawei@n148 sed]$ sed '2r 2.txt'  1.txt
123
245
abc
xyz
qqw
789
===================================

在匹配245的行之后插入文件2.txt的内容，如果1.txt中有多行匹配456则在每一行之后都会插入
[huawei@n148 sed]$ sed  '/245/r   2.txt'   1.txt
123
245
abc
xyz
qqw
789
===================================

打印1.txt后打印2
[huawei@n148 sed]$ sed  '$r  2.txt'   1.txt
123
245
789
abc
xyz
qqw
```

## 替换 s
流编辑器中最强大的功能就是替换substitute。原始输入文件不会被修改，sed 只在模式空间中执行替换命令，然后输出模式空间的内容。

语法
```
sed ‘[address-range|pattern-range] s/original-string/replacement-string/[substitute-flags]’ input file
```
上面提到的语法为：
* address-range 或 pattern-range(即地址范围和模式范围)是可选的。如果没有指
定，那么 sed 将在所有行上进行替换。
* s 即执行替换命令 substitute
* original-string 是被 sed 搜索然后被替换的字符串，它可以是一个正则表达式
* replacement-string 替换后的字符串
* substitute-flags 是可选的，下面会具体解释
### 行首、行尾追加内容、去除行首字符串
```
[huawei@n148 sed]$ sed 's/^/> /' employee.txt
> 101,John Doe,CEO
> 102,Jason Smith,IT Manager
> 103,Raj Reddy,Sysadmin
> 104,Anand Ram,Developer
> 105,Jane Miller,Sales Manager

去除上面插入的内容
[huawei@n148 sed]$ sed -e 's/^/> /' -e  's/^> //'  employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在每行尾加上"haha"字段，注意空行直接变为了haha,可以省略“//&/”里的&，效果一样否是追加到行尾
[huawei@n148 reg]$ cat 1.txt
#abc,123
123#$
#def,?
#456@qq
123,#$%%

hello
[huawei@n148 reg]$ sed  's/$/haha/'  1.txt
[huawei@n148 reg]$ sed  's/$/&haha/'  1.txt
#abc,123haha
123#$haha
#def,?haha
#456@qqhaha
123,#$%%haha
haha
hellohaha
```
### 替换包含key的行
```
将文件中的2替换为hello，默认只替换每行第一个2

[huawei@n148 reg]$ sed 's/2/hello/' 1.txt
111
hello22
333
444
555

只把包含 Sales 的行中的 Manager 替换为 Directo,注意：本例由于使用了地址范围限制，所以只有一个 Manager 被替换了

[huawei@n148 sed]$ sed '/Sales/s/Manager/Director/' employee.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Director

```
### 在不含A的行中用B替换C  !s
```
在每一不含有"baz"的行中用"bar"替换foo"
[huawei@n148 sed]$ cat doc.txt
bazfoo
bafoo
barfoo
[huawei@n148 sed]$ sed '/baz/!s/foo/bar/g' doc.txt
bazfoo
babar
barbar
```
### 替换所有的匹配 g
```
g 代表全局(global)。 默认情况下，sed 至会替换每行中第一次出现的 original-string。如果你要替换每行中出现的所有 original-string,就需要使用 g。将文本中所有的2都替换为hello。会在所有行上替换，因为没有指定地址范围。

[huawei@n148 reg]$ sed 's/2/hello/g' 1.txt
111
hellohellohello
333
444
555
```
### 指定仅替换第几次匹配

```
使用数字可以指定 original-string 出现的次序。只有第 n 次出现的 original-string 才会触发替换。每行的数字从 1 开始，最大为 512。
将每行中第3个匹配的2替换为hello

[huawei@n148 reg]$ sed 's/2/hello/3' 1.txt
111
22hello
333
444
555
```
### 替换指定行的倒数第几次
```
替换倒数第2个
1 把\去掉后，就变成(.*)foo(.*foo),其中(.*)匹配前3个foo，对应分组1；(.*foo)匹配最后1个foo，对应组2
2 why.*可以匹配那么多？见sed正则的贪婪匹配
[huawei@n148 sed]$ echo "foofoofoofoofoo" | sed 's/\(.*\)foo\(.*foo\)/\1bar\2/'
foofoofoobarfoo

替换倒数第1个
[huawei@n148 sed]$ echo "foofoofoofoofoo" | sed 's/\(.*\)foo/\1bar/'
foofoofoofoobar

```
### sed正则的贪婪匹配 .*
```
[huawei@n148 sed]$ echo "123:456:789" | sed 's/:.*//'
123
[huawei@n148 sed]$ echo "123:456:789" | sed 's/.*://'
789
[huawei@n148 sed]$ echo "123:456:789"|sed 's/[^:]*:\(.*\)/\1/'
456:789
第一个替换中 .* 是包含所有的字符，它会贪婪的匹配到最后一个:之前的所有字符，那么替换成空，自然会留下了789，第二个替换道理一样，所以剩下了123。第三个是从头开始把非冒号的内容和第一个冒号排除掉，剩下的做标记，就可以取出剩下的内容


最后一个1替换成Hello
[huawei@n148 sed]$ echo "a 1 c 1 d 1 e 1"|sed 's/\(.*\)1/\1Hello/'
a 1 c 1 d 1 e Hello

在匹配1时标记后面多了一个空格，原内容最后一个1后面是没有空格的，正则就会匹配到倒数第二个1
[huawei@n148 sed]$ echo "a 1 c 1 d 1 e 1"|sed 's/\(.*\)1 /\1Hello /'
a 1 c 1 d Hello e 1


[huawei@n148 sed]$ cat doc.txt
/experience/hcj/A420103WBXZ073000021101280013801
/Cat34445/A2_Further_Maths/56/C420103WBPX010100041104200202329
/Deleted/A1_332/35/B420103WBPX010100041104200202329
/Actor/A2_334/11/A420103WBPX010100041104200202329
/Booting/A3335/32/A420103WBPX01010004110420020232

每行中存在若干个"/A" "/B" "/C" ，只替换最后一个匹配的为制表符，结果如下
[huawei@n148 sed]$ sed 's#\(.*\)/[ABC]#\1\t#' doc.txt
/experience/hcj 420103WBXZ073000021101280013801
/Cat34445/A2_Further_Maths/56   420103WBPX010100041104200202329
/Deleted/A1_332/35      420103WBPX010100041104200202329
/Actor/A2_334/11        420103WBPX010100041104200202329
/Booting/A3335/32       420103WBPX01010004110420020232
这就是利用了正则的贪婪匹配，标记出 \(.*\)内容本身也包含了所有字符，那么它会一直匹配到最后一个出现的字样
```
### 打印标志 p(print)
```
命令 p 代表 print。当替换操作完成后，打印替换后的行。与其他打印命令类似，sed 中比较有用的方法是和-n 一起使用以抑制默认的打印操作。

只打印替换后的行：
[huawei@n148 sed]$ sed -n 's/John/Johnny/p' employee.txt
101,Johnnynyny Doe,CEO


把每行中第二次出现的 locate 替换为 find 并打印出来：
[huawei@n148 sed]$ cat substitute-locate.txt
locate command is used to locate files
locate command uses database to locate files
locate command can also use regex for searching
[huawei@n148 sed]$ sed -n 's/locate/find/2p' substitute-locate.txt
locate command is used to find files
locate command uses database to find files


将匹配“j... ”的替换成“Jason ”并打印
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n 's/J... /Jason /p' employee.txt
101,Jason Doe,CEO
105,Jason Miller,Sales Manager
```
### 写标志 w

```
标志 w 代表 write。当替换操作执行成功后，它把替换后的结果保存的文件中。多数人更倾向于使用 p 打印内容，然后重定向到文件中。为了对 sed 标志有个完整的描述，在这里把这个标志也提出来了。


只把替换后的内容写到 output.txt 中,和之前使用的命令 p 一样，使用 w 会把替换后的内容保存到文件 output.txt 中。
[huawei@n148 sed]$ sed -n 's/John/Johnny/w output.txt' employee.txt
[huawei@n148 sed]$ cat output.txt
101,Johnnynyny Doe,CEO


把每行第二次出现的 locate 替换为 find，把替换的结果保存到文件中，同时显示输入文件所有内容：
[huawei@n148 sed]$ sed 's/locate/find/2w output.txt' substitute-locate.txt
locate command is used to find files
locate command uses database to find files
locate command can also use regex for searching
[huawei@n148 sed]$ cat output.txt
locate command is used to find files
locate command uses database to find files


将每行中所有匹配的2替换为hello，并将替换后的内容写入2.txt，这里仅打印了替换后的匹配行
[huawei@n148 reg]$ sed -n 's/2/hello/gpw 2.txt' 1.txt	
hellohellohello
[huawei@n148 reg]$ cat 2.txt
hellohellohello
```
### 忽略大小写标志 i
```
替换标志 i 代表忽略大小写。可以使用 i 来以小写字符的模式匹配 original-string。该标志只有 GNU Sed 中才可使用。
本地测试不起作用 sed 's/john/Johnny/i' employee.txt
```

### 执行命令标志 e
```
替换标志 e 代表执行(execute)。该标志可以将模式空间中的任何内容当做 shell 命令执行，并把命令执行的结果返回到模式空间。该标志只有 GNU Sed 中才可使用。

[huawei@n148 sed]$ cat files.txt
/etc/passwd
/etc/group

在 files.txt 文件中的每行前面添加 ls –l 并打击结果：
[huawei@n148 sed]$ sed 's/^/ls -l/' files.txt
ls -l/etc/passwd
ls -l/etc/group


在 files.txt 文件中的每行前面添加 ls –l 并把结果作为命令执行:
[huawei@n148 sed]$ sed 's/^/ls -l /e' files.txt
-rw-r--r-- 1 root root 2650 Sep 24 15:08 /etc/passwd
-rw-r--r-- 1 root root 1115 Sep 24 15:08 /etc/group

```
### 替换命令分界符，即替换掉/
```
默认的分界符/,即 s/original-string/replacement-string/g,如果在original-string 或 replacement-string 中有/,那么需要使用反斜杠\来转义

如把/usr/local/bin 替换为/usr/bin
[huawei@n148 sed]$ cat path.txt
reading /usr/local/bin directory
[huawei@n148 sed]$ sed 's/\/usr\/local\/bin/\/usr\/bin/' path.txt
reading /usr/bin directory
如果要替换一个很长的路径，每个/前面都使用\转义，会显得很混乱。以使用任何一个字符作为 sed 替换命令的分界符，如 | 或 ^ 或 @ 或者 !。


[huawei@n148 sed]$ sed 's|/usr/local/bin|/usr/bin|' path.txt
reading /usr/bin directory
[huawei@n148 sed]$ sed 's^/usr/local/bin^/usr/bin^' path.txt
reading /usr/bin directory
[huawei@n148 sed]$ sed 's@/usr/local/bin@/usr/bin@' path.txt
reading /usr/bin directory
[huawei@n148 sed]$ sed 's!/usr/local/bin!/usr/bin!' path.txt
reading /usr/bin directory

```

### 语句块 { }
```
即单行内容上执行多个命令，sed中可以用{}来组合命令，就好比编程语言中的语句块。
sed执行的过程是读取内容、执行命令、打印结果、重复循环。其中执行命令部分，可以由多个命令执行，sed 将一个一个地依次执行它们。例如，你有两个命令，sed 将在模式空间中执行第一个命令，然后执行第二个命令。如果第一个命令改变了模式空间的内容，第二个命令会在改变后的模式空间上执行(此时模式空间的内容已经不是最开始读取进来的内容了)。

把 Developer 替换为 IT Manager,然后把 Manager 替换为 Director:
2种样式均测试失败
[huawei@n148 sed]$ sed '{
> s/Developer/IT Manager/
> s/Manager/Director/
> }'employee.txt
sed: -e expression #1, char 48: extra characters after command


[huawei@n148 sed]$ sed 's/Developer/IT Manager/ s/Manager/Director/' employee.txt
sed: -e expression #1, char 25: unknown option to `s'

```
### 删除行首、行尾的空白
```
删除每一行开头的空白(空格，TAB)左对齐排列全文
^[ \t]* 的含义为以空格或者TAB键开始的(或者是他们的组合)行
[huawei@n148 postdb]$ sed 's/^[ \t]*//'  user.c

在每一行结尾处删除最后的空格(空格,TAB)，跟上边的命令"前后呼应"
[huawei@n148 postdb]$ sed 's/[ \t]*$//'  user.c

删除每一行的开头和结尾的空格，上面的2个都有了
[huawei@n148 postdb]$ sed 's/^[ \t]*//;s/[ \t]*$//' user.c
```
### 其他高级些的案例
```
匹配有#号的行，替换匹配行中逗号后的所有内容为空  (,.*)表示逗号后的所有内容
[huawei@n148 reg]$ cat 1.txt
#abc,123
#123,{[?>|
,def#
#456,%$#
123#,hello
[huawei@n148 reg]$ sed '/#/s/,.*//g' 1.txt 
#abc
#123

#456
123#
```
### 替换#注释为空行，并删除
将文件中以#开头的行替换为空行，即注释的行。( ^#)表示匹配以#开头，（.*）代表所有内容
```
[huawei@n148 reg]$ cat 1.txt
#abc,123
123#$
#def,?
#456@qq
123,#$%%
[huawei@n148 reg]$ sed 's/^#.*//'  1.txt

123#$


123,#$%%

```
先替换文件中所有带注释行为空行，然后删除空行，替换和删除操作中间用分号隔开
```
[huawei@n148 reg]$ cat 1.txt
#abc,123
123#$
#def,?
#456@qq
123,#$%%

hello
[huawei@n148 reg]$ sed 's/^#.*//;/^$/d'  1.txt	
123#$
123,#$%%
hello
```
### 替换每行最后的两个字符
每个点代表一个字符，$表示匹配末尾  （..$）表示匹配最后两个字符，只有2个字符的行则清空了
```
[huawei@n148 reg]$ cat 1.txt
abc,123
123#$
def,?
456@qq
12
[huawei@n148 reg]$ sed 's/..$//g' 1.txt
abc,1
123
def
456@

```
### 101变为[101]
```
[huawei@n148 sed]$ cat 1.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed 's/^[0-9][0-9][0-9]/[&]/g' 1.txt
[101],John Doe,CEO
[102],Jason Smith,IT Manager
[103],Raj Reddy,Sysadmin
[104],Anand Ram,Developer
[105],Jane Miller,Sales Manager
```
### 把每一行放进< >中
```
[huawei@n148 sed]$ sed 's/^.*/<&>/' 1.txt
<101,John Doe,CEO>
<102,Jason Smith,IT Manager>
<103,Raj Reddy,Sysadmin>
<104,Anand Ram,Developer>
<105,Jane Miller,Sales Manager>
```

## 执行sed脚本，选项 -f
把具体的sed命令放入文件中然后使用-f 选项来调用，也可以使用—file 来代替-f
```
[huawei@n148 sed]$ cat test-script.sed
/^root/ p
/^nobody/ p

[huawei@n148 sed]$ sed -n -f test-script.sed  /etc/passwd
root:x:0:0:root:/root:/bin/bash
nobody:x:99:99:Nobody:/:/sbin/nologin


[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

[huawei@n148 sed]$ cat mycommands.sed
s/\([^,]*\),\([^,]*\),\(.*\).*/\2,\1, \3/g
s/^.*/<&>/
s/Developer/IT Manager/
s/Manager/Director/

[huawei@n148 sed]$ sed -f mycommands.sed employee.txt
<John Doe,101, CEO>
<Jason Smith,102, IT Director>
<Raj Reddy,103, Sysadmin>
<Anand Ram,104, IT Director>
<Jane Miller,105, Sales Director>

```
## 多套命令，选项 -e 或 ；以及 { }
即执行多个，也可以使用—expression 来代替。
```
[huawei@n148 sed]$ sed -n -e '/^root/ p' -e '/^nobody/ p' -e '/^mail/ p' /etc/passwd
root:x:0:0:root:/root:/bin/bash
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin

有些版本可以更简洁一些使用“；”分开
[huawei@n148 ~]$ seq 6 | sed -e 's/1/10/;s/2/20/'
10
20
3
4
5
6

[huawei@n148 ~]$ seq 10|sed -n '5{p;q}'
5
[huawei@n148 ~]$ seq 10|sed -n '5{p;q;}'
5

也可分为多行
[huawei@n148 ~]$ seq 10|sed -n '5{
  p
  q
}'
5
[huawei@n148 ~]$ seq 10|sed -n '
  5p
  5q
'
5


不是最后一行则h将模式覆盖到保持，不是最后一行则d清理模式，所以打印除了最后一行，作用不是重点，看第2次的{}式样语法
[huawei@n148 sed]$ seq 5|sed  '$!h;$!d'
5
[huawei@n148 sed]$ seq 5|sed  '$!{h;d}'
5
```
## 写会原文件，选项 -i
为了修改输入文件，通常方法是把输出重定向到一个临时文件，然后重命名该临时文件。也可以使用--in-place 来代替-i:
```
[huawei@n148 sed]$ sed 's/John/Johnny/' employee.txt > new-employee.txt
[huawei@n148 sed]$ mv new-employee.txt employee.txt
```
在 sed 命令中使用-i 选项，使 sed 可以直接修改输入文件
```
[huawei@n148 sed]$ sed -i 's/John/Johnny/' employee.txt
```
在替换前备份employee.txt为employee.txtbak，然后再改employee.txt。下面的命令是等价的
```
[huawei@n148 sed]$ sed -ibak 's/John/Johnny/' employee.txt
[huawei@n148 sed]$ sed --in-place=bak 's/John/Johnny/' employee.txt
```
## 保持文件所有者不变，选项 -c
此未测试
该选项应和-i 配合使用。使用-i 时，通常在命令执行完成后，sed 使用临时文件来保持更改
后的内容，然后把该临时文件重命名为输入文件。但这样会改变文件的所有者(奇怪的是我
的测试结果是它不会改变文件所有者)，配合 c 选项，可以保持文件所有者不变。也可以使
用--copy 来代替。

```
下面的命令是等价的:
sed -ibak -c ‘s/John/Johnny/’ employee.txt
sed --in-place=bak --copy ‘s/John/Johnny/’ employee.txt
```
## 指定行的长度，选项 -l
与命令l的效果类似
```
[huawei@n148 sed]$ sed -n 'l 20' employee.txt
101,Johnnyny Doe,CE\
O$
102,Jason Smith,IT \
Manager$
103,Raj Reddy,Sysad\
min$
104,Anand Ram,Devel\
oper$
105,Jane Miller,Sal\
es Manager$
[huawei@n148 sed]$ sed -n -l 20 'l' employee.txt
101,Johnnyny Doe,CE\
O$
102,Jason Smith,IT \
Manager$
103,Raj Reddy,Sysad\
min$
104,Anand Ram,Devel\
oper$
105,Jane Miller,Sal\
es Manager$

```
## 打印模式后读入新行覆盖到模式，继续执行后续命令 n
执行n打印完模式，读取一行新内容覆盖模式空间后，继续执行n后面的命令，如果有选项-n，则此命令不打印。如果n失败（没有新行了）则直接结束sed
```
与cat功能相同，执行顺序是：
1 新一轮：模式里是1，然后n（打印1，读入2），本轮结束，打印2，清除模式
2 新一轮：模式里是3，然后n（打印3，读入4），本轮结束，打印4，清除模式
3 新一轮：模式里是5，n失败，打印5，本轮结束清除模式
总共执行了3轮
[huawei@n148 sed]$ sed n employee.txt
101,Johnnyny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

打印奇数
pattern space先读入1，然后执行到n（打印模式空间的内容后，把下一行2读入pattern space中并覆盖原本的1），然后pattern space中的内容（2）被删除（d操作），接下来打印3后4被删除，所以打印出一系列奇数
[huawei@n148 sed]$ seq 5|sed 'n;d'
1
3
5
[huawei@n148 sed]$ seq 6|sed 'n;d'
1
3
5
[huawei@n148 sed]$ seq 7|sed 'n;d'
1
3
5
7

如果使用了-n 选项，将没有任何输出，参考选项-n说明
$ sed -n n employee.txt


先在每行下面追加空行，再删除掉所有空行
[huawei@n148 ~]$ seq 5|sed 'G'| sed 'n;d'
1
2
3
4
5
```
## 读入新行附加到模式（两行看做一行），继续执行后续命令 N
用shell来比喻的话  
* N是:  echo 下一行内容>>模式空间
* n是:  echo 下一行内容>模式空间

多行模式空间：
* 模式空间最初的内容和新的输入行之间用换行符分隔。
* 在模式空间中嵌入的换行符可以利用“\n”来匹配。
* 在多行模式空间中，元字符“^”匹配空间中的第一个字条，而不匹配换行符后面的字符。 
* “$”只匹配模式空间中最后的换行符，而不匹配任何嵌入的换行符

```
注释：读取1，$!条件满足（不是尾行），执行N命令，得出1\n2，执行P，打印得1后清理模式，读取3，$!条件满足（不是尾行），执行N命令，得出3\n4，执行P，打印得3后清理模式，读取5，$!条件不满足，跳过N，执行P，打印得5
[huawei@n148 ~]$ seq 5|sed -n '$!N;P'
1
3
5


pattern space先读入1，然后执行到N，把下一行添加到当前的pattern space中，pattern space内容为（1\n2），然后执行d操作被删除。接下去读入3（系统读入总是覆盖原有内容），执行N，pattern space 内容变为（3\n4），然后再被删除。如果行数是奇数则会最后打印出一行
[huawei@n148 sed]$ seq 5|sed 'N;d'
5
[huawei@n148 sed]$ seq 6|sed 'N;d'
[huawei@n148 sed]$ seq 7|sed 'N;d'
7


[huawei@n148 sed]$ echo -e "1\n2\n3\n4" | sed -n 'N;s/\n/ /;p'
1 2
3 4
sed先读入第一行到pattern space，然后执行N命令，将第二行追加进pattern space，这时pattern space里面就是1\n2，然后执行s/\n/ /，将换行符替换成空格，最后打印。


[huawei@n148 sed]$ echo -e "1\n2\n3\n4" | sed -n 'n;s/\n/ /;p'
2
4
sed先读入第一行到pattern space，然后执行n命令，用第二行覆盖pattern space，这时pattern space里面就是2，然后执行s/\n/ /，因为pattern space里没有\n，所以不做任何替换，直接打印


```
## 关闭自动打印模式空间内容，选项 -n
选项-n与命令n一起使用时候，最终不会打印模式内容，貌似使用命令n是为了读取下一行覆盖到模式空间里（详解命令n）
```
两个不输出任何信息
[huawei@n148 sed]$ seq 7|sed -n 'n'
[huawei@n148 sed]$ seq 7|sed -n 'N'


参数p指打印pattern space的内容
[huawei@n148 sed]$ seq 7|sed -n 'n;p'
2
4
6

[huawei@n148 sed]$ seq 7|sed -n 'N;p'
1
2
3
4
5
6


[huawei@n148 sed]$ sed 'p' employee.txt
101,Johnny Doe,CEO
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
105,Jane Miller,Sales Manager

[huawei@n148 sed]$ sed -n 'p' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


------------------------------
下面的几种方法理解的还有问题。。。

打印奇数行，即打印一行跳过一行
[huawei@n148 sed]$ sed -n 'p;n' employee.txt
101,John Doe,CEO
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager

打印一行跳过2行
[huawei@n148 sed]$ sed -n 'p;n;n' employee.txt
101,John Doe,CEO
104,Anand Ram,Developer

打印偶数行，即跳过一行打印一行
[huawei@n148 sed]$ sed -n 'n;p' employee.txt
102,Jason Smith,IT Manager
104,Anand Ram,Developer

跳过一行打印一行2遍，这是错的。。。
[huawei@n148 sed]$ sed -n 'n;p;p' employee.txt
102,Jason Smith,IT Manager
102,Jason Smith,IT Manager
104,Anand Ram,Developer
104,Anand Ram,Developer

```
## 分组替换(单个分组)
跟在正则表达式中一样，sed 中也可以使用分组。分组以\(开始，以\)结束。分组可以用在
回溯引用中。回溯引用即重新使用分组所选择的部分正则表达式，在 sed 替换命令的 replacement-string中和正则表达式中，都可以使用回溯引用。

```
找出100以内，个位与十位相同的2位数
[huawei@n148 sed]$ seq 100 | sed -n '/^\(.\)\1$/p'
11
22
33
44
55
66
77
88
99
正则“^\(.\)\1$”的首尾^$代表是一个完整行，\1表示前面\(.\)中内容，换种说法就是\1与前面\(.\)是相同的，那么就是个位与十位相同了


下例中正则表达式\([^,]*\)匹配字符串从开头到第一个逗号之间的所有字符(并将其放入第一个分组中)，replacement-string 中的\1 将替代匹配到的分组，g 即是全局标志

[huawei@n148 sed]$ sed 's/\([^,]*\).*/\1/g' employee.txt
101
102
103
104
105



显示/etc/passwd的第一列，即用户名
[^:]*能够匹配到非:开头的任意长度的到：结尾的作为分组1，（）外的.*匹配：后面的内容，没有用到这部分
[huawei@n148 sed]$ sed 's/\([^:]*\).*/\1/' /etc/passwd
root
bin
daemon
...


如果单词第一个字符为大写，那么会给这个大写字符加上()
[huawei@n148 sed]$ echo "The Geek Stuff"|sed 's/\(\b[A-Z]\)/\(\1\)/g'
(T)he (G)eek (S)tuff


格式化数字，增加其可读性:
[huawei@n148 sed]$ cat numbers.txt
1
12
123
1234
12345
123456
[huawei@n148 sed]$ sed 's/\(^\|[^0-9.]\)\([0-9]\+\)\([0-9]\{3\}\)/\1\2,\3/g' numbers.txt
1
12
123
1,234
12,345
123,456
```


## 分组替换(多个分组)
可以使用多个\(和\)划分多个分组，使用多个分组时，需要在 replacement-string 中使用\n来指定第 n 个分组，注意：sed 最多能处理 9 个分组，分别用\1 至\9 表示。
```

只打印第一列(雇员 ID)和第三列(雇员职位):
[huawei@n148 sed]$ sed 's/^\([^,]*\),\([^,]*\),\([^,]*\)/\1,\3/' employee.txt
101,CEO
102,IT Manager
103,Sysadmin
104,Developer
105,Sales Manager

上面例子original-string 中，划分了 3 个分组，以逗号分隔。
([^,]*\) 第一个分组，匹配雇员 ID
，为字段分隔符
([^,]*\) 第二个分组，匹配雇员姓名
，为字段分隔符
([^,]*\) 第三个分组，匹配雇员职位
，为字段分隔符，上面的例子演示了如何使用分组
\1 代表第一个分组(雇员 ID)
，出现在第一个分组之后的逗号
\3 代表第二个分组(雇员职位)
============================================================


交换第一列(雇员 ID)和第二列(雇员姓名)：
[huawei@n148 sed]$ sed 's/^\([^,]*\),\([^,]*\),\([^,]*\)/\2,\1,\3/' employee.txt
Johnnyny Doe,101,CEO
Jason Smith,102,IT Manager
Raj Reddy,103,Sysadmin
Anand Ram,104,Developer
Jane Miller,105,Sales Manager
============================================================

[huawei@n148 sed]$ echo aabbccddeeffgghh|sed 's/^\(..\)\(..\)\(..\)\(..\).*$/\1:\2:\3:\4/'
aa:bb:cc:dd

其中s/是替换命令，^表示从一行的开头匹配  
第一个\(..\)表示匹配任意2个字符，并且后面的\1，就是这次匹配的结果。
对于字符串aabbccddeeffgghh而言，就是aa这2个字符
同理，第二\(..\)匹配bb，对应\2
第三\(..\)匹配cc，对应\3
第四\(..\)匹配dd，对应\4
剩下的eeffgghh匹配 .*$，其中.*表示匹配任意个字符，$匹配到末尾，这些字符串被抛弃
aabbccddeeffgghh得到的结果就是aa:bb:cc:dd


============================================================

正则表达式[^,]*代表开头到第一个逗号之间的所有字符

[huawei@n148 ~]$ echo 101,John Doe,CEO|sed 's/[^,]*/@@@/'
@@@,John Doe,CEO

============================================================

最牛逼分组替换例子

[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ cat mycommands.sed
#按,分割  交换第一列和第二列
s/\([^,]*\),\([^,]*\),\(.*\).*/\2,\1,\3/g
#把整行内容放入<>中
s/^.*/<&>/
#把 Developer 替换为 IT Manager
s/Developer/IT Manager/
#把 Manager 替换为 Director
s/Manager/Director/
[huawei@n148 sed]$ sed -f mycommands.sed employee.txt
<John Doe,101,CEO>
<Jason Smith,102,IT Director>
<Raj Reddy,103,Sysadmin>
<Anand Ram,104,IT Director>
<Jane Miller,105,Sales Director>

```
## 被匹配的内容 &

```
当在 replacement-string 中使用&时，它会被替换成匹配到的 original-string 或正则表达式，这是个很有用的东西。

[huawei@n148 sed]$ echo hello| sed 's/hello/(&)/'
(hello)
[huawei@n148 sed]$ echo hello| sed 's/[a-z]*/(&)/'
(hello)
[huawei@n148 sed]$ echo "hello world"| sed 's/[a-z]*/(&)/'
(hello) world
[huawei@n148 sed]$ echo "hello world"| sed 's/[a-z]*/(&)/g'
(hello) (world)
[huawei@n148 sed]$ echo hello| sed 's/[a-z]*/(&) world/g'
(hello) world


给雇员 ID(即第一列的 3 个数字)加上[ ],如 101 改成[101]
[huawei@n148 sed]$ sed 's/^[0-9][0-9][0-9]/[&]/g' employee.txt
[101],Johnnyny Doe,CEO
[102],Jason Smith,IT Manager
[103],Raj Reddy,Sysadmin
[104],Anand Ram,Developer
[105],Jane Miller,Sales Manager

把每一行放进< >中：
[huawei@n148 sed]$ sed 's/^.*/<&>/' employee.txt
<101,Johnnyny Doe,CEO>
<102,Jason Smith,IT Manager>
<103,Raj Reddy,Sysadmin>
<104,Anand Ram,Developer>
<105,Jane Miller,Sales Manager>

```

## 直接执行sed文件
```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

[huawei@n148 sed]$ cat myscript.sed
#!/bin/sed -f
#交换第一列和第二列
s/\([^,]*\),\([^,]*\),\(.*\).*/\2,\1, \3/g
#把整行内容放入<>中
s/^.*/<&>/
#把 Developer 替换为 IT Manager
s/Developer/IT Manager/
#把 Manager 替换为 Director
s/Manager/Director/

给这个脚本加上可执行权限,然后直接在命令行调用它
[huawei@n148 sed]$ chmod u+x myscript.sed
[huawei@n148 sed]$ ./myscript.sed employee.txt
<John Doe,101, CEO>
<Jason Smith,102, IT Director>
<Raj Reddy,103, Sysadmin>
<Anand Ram,104, IT Director>
<Jane Miller,105, Sales Director>

==============================================
[huawei@n148 sed]$ cat testscript.sed
#!/bin/sed -nf
/root/ p
/nobody/ p
/mail/ p
[huawei@n148 sed]$ chmod u+x testscript.sed
[huawei@n148 sed]$ ./testscript.sed /etc/passwd
root:x:0:0:root:/root:/bin/bash
mail:x:8:12:mail:/var/spool/mail:/sbin/nologin
operator:x:11:0:operator:/root:/sbin/nologin
nobody:x:99:99:Nobody:/:/sbin/nologin
nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin
```

## sed模拟cat
```
[huawei@n148 sed]$ cat employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed 'N' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed 'n' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n 'p' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed 's/JUNK/&/ p' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager
```
## sed模拟paste
匹配的样式单一。如果折行大于2则要改模式
```
[huawei@n148 sed]$ sed '$!N;s/\n/ /'  employee.txt
101,John Doe,CEO 102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin 104,Anand Ram,Developer
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed 'N;s/\n/ /'  employee.txt
101,John Doe,CEO 102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin 104,Anand Ram,Developer
105,Jane Miller,Sales Manager

```
## sed模拟rev  包含GsD&分组;
反转一行中每个字符的顺序
```
1 执行/\n/!G，即匹配到\n就不执行G，模式中是123\n（\n是可能是echo默认带有的）
2 然后s/\(.\)\(.*\n\)/&\2\1/后模式中是123\n23\n1 ，即用\(.\)匹配到1并放入1组，用\(.*\n\)匹配到23\n放到2组，此时\2的内容为23\n，\1的内容为1，&则代表原始内容123\n。最终即123\n被替换成了123\n23\n1
3 然后\\D,其实就是D，删除了123\n这行，模式中剩余23\n1，因为还有内容所以从头开始重新会步骤1（命令D带有循环效果）
4 此时23\n1带有\n不执行G
5 再s替换，用\(.\)匹配到2并放入1组，用\(.*\n\)匹配到3\n放到2组，此时\2的内容为3\n，\1的内容为2，&则代表原始内容23\n(注意没有1)。最终即23\n1被替换成了23\n3\n21
6 再D，变为3\n21，循环G不执行
7 再s替换，用\(.\)匹配到3并放入1组，用\(.*\n\)匹配到\n放到2组，此时\2的内容为\n，\1的内容为3，&则代表原始内容3\n。最终即3\n21被替换成了3\n\n321
8 再D，变为\n321，循环G不执行。模式里第一行是空所以s与D也不执行了，退出了循环
9 执行最后的s/.// 把第一个字符换成空，即去掉了\n

[huawei@n148 sed]$ echo "123"|sed '/\n/!G;s/\(.\)\(.*\n\)/&\2\1/;//D;s/.//'
321


[huawei@n148 sed]$ cat employee.txt|sed '/\n/!G;s/\(.\)\(.*\n\)/&\2\1/;//D;s/.//'
OEC,eoD nhoJ,101
reganaM TI,htimS nosaJ,201
nimdasyS,yddeR jaR,301
repoleveD,maR dnanA,401
reganaM selaS,relliM enaJ,501
```
## sed模拟grep
```
[huawei@n148 sed]$ grep Jane employee.txt
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n 's/Jane/&/ p' employee.txt
105,Jane Miller,Sales Manager
[huawei@n148 sed]$ sed -n '/Jane/ p' employee.txt
105,Jane Miller,Sales Manager



grep –v (打印不匹配的行):
[huawei@n148 sed]$ grep -v Jane employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer

[huawei@n148 sed]$ sed -n '/Jane/ !p' employee.txt
101,Johnny Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
```
## sed模拟head
实验命令q退出来实现打印前n行
```
[huawei@n148 sed]$ sed q /etc/passwd
[huawei@n148 sed]$ head -10 /etc/passwd
[huawei@n148 sed]$ sed '11,$ d' /etc/passwd
[huawei@n148 sed]$ sed -n '1,10 p' /etc/passwd
[huawei@n148 sed]$ sed '10 q' /etc/passwd
[huawei@n148 sed]$ sed 10q /etc/passwd
```
## sed模拟tail
```
显示文件中的最后一行（模拟“tail -1”）
$!d:非最后一行都删掉了
[huawei@n148 sed]$ sed '$!d'  employee.txt
105,Jane Miller,Sales Manager
最后一行打印，其余不打印
[huawei@n148 sed]$  sed -n '$p' employee.txt
105,Jane Miller,Sales Manager


显示文件中的最后2行（模拟“tail -2”命令）
1 显示101，然后$!N成立，把102读入模式，此时是101\n102，
2 然后$!D，因为还有103所以成立，然后D把模式里的101\n删了
3 循环直到模拟里只有104，然后执行$!N成立把105读入，变为104\n105
4 然后$!D失败，因为到文件尾了，所以也不执行D，最终大印出104\n105，即末尾两行
[huawei@n148 sed]$ sed '$!N;$!D' employee.txt
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


[huawei@n148 sed]$ seq 5 | sed 'N;$!D'
4
5


打印尾5行
[huawei@n148 sed]$ seq 200 |sed -e :a -e '$q;N;6,$D;ba;'
196
197
198
199
200
1 先看“-e :a -e '$q;N;ba'”这个循环不停的读入下一行直到结尾，这样整个文本就形成一个由\n分割的链
2 现在加上“6,$D”执行得到最终结果，即如果当前读的行是在第六行之后，则执行D，会去除模式空间的首行。最终只保留了末尾5行

[huawei@n148 sed]$ seq 200 | sed ':a; N; 1,5ba; D'
196
197
198
199
200
1 执行“:a; N; 1,5ba”将文件的前6行读入模式空间。首先N后模式是前2行，然后“1,5ba”,2在1~5之间所以循环回到“a” ，退出循环时候模式里是1~6
2 执行D，1被去除（此时模式里是5行）
3 下一轮开始N，加入7，执行D（还是5行）
4 直到最后5行结束
```
## sed模拟uniq 去重
所有下面的去重操作默认需要先sort，将相同的行凑在一起。否则如这样的原始内容就会有缺陷
```
[huawei@n148 sed]$ cat data.txt
sex
f
f
m
m
m
f
f
```
```
删除文件中重复的连续的行,重复行中第一行保留，其他删除
[huawei@n148 sed]$ cat data.txt
sex
f
f
m
m
m
[huawei@n148 sed]$ sed '$!N; /^\(.*\)\n\1$/!P; D' data.txt
sex
f
m
1 首先$!N非最后一行就N再读一行附加到模式后（模式里当前有2行），没有匹配到‘^\(.*\)\n\1$’则P进行打印模式内的第一行，若匹配到了则不会P，最后D删除模式内的第一行，接下来看此正则
2 ‘^\(.*\)\n\1$’首先^\(.*\)能够取到\n前整行内容，分组\1$代表这个整行且结尾处无多余内容，这段能够匹配到abc\nabc类似这样的内容，即匹配两行内容一致

-------------------------------------------------

删除除重复行外的所有行（模拟“uniq -d”）
[huawei@n148 sed]$ sed '$!N; s/^\(.*\)\n\1$/\1/; t; D' data.txt
f
m
1 $!N同上，^\(.*\)\n\1$也同上，则s的作用就是匹配到连续2行一样的内容则替换为一行（即删除了一行）
2 s匹配成功则t开启下一轮（默认会打印出模式内容），否则D去除模式中的第一行
```
## 显示文件的倒数第2行
```
1 如果不是最后一行则执行hd，h将模式内容覆盖到保持，d则清理模式，这部分的目的就是如果不是最后一行就把内容备份到保持中
2 如果是最后一行则不进行hd，然后x交换模式与保持，交换后模式里是上一轮的行备份，保持则为最后一行的内容，并最后打印出了模式里的内容
3 $!{h;d;}的语法见多套命令

[huawei@n148 sed]$ seq 5|sed -e '$!{h;d;}' -e x
4

后面两种未分析
sed -e '$!{h;d;}' -e x              # 当文件中只有一行时，输入空行
sed -e '1{$q;}' -e '$!{h;d;}' -e x  # 当文件中只有一行时，显示该行
sed -e '1{$d;}' -e '$!{h;d;}' -e x  # 当文件中只有一行时，不输出
```
## 模式空间与保持空间
模式空间是内部维护的一个缓存，存放着读入的一行或者多行内容，处理完后会自动清空，无法保存模式空间中被处理的行，因此sed又引入了另外一个缓存空间-保持空间用于保存模式空间的内容，模式空间的内容可以复制到保持空间，同样地保持空间的内容可以复制回模式空间。sed提供了几组命令用来完成复制的工作，其它命令无法匹配也不能修改模式空间的内容。
## 模式覆盖 h、追加 H 到保持
保存（Hold) h/H：将模式空间的内容覆盖或者追加到保持空间
```
输出23451

[huawei@n148 ~]$ seq 10|sed -n '1{h;n};5{x;H;x};1,5p'
2
3
4
5
1
[huawei@n148 ~]$ seq 10|sed -n -e '
>  1{h;n} # 保存第 1 行的内容到保持缓冲区并继续
>  5{     # 在第 5 行
>    x    # 交换模式和保持空间
>         # (现在模式空间包含了第 1 行)
>    H    # 在保持空间的第 5 行后追加第 1 行
>    x    # 再次交换第 5 行和第 1 行，第 5 行回到模式空间
>  }
>  1,5p   # 输出第 2 行到第 5 行
>         # (没有输错！尝试去找到为什么这个规则
>         # 不在第 1 行上运行 ;)
> '
2
3
4
5
1
理解：1会覆盖到保持后n读入2，但有-n所以不打印，2属于1,5p最打印出2，循环打印出34，第5时候先x交换出1，此时模式1，保持是5，然后H将1追加到保持里，此时保持是5\n1，然后x交换到模式里，属于1，5然后p打印模式所有
```
## 保持覆盖 g、追加 G 到模式
取回（Get）g/G：将保持空间的内容覆盖或者追加到模式空间
## 交换模式与保持 x
交换（Exchange）x：交换模式空间和保持空间的内容
交换命令比较容易理解，保存命令和取回命令都有大写和小写两种形式，这两种形式的区别是小写的是将会覆盖目的空间的内容，而大写的是将内容追加到目的空间，追加的内容和原有的内容是以\n分隔。
```
输出23451

[huawei@n148 ~]$ seq 10|sed -n -e '
>  1{x;n} # 交换保持和模式空间
>         # 保存第 1 行到保持空间中
>         # 然后读取第 2 行
>  5{
>    p    # 输出第 5 行
>    x    # 交换保持和模式空间
>         # 去取得第 1 行的内容放回到模式空间
>  }
>  1,5p   # 输出第 2 到第 5 行
> '
2
3
4
5
1
[huawei@n148 ~]$ seq 10|sed -n '1{x;n};5{p;x};1,5p'
2
3
4
5
1
理解：1会x存入保持后n读入2，但有-n所以不打印，2属于1,5p最打印出2，循环打印出34，第5时候先p打出5，后x交换出1，5在1，5行p打印1
```
## 使用h、H、G、g、x
```
[huawei@n148 sed]$ cat test.txt
1
2
11
22
111
222
==================================================

返回的结果正常，因为复制到保持空间的内容并没有取回

[huawei@n148 sed]$ sed 'h' test.txt
1
2
11
22
111
222
==================================================

1!G：1就是选定第一行，"!"表示后边的命令对所有没有被选定的行发生作用。即第1行不执行“G”命令，从第2行开始执行。也就是说除了第一行,后边每行都加了空行,这是因为内存缓冲区中默认值是空行
[huawei@n148 sed]$ seq 5|sed '1!G'
1
2

3

4

5

==================================================
1 数字1不用执行G, h将模式的1覆盖到保持里，打印出模式里的1后模式清空
2 数字2时候先执行G，将保持的1追加到模式的2后，此时模式里是2\n1,执行h将2/n1覆盖到保持里，然后打印模式的2\n1后清理模式
3 后面的以此类推
[huawei@n148 sed]$ seq 5|sed '1!G;h'
1
2
1
3
2
1
4
3
2
1
5
4
3
2
1
==================================================

每一行的后面都多了一个空行，原因是每行都会从保持空间取回一行，追加（大写的G）到模式空间的内容之后，以\n分隔。

[huawei@n148 sed]$ sed 'G' test.txt
1

2

11

22

111

222

==================================================

 命令执行后，发现前面多了一个空行并且最后一行不见了。我在前面一直强调sed命令用好，要有用大脑回顾命令执行过程的能力：
* 当读入第一行的时候，模式空间中的内容是第一行的内容，而保持空间是空的，这个时候交换两个空间，导致模式空间为空，保持空间为第一行的内容，因此输出为空行；
* 当读入下一行之后，模式空间为第2行的内容，保持空间为第一行的内容，交换后输出第1行的内容；
* 依次读入每一行，输出上一行的内容；
* 直到最后一行被读入到模式空间，交换后输出倒数第二行的内容，而最后一行的内容并没有输出，此时命令执行结束。

[huawei@n148 sed]$ sed 'x' test.txt

1
2
11
22
111
==================================================
```
### 插入空行
在当前位置后加一行保留空间中的内容，无任何动作时，保留空间为空行
```
在每一行后面增加一空行
[huawei@n148 sed]$ seq 5|sed G
1

2

3

4

5

在每一行后面增加两行空行
[huawei@n148 sed]$ sed 'G;G' employee.txt
101,John Doe,CEO


102,Jason Smith,IT Manager


103,Raj Reddy,Sysadmin


104,Anand Ram,Developer


105,Jane Miller,Sales Manager


将原来的所有空行删除并在每一行后面增加一空行。这样在输出的文本中每一行后面将有且只有一空行。
[huawei@n148 sed]$ sed '/^$/d;G' employee.txt
101,John Doe,CEO

102,Jason Smith,IT Manager

103,Raj Reddy,Sysadmin

104,Anand Ram,Developer

105,Jane Miller,Sales Manager


在每个含有字符串3的行上插入一行空白行 
0 这里默认会打印每一行的内容，因为前面没有-n
1 保留空间中开始为空行，模式空间中经过sed  '/3/'查询后为包含3内容的那一行
2 第一个x，交换模式空间和保留空间的内容，此时模式空间中内容为空行，保留空间中内容为含有3内容的行
3 命令p打印模式空间内容（空行）
4 第二个x，交换模式空间和保留空间中的内容
[huawei@n148 sed]$ sed '/3/{x;p;x;}' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager

103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


在每个含有字符串3的行下插入一行空白行
[huawei@n148 sed]$ sed '/3/G' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin

104,Anand Ram,Developer
105,Jane Miller,Sales Manager




每个含有字符串3的行上，下各插入一行空白行
[huawei@n148 sed]$ sed '/3/{x;p;x;G}' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager

103,Raj Reddy,Sysadmin

104,Anand Ram,Developer
105,Jane Miller,Sales Manager

在每3行后增加一空白行
[huawei@n148 sed]$  sed 'n;n;G;'  myscript.sed
#!/bin/sed -f
#▒▒▒▒▒▒һ▒к͵ڶ▒▒▒
s/\([^,]*\),\([^,]*\),\(.*\).*/\2,\1, \3/g

#▒▒▒▒▒▒▒▒▒ݷ▒▒▒<>▒▒
s/^.*/<&>/
#▒▒ Developer ▒滻Ϊ IT Manager

s/Developer/IT Manager/
#▒▒ Manager ▒滻Ϊ Director
s/Manager/Director/

```
### 逆序输出
用sed模拟出tac的功能（倒序输出）
```
$!d：最后一行不删除（保留最后1行）。
[huawei@n148 sed]$ seq 10|sed '1!G;h;$!d'
10
9
8
7
6
5
4
3
2
1
[huawei@n148 sed]$ seq 10|sed -n '1!G;h;$p'
10
9
8
7
6
5
4
3
2
1
```

### join效果
```
使用H将每一行都追加到保持空间
这里利用d命令打断常规的命令执行流程，让sed继续读入新的一行，直接到将最后一行都放到保持空间。
这个时候使用x命令将保持空间的内容交换到模式空间，模式空间的内容现在是这样的：\n1\n11\n2\n11\n22\n111\n222。
替换的步骤分成两个，首先去掉首个回车符，然后把剩余的回车符替换成逗号。

[huawei@n148 sed]$ sed 'H;$!d;${x;s/^\n//;s/\n/,/g}' test.txt
1,2,11,22,111,222
```
### 将语句中的特定单词转换成大写，牛！
精准转换Match为大写
```
[huawei@n148 sed]$ cat find.txt
ABCDEFGHIJKLMNOPQRSTUVWXYZ
find the Match statement
bcdefghijklmnopqrstuvwxyz

不可y命令，会把模式空间的所有内容都转换，不满足需求
[huawei@n148 sed]$ sed 'y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/' find.txt
ABCDEFGHIJKLMNOPQRSTUVWXYZ
FIND THE MATCH STATEMENT
BCDEFGHIJKLMNOPQRSTUVWXYZ

================================================
详细步骤说明看下面。。。
[huawei@n148 sed]$ sed '/the .* statement/{
h
s/.*the \(.*\) statement.*/\1/
y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/
G
s/\(.*\)\n\(.*the \).*\( statement.*\)/\2\1\3/
}'  find.txt
ABCDEFGHIJKLMNOPQRSTUVWXYZ
find the MATCH statement
bcdefghijklmnopqrstuvwxyz
-------------------------
1 
首先“/the .* statement/”找到需要处理的行（假设当前行为find the Match statement）。

Pattern space: find the Match statement
-------------------------
2 
h：将当前行保存到保持空间：

Pattern space: find the Match statement
Hold space: find the Match statement
-------------------------
3
“s/.*the \(.*\) statement.*/\1/”：  用替换命令获取需要处理的单词：

Pattern space: Match
Hold space: find the Match statement
-------------------------
4
“y/abcdefghijklmnopqrstuvwxyz/ABCDEFGHIJKLMNOPQRSTUVWXYZ/”：转换成大写

Pattern space: MATCH
Hold space: find the Match statement
-------------------------
5
G：将保持空间的内容追加到模式空间最后

Pattern space: MATCH\nfind the Match statement
Hold space: find the Match statement
-------------------------
6
“s/\(.*\)\n\(.*the \).*\( statement.*\)/\2\1\3/”：再进行替换

Pattern space: find the MATCH statement
Hold space: find the Match statement
-------------------------
```
### 行列转化
```
-n ：取消默认的输出
H表示模式空间的内容追加到保持空间
${...} 表示最后执行，意思是最后才执行{ }里面的内容，所以最后的时候保持空间里面的内容和cat的内容一致
x 表示交换保持空间和模式空间的内容，那么此时模式空间里的内容就是cat的内容了，此时再使用 "s/\n/ /g" 替换命令，将换行符\n,替换成空格，这样列就变成行了，反之亦然

[huawei@n148 sed]$ cat foo
1
2
3
4
5
[huawei@n148 sed]$ sed -n 'H;${x;s/\n/ /g;p}' foo
 1 2 3 4 5

另一个版本
[huawei@n148 sed]$ cat test.txt
11
22
33
44
[huawei@n148 sed]$ sed ':a;N;s/\n/ /;ta' test.txt
11 22 33 44
[huawei@n148 sed]$ sed ':b;N;s/\n/ /;tb' test.txt
11 22 33 44
```

### 累加求和
```
H表示模式空间的内容追加到保持空间
${x;s/\n/+/g;s/^+//;p} : 最后执行(因为${} )；交换保持空间和模式空间的内容;
“s/\n/+/g”   将\n替换成+
“s/^+//”    将开头的加号换为空
最后使用bc计算器就可以求和了

[huawei@n148 sed]$ seq 100|sed -n 'H;${x;s/\n/+/g;s/^+//;p}' | bc
5050
```

## 跳转与标签 b t T :
* 命令b后面有标签则跳到标签，后面没有标签则直接下一轮
* 命令t则是s///替换成功才跳转，t后有标签则跳到标签，没有标签则下一轮
* 命令T暂时未理解  
If no s/// has done a successful  substitution  since  the  last input  line  was  read  and  since the last t or T command, then branch to label; if label is omitted, branch to end of script.
* ：冒号则代表的标签，后面跟着的字符串是标签名称
```
这是不循环直接b跳到下一轮的案例。分别匹配3个k，匹配到就直接b下一轮，否则d清理模式后下一轮
[huawei@n148 sed]$ cat ll.txt
1AAA23
4567
890

AbcdBBBefg
qwe
qwe

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123
[huawei@n148 sed]$ sed -e '/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d ll.txt
1AAA23
AbcdBBBefg
CCCa
AAAa
BBBa
AAAa
CCCa
123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123

---------------------------------------

执行“/3/ba”，如果匹配了3则跳转到标签:a，接着执行后面的s/$/ YES/，即在行末插入YES，否则就不跳转接着执行s/$/ NO/, 在行末插入NO。然后执行b直接结束

[huawei@n148 sed]$ sed '/3/ba;s/$/ NO/;b;:a;s/$/ YES/' employee.txt
101,John Doe,CEO NO
102,Jason Smith,IT Manager NO
103,Raj Reddy,Sysadmin YES
104,Anand Ram,Developer NO
105,Jane Miller,Sales Manager NO

使用t跳转的版本。
执行s/$/ YES/，s命令执行成功，执行t命令，没有标签，跳转到命令的结尾，这样将会跳过后面的s/$/ NO/。如果不匹配，s/$/ YES/不执行，则t命令也不执行，只执行后面的s/$/ NO/
[huawei@n148 sed]$ sed '/^103/s/$/ YES/;t;s/$/ NO/' employee.txt
101,John Doe,CEO NO
102,Jason Smith,IT Manager NO
103,Raj Reddy,Sysadmin YES
104,Anand Ram,Developer NO
105,Jane Miller,Sales Manager NO


加！可以起到反效果，即匹配则不跳转。然并卵。。。
[huawei@n148 sed]$ sed '/3/!ba;s/$/ NO/;b;:a;s/$/ YES/' employee.txt
101,John Doe,CEO YES
102,Jason Smith,IT Manager YES
103,Raj Reddy,Sysadmin NO
104,Anand Ram,Developer YES
105,Jane Miller,Sales Manager YES

```
### 右对齐
```
按照10列宽右对齐，先看成品，分析看下面
[huawei@n148 sed]$ echo "abcde"| sed -e :a -e 's/^.\{1,9\}$/ &/;ta'
     abcde

向右挪一个空格，“^.\{1,9\}$”能够匹配从行首开始的连续最多9个字符。本句匹配到了前5个abcde，替换为：先空一个格后接被匹配的字符串（即“ &”），其实想向右挪几个空格就在&前加几个就行，继续往下看
[huawei@n148 sed]$ echo "abcde"| sed 's/^.\{1,9\}$/ &/'
 abcde

s命令成功替换后继续执行ta，跳转到标签a继续循环，直到向后挪的s操作失败。下面在s后加了p打印模式空间内容。
1 “abcde”-》“ abcde”
2 “ abcde”-》“  abcde”
3 以此类推到前面5个空格时候“     abcde”，不符合最多9个字符了，则s失败，但还是会多打印一行“     abcde”，t结束循环，此行sed完毕
[huawei@n148 sed]$ echo "abcde"| sed -e :a -e 's/^.\{1,9\}$/ &/p; ta'
 abcde
  abcde
   abcde
    abcde
     abcde
     abcde

分析完毕，总结一句就是把内容替换成空格加自身
[huawei@n148 sed]$ echo "abcde"| sed -e :a -e 's/^.\{1,9\}$/ &/; ta'
     abcde

用于多行也可以，但要如果一行的字符总数超过40，则不会自动换行。
[huawei@n148 sed]$  sed -e :a -e 's/^.\{1,40\}$/ &/; ta'  employee.txt
                         101,John Doe,CEO
               102,Jason Smith,IT Manager
                   103,Raj Reddy,Sysadmin
                  104,Anand Ram,Developer
            105,Jane Miller,Sales Manager
```
### 居中
```
原理同上，只是两边都加了空格
[huawei@n148 sed]$ echo "abcde"| sed -e :a -e 's/^.\{1,9\}$/ & /;ta'
   abcde
```
### 合并行
* 标志在行尾  
如果当前行以反斜杠“\”结束，则将下一行并到当前行末尾，并去掉原来行尾的反斜杠
```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager\
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer\
105,Jane Miller,Sales Manager

1 匹配行尾有\的，并再读一行新的附近到模式（N），此时模式内为xxx\\nyyy
2 替换\\n为空
3 循环之
[huawei@n148 sed]$ sed -e :a -e '/\\$/N; s/\\\n//; ta' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager103,Raj Reddy,Sysadmin
104,Anand Ram,Developer105,Jane Miller,Sales Manager
```

* 标志在行首  
如果当前行以=开头，将当前行并到上一行末尾并以单个空格代替原来行头的“=”
```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
=103,Raj Reddy,Sysadmin
=104,Anand Ram,Developer
105,Jane Miller,Sales Manager


[huawei@n148 sed]$ sed -e :a -e '$!N;s/\n=/ /;ta' -e 'P;D' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager 103,Raj Reddy,Sysadmin 104,Anand Ram,Developer
105,Jane Miller,Sales Manager

分析（先去除了PD）：
最后一行不N其余行都N
1 配合循环替换\n=为空，则第一次打印的是101与102两行，因为有N，此时101、102行已经都完毕了
2 第二次打印的是103与104行，并且是将104前面的\n=替换完成了
3 第三次打印105，其实最终整个文件被执行了三次
[huawei@n148 sed]$ sed -e :a -e '$!N;s/\n=/ /;ta' employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
=103,Raj Reddy,Sysadmin 104,Anand Ram,Developer
105,Jane Miller,Sales Manager

加上PD，替换失败则PD（P打印模式第一行并D删除模式第一行，此时模式里剩下前面执行N，进来的新一行），则与最终结果一样了
1 第一次循环先打印第一行，删除第一行，模式中有102
2 再次执行则103被N进来（此时模式有102\n103，执行s成功再次N进来104，替换又成功，再次105，此时失败退出循环，P完后D模式里的第一行，此时打印除了102-103-104那行
3 最后打印105，也执行了三次，只不过与上面内容不同
```
### 千分号
```
[huawei@n148 sed]$ echo 123456789 | sed -e :a -e 's/\(.*[0-9]\)\([0-9]\{3\}\)/\1,\2/;ta'
123,456,789

1 分组1(.*[0-9])表示n个字符后跟一个数字，即匹配了123456，规则见sed贪婪匹配
2 分组2([0-9]{3})表示三个数字
3 s替换成功（加入，）则循环之，即从后向前3个字符加一个逗号
```
### 段落匹配
```
如果某段包含"AAA",则打印这一段。(空行用来分隔段落)
[huawei@n148 sed]$ cat ll.txt
1AAA23
4567
890

AbcdBBBefg
qwe
qwe

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123
[huawei@n148 sed]$ sed -e '/./{H;$!d;}' -e 'x;/AAA/!d;'  ll.txt

1AAA23
4567
890

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123
1 前边/./{H;$!d;}用保留一个段落。使用‘.’能够匹配有字符的行（无字符以为空行），然后H模式追加到保持中，$!d代表如果该行不是最后一行就删除模式空间的所有内容，接着读入下一个非空行（不会执行后面的x）
2 备份整个段落到保持中使用x;/AAA/!d来查找，先x交换保持与模式，此时模式里是一个段落，然后匹配到k则不清理保持空间（会打印出），否则d掉

--------------------------


如果某段包含"AAA"和"BBB"和"CCC",则打印这一段。“/./{H;$!d;}”同上，“/AAA/!d;/BBB/!d;/CCC/!d”参考模式匹配中的案例说明
[huawei@n148 sed]$ sed -e '/./{H;$!d;}' -e 'x;/AAA/!d;/BBB/!d;/CCC/!d'  ll.txt

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123
--------------------------


如果某段包含"AAA"或"BBB"或"CCC",则打印这一段，参考同上
[huawei@n148 sed]$ sed -e '/./{H;$!d;}' -e 'x;/AAA/b' -e '/BBB/b' -e '/CCC/b' -e d  ll.txt

1AAA23
4567
890

AbcdBBBefg
qwe
qwe

CCCa
AAAa
BBBa
AAAa
CCCa
1233

123AAA123BBB123CCC123
123CCC123BBB123AAA123
123BBB123AAA123CCC123

```
# AWK
## 语法
规则是先模式匹配后执行动作
```
awk [options] ‘Pattern{Action}’ file
```
* options（选项）：我们使用过-F选项，也使用过-v选项
* Action（动作）：我们使用过print与printf
* Pattern（模式）：如BEGIN、END，空模式、关系运算、正则、行范围
## 变量
* awk 变量可以直接使用而不需事先声明
* 如果要初始化变量，最好在BEGIN 区域内作，它只会执行一次。
* Awk 中没有数据类型的概念，一个 awk 变量是 number 还是 string 取决于该变量所处的上下文。
* 内置变量就是awk预定义好的、内置在awk内部的变量
* 自定义变量就是用户定义的变量。  
* 关于$
```
在awk中，只有在引用$0、$1等内置变量的值的时候才会用到”$”,引用其他变量时，不管是内置变量，还是自定义变量，都不使用”$”,而是直接使用变量名。
```
### 自定义变量
```

[huawei@n148 awk]$ awk -v v1="str1" -v v2="str2" 'BEGIN{print v1,v2}'
str1 str2
[huawei@n148 awk]$ awk 'BEGIN{v1="str1"; v2="str2";  print v1 v2}'
str1str2
[huawei@n148 awk]$ v3="str3"
[huawei@n148 awk]$ awk -v v1=$v3 'BEGIN{print v1}'
str3

```
### 未定义变量的使用
如果直接使用不存在的变量，awk会自动创建这个变量，并且默认为这个变量赋值为”空字符串”，数组元素也是如此
```
va不存在，连续打印了3次空
[huawei@n148 awk]$ seq 3|awk 'BEGIN{print "start"} {if (NR==2){next}; print va;} END{print "over"}'
start


over
```
未定义，但正常使用的方法
```
[huawei@n148 awk]$ seq 10|awk '$0>5{c++} END{print c}'
5

打印最大的 UID 和其所在的行
[huawei@n148 awk]$ awk -F ':' '$3 > maxuid { maxuid = $3; maxline = $0 } END { print maxuid,maxline }' /etc/passwd
65534 nfsnobody:x:65534:65534:Anonymous NFS User:/var/lib/nfs:/sbin/nologin

```
### 类型转换（字符串与数字）
打印未初始化的变量默认是空，参与数学运算则默认是0，字符串参与运算则默认为0
```
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print a}'
test
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print ++a}'
1
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print a++}'
0
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print b}'

[huawei@n148 ~]$ awk 'BEGIN{a="test"; print b++}'
0
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print ++b}'
1
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print c["t"]}'

[huawei@n148 ~]$ awk 'BEGIN{a="test"; print c["t"]++}'
0
[huawei@n148 ~]$ awk 'BEGIN{a="test"; print ++c["t"]}'
1
```




## 内置算术函数
以下的不常用，常用的都有单个的案例
* atan2(y,x) y/x 的反正切值, 定义域在 −π 到 π 之间
* cos(x) x 的余弦值, x 以弧度为单位
* exp(x) x 的指数函数, e^x
* log(x) x 的自然对数 (以 e 为底)
* sin(x) x 的正弦值, x 以弧度为单位.
* sqrt(x) x 的平方根
## 内置字符串函数
函数 描述
次数


* match(s,r) 测试 s 是否包含能被 r 匹配的子串, 返回子串的起始位置或 0; 设置 RSTART 与 RLENGTH
* sprintf(fmt,expr-list) 根据格式字符串 fmt 返回格式化后的 expr-list

## 运行分析器 pgawk
pgawk 程序用来生成 awk执行的结果报告。用 pgawk 可以看到 akw每次执行了多少条语句(以及用户自定义的函数)。

```
首先建立下面的 awk 脚本作为样本，以供 pgawk 执行，然后分析其结果。

[huawei@n148 awk]$ cat profiler.awk
BEGIN {
 FS=",";
 print "Report Generate On:",strftime("%a %b %d %H:%M:%S %Z %Y",systime());
}
{
if ( $5 <= 5 )
  print "Buy More: Order",$2,"immediately!"
else
  print "Sell More: Give discount on",$2,"immediately!"
}
END {
 print "----------"
}



接下来使用 pgawk(不是直接调用 awk)来执行该样本脚本。使用—profier 选项可以指定输出文件名。不指定“--profile=myprofiler.out”默认会创建输出文件 profier.out(或者 awkprof.out)，

[huawei@n148 awk]$ pgawk --profile=myprofiler.out -f profiler.awk items.txt
Report Generate On: Sun Nov 21 22:13:08 CST 2021
Sell More: Give discount on HD Camcorder immediately!
Buy More: Order Refrigerator immediately!
Sell More: Give discount on MP3 Player immediately!
Sell More: Give discount on Tennis Racket immediately!
Buy More: Order Laser Printer immediately!
----------


查看默认的输出文件 myprofiler.out来弄清楚每条单独的 awk 语句的执行次数。
[huawei@n148 awk]$ cat myprofiler.out
        # gawk profile, created Sun Nov 21 22:13:08 2021

        # BEGIN block(s)

        BEGIN {
     1          FS = ","
     1          print "Report Generate On:", strftime("%a %b %d %H:%M:%S %Z %Y", systime())
        }

        # Rule(s)

     5  {
     5          if ($5 <= 5) { # 2
     2                  print "Buy More: Order", $2, "immediately!"
     3          } else {
     3                  print "Sell More: Give discount on", $2, "immediately!"
                }
        }

        # END block(s)

        END {
     1          print "----------"
        }

查看 out 文件时
1 左侧一列有一个数字，标识着该 awk 语句执行的次数。如 BEGIN 区域里的 print 语句仅执行了一次。而 while 循环执行了 5 次。
2 对于任意一个条件判断语句，左边有一个数字，括号右边也有一个数字。左边数字代表该判断语句执行了多少次，右边数字代表判断语句为 true 的次数。上面的例子中，由(# 2)可以判定，if 语句执行了 5 次，但只有 2 次为 true。
```
## 整行内容 $0

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
## 最后一行
在 END 里 NR 的值会被保留下来，但是 $0 不会，需要通过用户变量存储。
```
[huawei@n148 awk]$ awk '{last=$0} END{print last}' emp.data
Susie 4.25 18
```
## 指定列 $1, $2...
默认输出是空格间隔，可以单独去设置
```
[huawei@n148 playground]$ awk '{print $2,$1}' emp.data
4.00 Beth
3.75 Dan
4.00 Kathy

[huawei@n148 sed]$ df|awk '{print $5}'
Use%
0%
1%
9%
0%
63%
24%
75%
1%
1%
0%
100%
```

## 拼文本、格式化输出
将自己的字段与文件中的列结合起来
```
[huawei@n148 awk]$ df|awk '{print "use:\t" $5}'
use:    Use%
use:    0%
use:    1%
use:    9%
use:    0%
use:    63%

引号不要加在变量上
[huawei@n148 awk]$ df|awk '{print "use:\t\"" $5 "\""}'
use:    "Use%"
use:    "0%"
use:    "1%"
use:    "9%"
use:    "0%"

[huawei@n148 playground]$ awk '{ print "total pay for", $1, "is", $2 * $3 }' emp.data
total pay for Mark is 100
total pay for Mary is 121
total pay for Susie is 76.5
```
## 列计算
```
[huawei@n148 playground]$ awk '{print $1, $2*$3}' emp.data
```
## 使用 printf
* printf 不会使用 OFS 和 ORS，它只根据”format”里面的格式打印数据。
* printf需要指定值的输出类型，print则更像是泛型
* 可以指定打印列的宽度（见例）

```
[huawei@n148 playground]$ awk '{ printf("total pay for %s is $%.2f\n", $1, $2 * $3) }' emp.data
total pay for Mark is $100.00
total pay for Mary is $121.00
total pay for Susie is $76.50

[huawei@n148 playground]$ awk '{ printf("%-8s $%6.2f\n", $1, $2 * $3) }' emp.data
Mark     $100.00
Mary     $121.00
Susie    $ 76.50


在左边补空格,把字符串”Good”打印成 6 个字符
[huawei@n148 awk]$ awk 'BEGIN { printf "%6s\n","Good" }'
  Good


指定宽度为 6，但仍然输出所有字符:
[huawei@n148 awk]$ awk 'BEGIN { printf "%6s\n", "Good Boy!" }'
Good Boy!

以绝对宽度打印字符串
[huawei@n148 awk]$ awk 'BEGIN { printf "%.6s\n", "Good Boy!" }'
Good B
[huawei@n148 awk]$ awk 'BEGIN { printf "%6s\n", substr("Good Boy!",1,6) }'
Good B


右对齐
[huawei@n148 awk]$ awk 'BEGIN { printf "|%6s|\n", "Good" }'
|  Good|

左对齐
[huawei@n148 awk]$ awk 'BEGIN { printf "|%-6s|\n", "Good" }'
|Good  |


```
## 复合语句 { }  { }
```
[huawei@n148 awk]$ cat test6.txt
f s
1 2
1 2

执行了3次打印
[huawei@n148 awk]$ awk '{print $0}' test6.txt
f s
1 2
1 2

执行了3次打印
[huawei@n148 awk]$ awk '{print $1 $2}' test6.txt
fs
12
12

执行了3次打印
[huawei@n148 awk]$ awk '{print $1,$2}' test6.txt
f s
1 2
1 2

执行了6次打印
[huawei@n148 awk]$ awk '{print $1}{print $2}' test6.txt
f
s
1
2
1
2

```
## 列数 NF、最后一列 $NF、倒数第二列 $(NF-1)
下面的$NF就是$3，因为只有3列
```
[huawei@n148 playground]$ awk '{print NF, $1, $NF}' emp.data
3 Beth 0
3 Dan 0

[huawei@n148 awk]$ cat test4.txt
Allen Phillips
Green Lee
William Aiden James Lee
Angle Jack
Tyler Kevin
Lucas Thomas
Kevin

首先是默认每行的循环，然后再for循环本行的每一列
[huawei@n148 awk]$ awk '{for(i=1;i<=NF;i++){print $i}}'  test4.txt
Allen
Phillips
Green
Lee
William
Aiden
James
Lee
Angle
Jack
Tyler
Kevin
Lucas
Thomas
Kevin
```

## 打印行号（总行数） NR、FNR
用于 END 区域时，代表输入文件的总记录数
```
[huawei@n148 playground]$ awk '{print NR,$0}' emp.data
1 Beth 4.00 0
2 Dan 3.75 0


[huawei@n148 awk]$ cat test1.txt
abc 123 iuy ddd
8ua 456 auv ppp 7y7

打印行号、本行字段数量
[huawei@n148 awk]$ awk '{print NR,NF}' test1.txt
1 4
2 5

多文件时，NR会连续显示行号，而不是每个文件都从1开始
[huawei@n148 awk]$ awk '{print NR,$0}' test1.txt t1.txt
1 abc 123 iuy ddd
2 8ua 456 auv ppp 7y7
3 aaa#bbb#ccc#ddd
4 123#asdf#1#af

FNR能解决上面的问题
[huawei@n148 awk]$ awk '{print FNR,$0}' test1.txt t1.txt
1 abc 123 iuy ddd
2 8ua 456 auv ppp 7y7
1 aaa#bbb#ccc#ddd
2 123#asdf#1#af

```
## 输入列分隔符 FS （或-F）
作用是将一行分为n个列。默认是“空格”。FS 只能在 BEGIN 区域中使用。
也见到过直接拿FS当变量被打印的案例见分割一行排序
```

[huawei@n148 awk]$ cat t1.txt
aaa#bbb#ccc#ddd
123#asdf#1#af

不管是通过-F选项，还是通过FS这个内置变量，结果都一致
[huawei@n148 awk]$ awk -v FS='#' '{print $1,  $2}' t1.txt
aaa bbb
123 asdf

当字段分隔符是单个字符时，下面的所有写法都是对的，即可以把它放在单引号或双引号中，或者不使用引号：
[huawei@n148 awk]$ awk -F# '{print $1,  $2}' t1.txt
aaa bbb
123 asdf
[huawei@n148 awk]$ awk -F'#' '{print $1,  $2}' t1.txt
aaa bbb
123 asdf
[huawei@n148 awk]$ awk -F"#" '{print $1,  $2}' t1.txt
aaa bbb
123 asdf

-------------------------------
[huawei@n148 awk]$ awk -v FS="#" '{printf "第一列:%s\t第二列:%s\n", $1, $2}' t1.txt
第一列:aaa      第二列:bbb
第一列:123      第二列:asdf

-------------------------------

FS 只能在 BEGIN 区域中使用
[huawei@n148 awk]$ awk 'BEGIN {FS=","} {print $2,$3}' employee.txt
John Doe CEO
Jason Smith IT Manager
Raj Reddy Sysadmin
Anand Ram Developer
Jane Miller Sales Manager

-------------------------------

当包含多个字段分隔符的文件时，FS也支持正则

[huawei@n148 awk]$ cat employee-multiple-fs.txt
101,John Doe:CEO%10000
102,Jason Smith:IT Manager%5000
103,Raj Reddy:Sysadmin%4500
104,Anand Ram:Developer%4500
105,Jane Miller:Sales Manager%3000

[huawei@n148 awk]$ awk 'BEGIN {FS="[,:%]"} {print $2,$3}' employee-multiple-fs.txt
John Doe CEO
Jason Smith IT Manager
Raj Reddy Sysadmin
Anand Ram Developer
Jane Miller Sales Manager

```

## 输出列分隔符 OFS
用于对处理完的文本进行输出的时候，以什么字符串作为列之间的分隔符。OFS默认是“空格”，所以输出每一列的时候是用空格隔开的。
```
[huawei@n148 awk]$ df|awk -v OFS="+++" '{print $5,$6}'
Use%+++Mounted
0%+++/dev
1%+++/dev/shm
9%+++/run
0%+++/sys/fs/cgroup
63%+++/

注意观察下面2个$1与$2之间逗号的效果(有逗号会使用OFS)，之间没有逗号则连一起输出（因为空格是连接字符串的操作符。。。）
[huawei@n148 awk]$ awk -v FS='#'  '{print $1,  $2}' t1.txt
aaa bbb
123 asdf
[huawei@n148 awk]$ awk -v FS='#'  '{print $1  $2}' t1.txt
aaabbb
123asdf

```
## 一起使用FS 和 OFS
同时使用自定义的输入与输出分隔符号
```
[huawei@n148 awk]$ awk -v FS='#' -v OFS='+++' '{print $1,  $2}' t1.txt
aaa+++bbb
123+++asdf

```
## 输入行分隔符 RS
作用是将整个文件分为新的n行，也见到过直接拿RS当变量被打印的案例见分割一行排序

不指定RS则默认是”回车换行”，指定RS的目的是为了在读取输入文件时不按照"回车换行"作为一行，而是按照“指定的符号”将整个文件分割为若干行，此时原来的“回车换行”则会被认为是一个普通的符号存在于一行中，参见下例的“8ua”前面是没有行号的，因为它与4 ddd是同一行
```
[huawei@n148 awk]$ cat test1.txt
abc 123 iuy ddd
8ua 456 auv ppp 7y7

使用”空格”作为行分隔符显示文本一共有8行，此时“回车换行”不是分隔符，4 ddd与8ua是同一行
[huawei@n148 awk]$ awk -v RS=" " '{print NR,$0}' test1.txt
1 abc
2 123
3 iuy
4 ddd
8ua
5 456
6 auv
7 ppp
8 7y7

```

## 输出行分隔符 ORS
ORS默认也是”回车换行”，如果指定为其他符号，输出完一行内容需要另起一行时则会输出“指定的符号”，然后再继续输出下一行的内容
```
[huawei@n148 awk]$ awk -v ORS="+++" '{print NR,$0}' test1.txt
1 abc 123 iuy ddd+++2 8ua 456 auv ppp 7y7+++[huawei@n148 awk]$
```
## 一起使用 RS 和 ORS
读取按照空格分割新行，输出的两行之间使用+++
```
[huawei@n148 awk]$ awk -v RS=' ' -v ORS="+++" '{print NR,$0}' test1.txt
1 abc+++2 123+++3 iuy+++4 ddd
8ua+++5 456+++6 auv+++7 ppp+++8 7y7
+++[huawei@n148 awk]$

```

## 文件名 FILENAME
如果 awk 从标准输入获取内容，FILENAME 的值将会是“-”。注意:在 BEGIN 区域内，FILENAME 的值是空，因为 BEGIN 区域只针对 awk 本身，而不处理任
何文件。
```
[huawei@n148 awk]$ awk '{ print FILENAME ": " $0 }' employee.txt
employee.txt: 101,John Doe,CEO
employee.txt: 102,Jason Smith,IT Manager
employee.txt: 103,Raj Reddy,Sysadmin
employee.txt: 104,Anand Ram,Developer
employee.txt: 105,Jane Miller,Sales Manager
```
## 连接字符串 (空格)
(空格)是连接字符串的操作符

```
[huawei@n148 awk]$ cat string.awk
BEGIN {
 FS=",";
 OFS=",";
 string1="Audio";
 string2="Video";
 numberstring="100";
 string3=string1 string2;
 print "Concatenate string is:" string3;
 numberstring=numberstring+1;
 print "String to number:" numberstring;
}

[huawei@n148 awk]$ cat items.txt
101,HD Camcorder,Video,210,10
102,Refrigerator,Appliance,850,2
103,MP3 Player,Audio,270,15
104,Tennis Racket,Sports,190,20
105,Laser Printer,Office,475,5

[huawei@n148 awk]$ awk -f string.awk items.txt
Concatenate string is:AudioVideo
String to number:101

这个案例也解释了为什么在打印多个变量时，如果要使用 OFS 分隔每个字段，就需要
在 print 语句中用逗号分隔每个变量。如果没有使用逗号分隔，那么会把所有值连接成一个字符串。

```
## ARGV、ARGC
* ARGC 保存着传递给 awk 脚本的所有参数的个数
* ARGV 是一个数组，保存着传递给 awk 脚本的所有参数，其索引范围从 0 到 ARGC
* 当传递 5 个参数是，ARGC 的值为 6
* ARGV[0]的值永远是 awk

```
[huawei@n148 awk]$ cat arguments.awk
BEGIN {
 print "ARGC=",ARGC
 for(i=0;i<ARGC;i++)
 print ARGV[i]
}
[huawei@n148 awk]$ awk -f arguments.awk arg1 arg2 arg3 arg4 arg5
ARGC= 6
awk
arg1
arg2
arg3
arg4
arg5


[huawei@n148 awk]$ awk 'BEGIN{print "aaa", ARGV[0], ARGV[1], ARGV[2], ARGC}' p1 p2
aaa awk p1 p2 3
```

实现kv形式参数的高级案例
* 以”参数名 参数值”的格式给 awk 脚本传递一些参数
* awk 脚本获取传递元素的内容和数量作为参数
* 如果把”—item 104 –qty 25”作为参数传递给 awk 脚本，awk 会把商品 104 的数量设置为 25
* 如果把”—item 105 –qty 3”作为参数传递给 awk 脚本，awk 会把商品 105 的数量设置为 3


```
[huawei@n148 awk]$ cat argc-argv.awk
BEGIN {
 FS=",";
 OFS=",";
 for(i=0;i<ARGC;i++)
 {
   if(ARGV[i] == "--item")
   {
     itemnumber=ARGV[i+1];
     delete ARGV[i]
     i++;
     delete ARGV[i]
   }
   else if (ARGV[i]=="--qty")
   {
     quantity=ARGV[i+1]
     delete ARGV[i]
     i++;
     delete ARGV[i]
   }
 }
}
{
 if ($1==itemnumber)
   print $1,$2,$3,$4,quantity
 else
   print $0
}
[huawei@n148 awk]$ awk -f argc-argv.awk --item 104 --qty 25 items.txt
101,HD Camcorder,Video,210,10
102,Refrigerator,Appliance,850,2
103,MP3 Player,Audio,270,15
104,Tennis Racket,Sports,190,25
105,Laser Printer,Office,475,5

```
## ARGIND
* 当前处理的文件被存放在数组 ARGV 中，该数组在 body 区域被访问
* ARGIND是 ARGV 的一个索引，其对应的值是当前正在处理的文件名
* 当 awk 脚本仅处理一个文件时，ARGIND 的值是 1，ARGV[ARGIND]会返回当前正在处理的文件名。

```
[huawei@n148 awk]$ cat argind.awk
{
 print "ARGIND:",ARGIND
 print "Current file:",ARGV[ARGIND]
}
[huawei@n148 awk]$ awk -f argind.awk items.txt items-sold.txt
ARGIND: 1
Current file: items.txt
ARGIND: 1
Current file: items.txt
ARGIND: 1
Current file: items.txt
ARGIND: 1
Current file: items.txt
ARGIND: 1
Current file: items.txt
ARGIND: 2
Current file: items-sold.txt
ARGIND: 2
Current file: items-sold.txt
ARGIND: 2
Current file: items-sold.txt
ARGIND: 2
Current file: items-sold.txt
ARGIND: 2
Current file: items-sold.txt

```
## 数字输出格式 OFMT
初始是"%.6g"，表示一共只输出6位（不包括小数点）
```
[huawei@n148 awk]$ awk -f ofmt.awk
--- using g ---
Default OFMT: 143.123
%.3g OFMT: 143
%.4g OFMT: 143.1
%.5g OFMT: 143.12
%.6g OFMT: 143.123
--- using f ---
%.0f OFMT: 143
%.1f OFMT: 143.1
%.2f OFMT: 143.12
%.3f OFMT: 143.123
[huawei@n148 awk]$ cat ofmt.awk
BEGIN {
 total=143.123456789;
 print "--- using g ---"
 print "Default OFMT:",total;
 OFMT="%.3g"
 print "%.3g OFMT:",total;
 OFMT="%.4g"
 print "%.4g OFMT:",total;
 OFMT="%.5g"
 print "%.5g OFMT:",total;
 OFMT="%.6g"
 print "%.6g OFMT:",total;
 print "--- using f ---"
 OFMT="%.0f";
 print "%.0f OFMT:",total;
 OFMT="%.1f";
 print "%.1f OFMT:",total;
 OFMT="%.2f";
 print "%.2f OFMT:",total;
 OFMT="%.3f";
 print "%.3f OFMT:",total;
}

```

## shell环境变量数组 ENVIRON
ENVIRON 是一个包含所有 shell 环境变量的数组，其索引就是环境变量的名称
```

[huawei@n148 awk]$ cat environ.awk
BEGIN {
OFS="="
for(x in ENVIRON)
print x,ENVIRON[x]
}

[huawei@n148 awk]$ awk -f environ.awk
XDG_DATA_DIRS=/home/huawei/.local/share/flatpak/exports/share:/var/lib/flatpak/exports/share:/usr/local/share:/usr/share
AWKPATH=.:/usr/share/awk
OLDPWD=/home/huawei
LANG=en_US.UTF-8
HISTSIZE=1000
XDG_RUNTIME_DIR=/run/user/1004
...
```
## 不区分大小写 IGNORECASE
默认情况下，IGNORECASE 的值是 0，所有 awk 区分大小写。当把 IGNORECASE 的值设置为 1 时，awk 则不区分大小写，这在使用正则表达式和比较字符串时很有效率。
```
items.txt 中包含的是大写的”Video”,所以找不到
[huawei@n148 awk]$ awk '/video/ {print}' items.txt

当把 IGNORECASE 设置为 1 时，就能匹配并打印包含”Video”的行，因为现在 awk 不区分大小写。
[huawei@n148 awk]$ awk 'BEGIN{IGNORECASE=1} /video/{print}' items.txt
101,HD Camcorder,Video,210,10


---------------------------------------------------------------
同时支持字符串比较和正则表达式。

[huawei@n148 awk]$ awk -f ignorecase.awk items.txt
101,HD Camcorder,Video,210,10
104,Tennis Racket,Sports,190,20
[huawei@n148 awk]$ cat ignorecase.awk
BEGIN {
FS=",";
IGNORECASE=1;
}
{
if ($3 == "video") print $0;
if ($2 ~ "TENNIS") print $0;
}

```
## 错误信息 ERRNO
当执行 I/O 操作(比如 getline)出错时，变量 ERRNO 会保存错误信息。
```
试图用 getline 读取一个不存在的文件，此时 ERRNO 的内容将会是”No such file or directory”

[huawei@n148 awk]$ awk -f errno.awk items.txt
101,HD Camcorder,Video,210,10
No such file or directory
102,Refrigerator,Appliance,850,2
No such file or directory
103,MP3 Player,Audio,270,15
No such file or directory
104,Tennis Racket,Sports,190,20
No such file or directory
105,Laser Printer,Office,475,5
No such file or directory

[huawei@n148 awk]$ cat errno.awk
{
 print $0;
 x = getline < "dummy-file.txt"
 if ( x == -1 )
   print ERRNO
 else
   print $0
}

```
## 模式Pattern
分类：
* 空模式
* 关系运算模式
* BEGIN/END模式
* 正则模式
* 行范围模式

### 模式与动作{ }
即awk [options] ‘Pattern{Action}’ file中的‘Pattern{Action}’部分，由此可以看出Action是要在单引号里的。
* 模式可以理解为条件，如果当前行能与模式匹配，则会执行对应的动作
* 如没有模式只要动作，则默认匹配成功，会每行都执行动作
* 如只有模式没有动作，则默认匹配成功，会默认打印整行，即{print $0}。但”空模式”与”BEGIN/END模式”除外。
* 数字0、空字符串、null(大小写组合随意)表示”假”，不执行动作，反之则为真会执行动作

```
空模式，没有pattern部分，每一行都匹配，结果都是真，每一行都会执行动作
[huawei@n148 awk]$ seq 5|awk '{print $0}'
1
2
3
4
5

只有模式没有动作则默认动作为打印整行
[huawei@n148 awk]$ seq 5|awk '2'
1
2
3
4
5

模式为数字1会匹配成功
[huawei@n148 awk]$ seq 5|awk '1{print $0}'
1
2
3
4
5

模式数字0，无动作，匹配失败
[huawei@n148 awk]$ seq 5|awk '0'

模式数字0，有动作，匹配失败
[huawei@n148 awk]$ seq 5|awk '0{print $0}'

模式null匹配失败
[huawei@n148 awk]$ seq 5|awk 'null {print $0}'

模式NULL匹配失败
[huawei@n148 awk]$ seq 5|awk 'NULL {print $0}'

空字符串匹配失败
[huawei@n148 awk]$ seq 5|awk '"" {print $0}'

还能对真与假进行！取反
[huawei@n148 awk]$ seq 5|awk '!nULL {print $0}'
1
2
3
4
5

[huawei@n148 awk]$ seq 5|awk '!0 {print $0}'
1
2
3
4
5

也可以使用变量值来决定模式的计算结果
[huawei@n148 awk]$ seq 5|awk 'i=0'
[huawei@n148 awk]$ seq 5|awk 'i=1'
1
2
3
4
5
```
### 关系运算模式
只用于模式部分中，类似的if只能用于动作中，这是这2个的区别
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
关系运算符 https://www.zsythink.net/archives/1426
### 模式的组合 && || !
模式可以使用逻辑运算符（&&, ||, 和 !,）进行组合，例如 $2 >= 4 || $3 >= 20
```
[huawei@n148 awk]$ awk ' $2 >= 4 || $3 >= 20 { printf("%.2f for %s\n"),$2*$3,$1 }' emp.data
0.00 for Beth
40.00 for Kathy
100.00 for Mark
121.00 for Mary
76.50 for Susie

```
### 使用正则
* 在awk命令中，正则表达式被放入了两个斜线中
* 判断x匹配正则的语法：	x~/reg/  见下例
* 判断x不匹配正则的语法： x!~/reg/  见下例
```
[huawei@n148 playground]$ awk '/Asia/ { print }' countries
USSR 8649 275 Asia
China 3705 1032 Asia
India 1267 746 Asia
Japan 144 120 Asia
[huawei@n148 playground]$


[huawei@n148 awk]$ awk -v FS=":" 'BEGIN{printf "%-10s\t %s\n", "用户名称","用户id"} /^hua/{printf "%-10s\t %s\n", $1, $3}' /etc/passwd
用户名称         用户id
huawei           1004

如果正则里含有“/”,则需要转义，如使用awk实现grep的效果
[huawei@n148 awk]$ grep "/bin/bash$" /etc/passwd
root:x:0:0:root:/root:/bin/bash
gordon:x:1000:1000:gordon:/home/gordon:/bin/bash
omm:x:1001:1001::/home/omm:/bin/bash
postdb:x:1002:1002::/home/postdb:/bin/bash
pdm:x:1003:1003::/home/pdm:/bin/bash
huawei:x:1004:1004::/home/huawei:/bin/bash
hw:x:1005:1005::/home/hw:/bin/bash
postgres:x:1006:1006::/home/postgres:/bin/bash
jasonliu:x:1007:1007::/home/jasonliu:/bin/bash
jiangdewei:x:1008:1008::/home/jiangdewei:/bin/bash

awk需要把“/bin/bash$”的两侧加“/”，即变为“//bin/bash$/”,然后再把bin和bash之前各自的"/"转义变为“\/”,最终就是下面的丑样了

[huawei@n148 awk]$ awk '/\/bin\/bash$/{print $0}' /etc/passwd
root:x:0:0:root:/root:/bin/bash
gordon:x:1000:1000:gordon:/home/gordon:/bin/bash
omm:x:1001:1001::/home/omm:/bin/bash
postdb:x:1002:1002::/home/postdb:/bin/bash
pdm:x:1003:1003::/home/pdm:/bin/bash
huawei:x:1004:1004::/home/huawei:/bin/bash
hw:x:1005:1005::/home/hw:/bin/bash
postgres:x:1006:1006::/home/postgres:/bin/bash
jasonliu:x:1007:1007::/home/jasonliu:/bin/bash
jiangdewei:x:1008:1008::/home/jiangdewei:/bin/bash



注意默认是使用空格分割列
[huawei@n148 awk]$ awk '{print $1}' employee.txt
101,John
102,Jason
103,Raj
104,Anand
105,Jane

如果第1列符合正则或是不符合正则的使用案例如下
[huawei@n148 awk]$ awk '$1~/^.*n.*n/ {print $2,$1}' employee.txt
Ram,Developer 104,Anand

[huawei@n148 awk]$ awk '$1!~/^.*n.*n/ {print $2,$1}' employee.txt
Doe,CEO 101,John
Smith,IT 102,Jason
Reddy,Sysadmin 103,Raj
Miller,Sales 105,Jane

```
### 使用行范围
* 使用正则匹配，第一段正则是start，第二段是end，应是毕区间，即[start,end]
  
![开闭区间](https://bkimg.cdn.bcebos.com/pic/b7fd5266d0160924d12bf501d60735fae6cd3499?x-bce-process=image/resize,m_lfit,w_268,limit_1/format,f_auto)
* 使用行号匹配
```
实验测试使用正则的细致规范待验证
[huawei@n148 awk]$ awk '/Manager/,/Raj/{print $0}' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
105,Jane Miller,Sales Manager   为啥这行会打印出来？
[huawei@n148 awk]$ awk 'NR>=2 && NR<=4 {print $0}' employee.txt
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer

```
### BEGIN、END
有两个特殊的模式 BEGIN 和 END。
* BEGIN 模式指定了处理文本之前需要执行的操作。适合用来打印报文头部信息，以及用来初始化变量。可以有一个或多个 awk 命令。必须要用大写。是可选的
* END 模式指定了处理完所有行之后所需要执行的操作。适合打印报文结尾信息，以及做一些清理动作。可以有一个或多个 awk 命令。必须要用大写。是可选的



  
BEGIN 与END 分别提供了一种控制初始化与扫尾的方式，BEGIN 与 END 不能与其他模式作组合。如果有多个 BEGIN，会按照它们在程序中出现的顺序执行，多个 END 同样适用。



```
BEGIN的一个常见用途是更改输入行的分隔符，分隔符由内建变量FS 控制，默认由空格分割（FS=' '）。将 FS设置为什么值，就会使其成为字段分隔符。如下将FS设置为了\t，则打印中的空格被换为了\t

[huawei@n148 awk]$ awk 'BEGIN{FS="\t";printf("%10s %10s\n\n","COUNTRY","AREA")}'
   COUNTRY       AREA
```



简单使用
```
只打印BEGIN
[huawei@n148 awk]$ awk 'BEGIN{print "c1", "c2"}'
c1 c2

再加上内容
[huawei@n148 awk]$ df|awk 'BEGIN{print "c1", "c2"} {print $5,$6}'
c1 c2
Use% Mounted
0% /dev
1% /dev/shm
9% /run
0% /sys/fs/cgroup
63% /
24% /boot

再加END
[huawei@n148 awk]$ df|awk 'BEGIN{print "c1", "c2"} {print $5,$6} END{print "e1", "e2"}'
c1 c2
Use% Mounted
0% /dev
1% /dev/shm
9% /run
e1 e2


[huawei@n148 awk]$ awk -v FS=":" 'BEGIN{printf "%-10s\t %s\n", "用户名称","用户id"}{printf "%-10s\t %s\n", $1, $3}' /etc/passwd
用户名称         用户id
root             0
bin              1
daemon           2
adm              3
lp               4
sync             5
shutdown         6
halt             7
mail             8
operator         11
games            12
ftp              14
nobody           99
systemd-network  192

```
完整报表案例  
注意 print "" 打印一个空行, 它与一个单独的 print 并不相同, 后者打印当前行.
```
#!/bin/bash 
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

## 多个输入文件
这里使用了两次相同的文件，可以看出是多个文件依次进行处理的
```
[huawei@n148 awk]$ awk '$3 == 0 { print $1 }' emp.data emp.data
Beth
Dan
Beth
Dan

```
## 读取终端输入
会将program应用到在终端输入的内容，直到输入文件结束标志 (ctrl+d)
```
[huawei@n148 awk]$ awk '$3 == 0 { print $1 }'
a 4 0
a
b 5 10
```
awk 'BEGIN{printf "What is your name?"; getline name < "/dev/tty" }  print name  END{print "See you," name "."}


## 将awk程序放入文件 -f
放在脚本里的演示
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
## 简单判断、if else
简单判断属于“关系运算模式”（详见关系运算模式），语法类似下面这样即可，只可用于模式中
```
[huawei@n148 awk]$ seq 10|awk '$0>5{print $0}'
6
7
8
9
10
```
if else只能用在Action部分。关于if执行的语句体里的大括号 { } 规则与c语言一样，如果语句体是多句则需要加 { } 防止逻辑错误。
```

[huawei@n148 awk]$ awk '{print $1,$2}' test6.txt
f s
1 2
1 2
[huawei@n148 awk]$ awk '{if (NR==1) {print $2,$1}}' test6.txt
s f
[huawei@n148 awk]$ awk '{if (NR==1) {print $2; print $1}}' test6.txt
s
f

更多的条件使用（）
[huawei@n148 awk]$ awk -F ',' '{ if (($4 >= 500 && $4<= 1000) && ($5 <= 5)) print "Only",$5,"qty of",$2,"is available" }' items.txt
Only 2 qty of Refrigerator is available


不加{}因为就一句打印
[huawei@n148 awk]$ awk '{if (NR==1) print $2,$1}' test6.txt
s f

演示一个错误的逻辑，$1被打印了3次，$2只有if成功的那一次
[huawei@n148 awk]$ awk '{if (NR==1) print $2; print $1}' test6.txt
s
f
1
1
```

else与else if的
```
[huawei@n148 awk]$ awk '{if (NR==1) {print $1;print $2} else {print "no"}}' test6.txt
f
s
no
no


[huawei@n148 awk]$ awk '{if (NR==1) {print $1;print $2} else if (NR==2) {print "第二行"} else {print "else"}}' test6.txt
f
s
第二行
else

```
## 三元运算 ? :
```
if形式

[huawei@n148 awk]$ awk -F: '{if($3<500) {usertype="系统用户"}else{usertype="普通用户"};print $1,usertype}' /etc/passwd
root 系统用户
bin 系统用户
daemon 系统用户
adm 系统用户
lp 系统用户
...

使用？：
[huawei@n148 awk]$ awk -F: '{usertype=$3<500?"系统用户":"普通用户";print $1,usertype}' /etc/passwd
root 系统用户
bin 系统用户
daemon 系统用户
adm 系统用户
lp 系统用户
...

接着上面的逻辑进行用户类型的统计

[huawei@n148 awk]$ awk -F: '{$3<500?sysusers++:users++} END{print "sysusers:"sysusers", users:"users}' /etc/passwd
sysusers:30, users:22


```

## while、for、do、break、continue
```
[huawei@n148 awk]$ awk 'BEGIN{for(i=0;i<5;i++){if (i==3){break}; print i}}'
0
1
2

[huawei@n148 awk]$ awk 'BEGIN{for(i=0;i<5;i++){if (i==3){continue}; print i}}'
0
1
2
4

[huawei@n148 awk]$ awk 'BEGIN{i=0;while(i<5){print i++}}'
0
1
2
3
4

[huawei@n148 awk]$ awk -v i=0 'BEGIN{while(i<5){print i++}}'
0
1
2
3
4

[huawei@n148 awk]$ awk 'BEGIN{i=0;do{print i++}while(i<5)}'
0
1
2
3
4
```
在文件里使用
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
## exit、next
exit 命令立即停止脚本的运行，并忽略脚本中其余的命令。exit 命令接受一个数字参数最为 awk 的退出状态码，如果不提供参数，默认的状态码是 0。当awk中有END模式时，如果body里有exit，那么body里exit语句之后的所有动作将不再执行，但END里的动作会执行。见下面的案例
```
[huawei@n148 awk]$ seq 3|awk 'BEGIN{print "start"} {print $0} END{print "over"}'
start
1
2
3
over

[huawei@n148 awk]$ seq 3|awk 'BEGIN{print "start"} {print $0; exit}'
start
1

[huawei@n148 awk]$ seq 3|awk 'BEGIN{print "start"} {print $0; exit} END{print "over"}'
start
1
over
```

next命令可以促使awk不对当前行执行对应的动作，而是直接处理下一行。  
next与continue类似，只不过continue是针对”循环”而言的，next则是针对”逐行处理”而言的，next的作用是结束”对当前行的处理”，从而直接处理”下一行”。  
其实，awk的”逐行处理”也可以理解成为一种”循环”，因为awk一直在”循环”处理着”每一行”，
```
[huawei@n148 awk]$ seq 3|awk 'BEGIN{print "start"} {if (NR==2){next}; print $0;} END{print "over"}'
start
1
3
over
```
## 数组
Awk 的数组，都是关联数组，即一个数组包含多个”索引/值”的元素。索引没必要是一系列连续的数字，实际上，它可以使字符串或者数字，并且不需要指定数组长度。  

下面的案例：
* 数组索引没有顺序，甚至没有从 0 或 1 开始，而是直接从 101….105 开始,然后直接跳到 1001，又降到 55，还有一个字符串索引”na”
* 数组索引可以是字符串，数组的最后一个元素就是字符串索引，即”na”
* Awk 中在使用数组前，不需要初始化甚至定义数组，也不需要指定数组的长度。
* Awk 数组的命名规范和 awk 变量命名规范相同。
* 以 awk 的角度来说，数组的索引通常是字符串，即是你使用数组作为索引，awk 也会当
做字符串来处理。下面的写法是等价的：  
Item[101]=”HD Camcorder”  
Item[“101”]=”HD Camcorder”

```
[huawei@n148 awk]$ cat array-assign.awk
BEGIN {
 item[101]="HD Camcorder";
 item[102]="Refrigerator";
 item[103]="MP3 Player";
 item[104]="Tennis Racket";
 item[105]="Laser Printer";
 item[1001]="Tennis Ball";
 item[55]="Laptop";
 item["na"]="Not Available";
 print item["101"];
 print item[102];
 print item["103"];
 print item[104];
 print item["105"];
 print item[1001];
 print item["na"];
}
[huawei@n148 awk]$ awk -f array-assign.awk
HD Camcorder
Refrigerator
MP3 Player
Tennis Racket
Laser Printer
Tennis Ball
Not Available
```
### 创建、赋值
元素可以赋值为空，效果同变量。即可以使用未定义的元素，值是空
```
[huawei@n148 awk]$ awk 'BEGIN{a[0]=0; a[1]=1; a[2]=2; print a[1]}'
1
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; print a[1]}'
B
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; a[3]=""; print a[3]}'
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; a[3]=""; print a[4]}'
```
### 检测存在 in
如果访问一个不存在的数组元素，awk 会自动以访问时指定的索引建立该元素，并赋予null 值。为了避免这种情况，在使用前最后检测元素是否存在。不能使用判断空字符串来验证元素或变量是否存在，因为awk变量不用创建就可以使用且默认为空（null）。。。
```
错误的方式
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; a[3]=""; if (a[3]=="") print "null"}'
null
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; a[3]=""; if (a[4]=="") print "null"}'
null
[huawei@n148 awk]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; a[3]=""; if (a[100]=="") print "null"}'
null

需要使用in来判断元素是否存在
[huawei@n148 awk]$ awk 'BEGIN{a[1]="B"; a[2]="C"; a[3]=""; if (100 in a) print "exist"}'
[huawei@n148 awk]$ awk 'BEGIN{a[1]="B"; a[2]="C"; a[3]=""; if (3 in a) print "exist"}'
exist

使用！（取反）来判断不存在
[huawei@n148 awk]$ awk 'BEGIN{a[1]="B"; a[2]="C"; a[3]=""; if (!(100 in a)) print "not exist"}'
not exist
```
### 删除 delete
使用 delete 语句。一旦删除了某个元素，就再也获取不到它的值了。如item[103]="" 并没有删除整个元素，仅仅是给它赋了 null 值。
```
删除元素与删除数组

[huawei@n148 ~]$ awk 'BEGIN{a[0]="A"; a[1]="B"; delete a[0]; print a[0]; print a[1]}'

B
[huawei@n148 ~]$ awk 'BEGIN{a[0]="A"; a[1]="B"; delete a; print a[0]; print a[1]}'
```
### 遍历 for
数字下标可以按顺序遍历，字符串下标就按字典顺序了
```
[huawei@n148 ~]$ awk 'BEGIN{a[0]="A"; a[1]="B"; a[2]="C"; for(i=0;i<3;++i){print a[i]}}'
A
B
C
[huawei@n148 ~]$ awk 'BEGIN{a["A"]="A"; a["B"]="B"; a["C"]="C"; for(i in a){print a[i]}}'
A
B
C
[huawei@n148 ~]$ awk 'BEGIN{a["A1"]="A"; a["A3"]="B"; a["A5"]="C"; for(i in a){print a[i]}}'
C
A
B
```

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
### 多维数组
虽然 awk 只支持一维数组，但其奇妙之处在于，可以使用一维数组来模拟多维数组
```
[huawei@n148 awk]$ cat array-multi.awk
BEGIN {
 item["1,1"]=10;
 item["1,2"]=20;
 item["2,1"]=30;
 item["2,2"]=40
 for (x in item)
 print item[x]
}
[huawei@n148 awk]$ awk -f array-multi.awk
10
20
30
40

即使使用了”1,1”作为索引值，它也不是两个索引，仍然是单个字符串索引，值为”1,1”。所以实际上是把 10 赋给一维数组中索引”1,1”代表的值。
```


```
把索引外面的引号去掉，仍然可以运行，但是结果有所不同。在多维数组中，如果没有把下标用引号引住，当指定元素 item[1,2]时，它会被转换为 item[“1\0342”]。Awk 用把两个下标用”\034”连接起来并转换为字符串。

[huawei@n148 awk]$ cat array-multi.awk
BEGIN {
 item[1,1]=10;
 item[1,2]=20;
 item[2,1]=30;
 item[2,2]=40
 for (x in item)
 print item[x]
}

[huawei@n148 awk]$ awk -f array-multi.awk
30
40
10
20
```
### 多维数组下标分隔符 SUBSEP
使用多维数组时，最好不要向上例那样给索引值加引号。通过变量 SUBSEP 可以把默认的下标分隔符改成任意字符
```
[huawei@n148 awk]$ cat d1.awk
BEGIN {
for (i = 1; i <= 2; i++)
   for (j = 1; j <= 3; j++)
   {
     array[i,j] = i * j * 10
   }
for (x in array)
{
   print x, array[x]
}
}

[huawei@n148 awk]$ awk -f d1.awk
21 20
22 40
23 60
11 10
12 20
13 30

其实是创建了一维数组，下标分别为1SUBSEP1, 1SUBSEP2,1SUBSEP3,2SUBSEP1,2SUBSEP2,2SUBSEP3。由于SUBSEP默认是'\034',不可打印，所以输出的第一列是2个数字连在了一起。在BEGIN里加入SUBSEP = ":"后再次运行输出结果如下

[huawei@n148 awk]$ cat d1.awk
BEGIN {
SUBSEP = ":"
for (i = 1; i <= 2; i++)
   for (j = 1; j <= 3; j++)
   {
     array[i,j] = i * j * 10
   }
for (x in array)
{
   print x, array[x]
}
}


[huawei@n148 awk]$ awk -f d1.awk
1:1 10
1:2 20
1:3 30
2:1 20
2:2 40
2:3 60

```
### 文件转数组（使用文件多列）
```
文件有两列
[huawei@n148 awk]$ cat data.txt
1 20
25 45
20 94
60 30

原文件第一列为k，第二列为v，每行按照a[$1]=$2放入数组，但顺序非文件行顺序
[huawei@n148 awk]$ awk '{a[$1]=$2}END{for(i in a) print i,a[i]}' data.txt
20 94
1 20
60 30
25 45

```
### 文件转数组（使用文件单列）
```
文件有两列
[huawei@n148 awk]$ cat data.txt
1 20
25 45
20 94
60 30

使用原文件第二列为v，k则是递增的idx。这样遍历时候顺序与文件行一致

[huawei@n148 awk]$ awk -v idx=0 '{a[idx++]=$2} END{for(i in a) print a[i],i}' data.txt
20 0
45 1
94 2
30 3

[huawei@n148 awk]$ awk -v idx=0 '{a[idx++]=$2} END{for(i=0;i<4;i++) print a[i],i}' data.txt
20 0
45 1
94 2
30 3

```
## 自定义函数 function
```
function fn-name(parameters)
{
	function-body
}
```
* fn-name: 函数名，和 awk 变量名一样，用户定义的函数名应该以字母开头，后续字符可以数字、字母或下划线，关键字不能用做函数名
* parameters:多个参数要使用逗号分开，也可以定义一个没有参数的函数
* function-body:一条或多条 awk 语句
```
[huawei@n148 awk]$ awk -f function.awk items.txt
101,HD Camcorder,Video,189,10
102,Refrigerator,Appliance,765,2
103,MP3 Player,Audio,135,15
104,Tennis Racket,Sports,95,20
105,Laser Printer,Office,427.5,5

[huawei@n148 awk]$ cat function.awk
BEGIN {
 FS=","
 OFS=","
}
{
 if ($5 <= 10)
   print $1,$2,$3,discount(10),$5
 else
   print $1,$2,$3,discount(50),$5
}
function discount(percentage)
{
 return $4 - ($4*percentage/100);
}

```
## 国际化
使用po文件，详见sed_and_awk_101_hacks的97

## 获取时间 systime、strftime
```
systime()函数返回系统的 POSIX 时间，即自 1970 年 1 月 1 日起至今经过的秒数。
[huawei@n148 awk]$ awk 'BEGIN { print systime() }'
1637505079

使用 systime 和 strftime 以可读的格式打印当前时间
[huawei@n148 awk]$ awk 'BEGIN { print strftime("%c",systime()) }'
Sun 21 Nov 2021 10:31:34 PM CST

展示多种可用的时间格式
[huawei@n148 awk]$ awk -f strftime.awk
--- basic formats ---
Format 1: 11/21/2021 22:32:37
Format 2: 11/21/21 10:32:37 PM
Format 3: 11-Nov-2021 22:32:37
Format 4: 11-Nov-2021 22:32:37 CST
Format 5: Sun Nov 21 22:32:37 CST 2021
Format 6: Sunday November 21 22:32:37 CST 2021
--- quick formats ---
Format 7: Sun 21 Nov 2021 10:32:37 PM CST
Foramt 8: 11/21/21
Format 9: 2021-11-21
Format 10: 11/21/2021
Format 11: 10:32:37 PM
--- single line format with %t ---
2021    November        21
--- multi line format with %n ---
2021
November
21
[huawei@n148 awk]$ cat strftime.awk
BEGIN {
 print "--- basic formats ---"
 print strftime("Format 1: %m/%d/%Y %H:%M:%S",systime())
 print strftime("Format 2: %m/%d/%y %I:%M:%S %p",systime())
 print strftime("Format 3: %m-%b-%Y %H:%M:%S",systime())
 print strftime("Format 4: %m-%b-%Y %H:%M:%S %Z",systime())
 print strftime("Format 5: %a %b %d %H:%M:%S %Z %Y",systime())
 print strftime("Format 6: %A %B %d %H:%M:%S %Z %Y",systime())
 print "--- quick formats ---"
 print strftime("Format 7: %c",systime())
 print strftime("Foramt 8: %D",systime())
 print strftime("Format 9: %F",systime())
 print strftime("Format 10: %x",systime())
 print strftime("Format 11: %X",systime())
 print "--- single line format with %t ---"
 print strftime("%Y %t%B %t%d",systime())
 print "--- multi line format with %n ---"
 print strftime("%Y%n%B%n%d",systime())
}

```
## 调用系统函数 system
执行系统命令时，可以传递任意的字符串作为命令的参数，它会被当做操作系统命令准确执行，并返回结果
```
[huawei@n148 awk]$ awk 'BEGIN { system("pwd") }'
/home/huawei/playground/awk
[huawei@n148 awk]$ awk 'BEGIN { system("date") }'
Sun Nov 21 22:40:16 CST 2021

```
## 输出重定向 > 、>>
* 把print语句打印的内容重定向到指定的文件中
```
[huawei@n148 awk]$ cat printf-width4.awk
BEGIN {
 FS=","
 printf "%-3s\t%-10s\t%-10s\t%-5s\t%-3s\n", "Num","Description","Type","Price","Qty" > "report.txt"
 printf "---------------------------------------------------------------------\n" >> "report.txt"
}
{
  if($5 > 10)
   printf "%-3d\t%-10s\t%-10s\t$%-.2f\t%03d\n", $1,$2,$3,$4,$5 >> "report.txt"
}
[huawei@n148 awk]$ awk -f printf-width4.awk items.txt
[huawei@n148 awk]$ cat report.txt
Num     Description     Type            Price   Qty
---------------------------------------------------------------------
103     MP3 Player      Audio           $270.00 015
104     Tennis Racket   Sports          $190.00 020
```
* 执行 awk 脚本时使用重定向
```
[huawei@n148 awk]$ cat printf-width4.awk
BEGIN {
 FS=","
 printf "%-3s\t%-10s\t%-10s\t%-5s\t%-3s\n", "Num","Description","Type","Price","Qty"
 printf "---------------------------------------------------------------------\n"
}
{
  if($5 > 10)
   printf "%-3d\t%-10s\t%-10s\t$%-.2f\t%03d\n", $1,$2,$3,$4,$5
}
[huawei@n148 awk]$ awk -f printf-width4.awk items.txt >report.txt
[huawei@n148 awk]$ cat report.txt
Num     Description     Type            Price   Qty
---------------------------------------------------------------------
103     MP3 Player      Audio           $270.00 015
104     Tennis Racket   Sports          $190.00 020

```

## 输入重定向 getline
正常情况下每从输入文件读取一行，body 区域的代码就会执行一次。用户无法干预，awk 自动执行这个过程。然而使用 geline 命令可以控制 awk 从标准输入、管道或者当前正在处理的文件之外的其他输入文件获得输入。它负责从输入获得下一行的内容，并给NF,NR和FNR等内建变量赋值。如果得到一条记录，getline函数返回1，如果到达文件的末尾就返回0，如果出现错误，例如打开文件失败，就返回-1，可以结合到while等流控制语句使用。

* getline无参数，读入的内容放到$0
```
[huawei@n148 awk]$ awk -F"," '{getline;print $0;}' items.txt
102,Refrigerator,Appliance,850,2
104,Tennis Racket,Sports,190,20
105,Laser Printer,Office,475,5

1 首先awk自动读入101，然后执行{}
2 遇到getline，读入102覆盖原有的101，打印
3 自动读入103，然后getline读入104，覆盖103，打印
4 自动读入105，getline失败，打印105
```
* getline有参数，读入的内容放到参数中
```
相当于打印奇数行了
[huawei@n148 awk]$ awk -F"," '{getline tmp;print $0;}' items.txt
101,HD Camcorder,Video,210,10
103,MP3 Player,Audio,270,15
105,Laser Printer,Office,475,5

奇偶分离
[huawei@n148 awk]$ awk -F"," '{getline tmp; print "$0->",$0;print "tmp->",tmp;}' items.txt
$0-> 101,HD Camcorder,Video,210,10
tmp-> 102,Refrigerator,Appliance,850,2
$0-> 103,MP3 Player,Audio,270,15
tmp-> 104,Tennis Racket,Sports,190,20
$0-> 105,Laser Printer,Office,475,5
tmp-> 104,Tennis Racket,Sports,190,20
```
* 从其他的文件 getline 内容
```
在两个文件中循环切换，打印所有内容。
[huawei@n148 awk]$ awk -F"," '{print $0;getline <"items-sold.txt"; print $0;}' items.txt
101,HD Camcorder,Video,210,10
101 2 10 5 8 10 12
102,Refrigerator,Appliance,850,2
102 0 1 4 3 0 2
103,MP3 Player,Audio,270,15
103 10 6 11 20 5 13
104,Tennis Racket,Sports,190,20
104 2 3 4 0 6 5
105,Laser Printer,Office,475,5
105 10 2 5 7 12 6
```
* 从其他的文件 getline 内容到变量中
```
[huawei@n148 awk]$ awk -F"," '{print $0; getline tmp < "items-sold.txt";print tmp;}' items.txt
101,HD Camcorder,Video,210,10
101 2 10 5 8 10 12
102,Refrigerator,Appliance,850,2
102 0 1 4 3 0 2
103,MP3 Player,Audio,270,15
103 10 6 11 20 5 13
104,Tennis Racket,Sports,190,20
104 2 3 4 0 6 5
105,Laser Printer,Office,475,5
105 10 2 5 7 12 6
```
* getline 执行外部命令并获取其输出

```
执行linux的date命令，并通过管道输出给getline，然后再把输出赋值给自定义变量d，并打印它。

[huawei@n148 awk]$ awk 'BEGIN{ "date" | getline d; print d}' test
Mon Nov 22 02:24:41 CST 2021

---------------------------------------

执行shell的date命令，并通过管道输出给getline，然后getline从管道中读取并将输入赋值给d，split函数把变量d转化成数组mon，然后打印数组mon的第二个元素

[huawei@n148 awk]$  awk 'BEGIN{"date" | getline d; split(d,mon); print mon[2]}'
Nov

---------------------------------------

命令ls的输出传递给geline作为输入，循环使getline从ls的输出中读取一行，并把它打印到屏幕。这里没有输入文件，因为BEGIN块在打开输入文件前执行，所以可以忽略输入文件。

[huawei@n148 awk]$ awk 'BEGIN{while( "ls" | getline) print}'
data2.txt
data.txt
employee-multiple-fs.txt
employee.txt
environ.awk
errno.awk
function.awk
...

---------------------------------------

awk将逐行读取文件/etc/passwd的内容，在到达文件末尾前，计数器lc一直增加，当到末尾时，打印lc的值。注意，如果文件不存在，getline返回-1，如果到达文件的末尾就返回0，如果读到一行，就返回1，所以命令 while (getline < "/etc/passwd")在文件不存在的情况下将陷入无限循环，因为返回-1表示逻辑真，注意关闭文件。

[huawei@n148 awk]$ awk 'BEGIN{while (getline < "/etc/passwd" > 0) lc++; print lc}'
52


---------------------------------------
这个案例比较一般

使用 getline 获取 date 命令的输出并打印出来。请注意这里也要使用 close 刚执行的命令，date 命令的输出保存在变量$0 中（此处getline后面省略则默认到$0里，换个别的变量即可）

[huawei@n148 awk]$ awk -f getline1.awk items.txt
Timestamp:Mon Nov 22 01:45:41 CST 2021
Sell More:Give discount on HD Camcorder immediatelty!
Buy More:Order Refrigerator immediately!
Sell More:Give discount on MP3 Player immediatelty!
Sell More:Give discount on Tennis Racket immediatelty!
Buy More:Order Laser Printer immediately!


其实这个例子并未体现出作用，完全可以在begin里直接system("date")打印出来即可
[huawei@n148 awk]$ cat getline1.awk
BEGIN {
 FS=",";
 "date" | getline			可以换成 "date" | getline timestamp
 close("date")
 print "Timestamp:" $0		可以换成 print "Timestamp:" timestamp
}
{
 if ( $5 <= 5)
   print "Buy More:Order",$2,"immediately!"
 else
   print "Sell More:Give discount on",$2,"immediatelty!"
}

```
## 双向管道 |&
awk 可以使用”|&”和外部进程通信，这个过程是双向的。
```
[huawei@n148 awk]$ awk -f two-way.awk
Sed and Awk is Great!
[huawei@n148 awk]$ cat two-way.awk
BEGIN {
 command = "sed 's/Awk/Sed and Awk/'"
 print "Awk is Great!" |& command
 close(command,"to");
 command |& getline tmp
 print tmp;
 close(command);
}

这个例子中：
1 command = “sed ‘s/Awk/Sed and Awk/’” 这是要和 awk 双向管道对接的命令。它是一个简单的 sed 替换命令，把”Awk”替换为”Sed and Awk”。
2 print “Awk is Great!” |& command 。 command的输入（即sed 替换命令的输入）是”Awk is Great!”。”|&”表示这里是双向管道。”|&”右边命令的输入来自左边命令的输出。
3 close(command,”to”) 命令执行完成，应该关闭”to”进程。
4 command |& getline tmp  。 command执行完成，就要用 getline 获取其输出。前面命令的输出会被存在变量”tmp”中。
5 print tmp  打印输出
6 close(command) 最后关闭命令。

```
## 关闭管道 close
可以在awk中打开一个管道，且同一时刻只能有一个管道存在。通过close()可关闭管道
```
awk把print语句的输出通过管道作为linux命令sort的输入,END块执行关闭管道操作。

[huawei@n148 awk]$ df|awk '{print $5,$1 | "sort" } END {close("sort")}'
0% devtmpfs
0% tmpfs
0% tmpfs
0% tmpfs
100% /dev/sr0
11% tmpfs
1% tmpfs
1% tmpfs
1% tmpfs
24% /dev/sda1
64% /dev/mapper/centos-root
76% /dev/mapper/centos-home
Use% Filesystem

```
## 随机数与取整 sand、rand、int
* rand()函数用于产生 0~1 之间的随机数，它只返回 0~1 之间的数，绝不会返回 0 或 1。
* srand(n)函数使用给定的参数 n 作为种子来初始化随机数的产生过程。不论何时启动，awk只会从 n 开始产生随机数，如果不指定参数 n，awk 默认使用当天的时间作为产生随机数的种子。
* int()函数返回给定参数的整数部分值。n 可以是整数或浮点数，如果使用整数做参数，返回值即是它本身，如果指定浮点数，小数部分会被截断。
```
[huawei@n148 ~]$ awk 'BEGIN{srand(); for (a=0;a<3;a++){print rand()}}'
0.0483539
0.649787
0.665423
[huawei@n148 ~]$ awk 'BEGIN{srand(); for (a=0;a<3;a++){print 100*rand()}}'
75.6052
86.6879
5.01419
[huawei@n148 ~]$ awk 'BEGIN{srand(); for (a=0;a<3;a++){print int(100*rand())}}'
68
89
14
```
## 字符串替换 sub、gsub
* 仅GAWK/NAWK支持的字符串函数。  
* sub仅可替换1次，如果替换操作执行成功，sub 函数返回 1，否则返回 0.
* gsub全部替换。
* 两个函数的第3个参数都是可选的，如果没有指定，则使用$0作为第3个参数。

```
演示sub第3个参数的作用，注意state的值已经被改变了。
[huawei@n148 awk]$ cat sub.awk
BEGIN {
 state="CA is California"
 sub("C[Aa]","KA",state);
 print state;
}
[huawei@n148 awk]$ awk -f sub.awk
KA is California

第 3 个参数是可选的，如果没有指定，awk 会使用$0(当前记录)做为第 3 个参数，如下案例
[huawei@n148 awk]$ cat test11.txt
Allen Phillips
Green Lee
William Ken Allen

sub仅可替换1次
[huawei@n148 awk]$ awk '{sub("l","L"); print }' test11.txt
ALlen Phillips
Green Lee
WiLliam Ken Allen

判断sub的返回值案例
[huawei@n148 awk]$ awk '{ if(sub("HD","High-Def")) print $0; }' items.txt
101,High-Def Camcorder,Video,210,10


仅替换$1，即第一列
[huawei@n148 awk]$ awk '{gsub("l","L",$1); print $0}' test11.txt
ALLen Phillips
Green Lee
WiLLiam Ken Allen

默认是替换所有列，即$0
[huawei@n148 awk]$ awk '{gsub("l","L"); print $0}' test11.txt
ALLen PhiLLips
Green Lee
WiLLiam Ken ALLen

还可以使用正则
[huawei@n148 awk]$ awk '{gsub(/[a-z]/,"@"); print $0}' test11.txt
A@@@@ P@@@@@@@
G@@@@ L@@
W@@@@@@ K@@ A@@@@


如果$4是Asia则换为yazhou
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
## 字符串长度 length
不指定参数则是计算$0的length
```
[huawei@n148 playground]$ awk '{print $1, length($1)}' emp.data
Beth 4
Dan 3
Kathy 5
Mark 4


[huawei@n148 awk]$ awk '{print $0, length()}' test11.txt
Allen Phillips 14
Green Lee 9
William Ken Allen 17

```
## 查找位置 index
* 查找指定字符串在指定列或整行中的起始位置。
* 也可以用 index 来检测指定的字符串(或者字符)是否存在于输入字符串中。如果指定的字符串没有出现，返回 0，就说明指定的字符串不存在
```
[huawei@n148 awk]$ cat test11.txt
Allen Phillips
Green Lee
William Ken Allen

在每一行找l的位置
[huawei@n148 awk]$ awk '{print index($0, "l")}' test11.txt
2
0
3

在第二列找en的位置
[huawei@n148 awk]$ awk '{print index($2, "en")}' test11.txt
0
0
2

演示查找到与未找到的案例
[huawei@n148 awk]$ cat index.awk
BEGIN {
 state="CA is California"
 print "String CA starts at location",index(state,"CA");
 print "String Cali starts at location",index(state,"Cali");
 if(index(state,"NY")==0)
 print "String NY is not found in:",state
}
[huawei@n148 awk]$ awk -f index.awk
String CA starts at location 1
String Cali starts at location 7
String NY is not found in: CA is California
```
## 字符串分割 split
返回分割后的数组长度，注意数组的元素下标从1开始
```

[huawei@n148 awk]$ awk -v ts="qq te ab th" 'BEGIN{arrlen=split(ts,arr," "); for (i=1;i<=arrlen;++i) print arr[i]}'
qq
te
ab
th
[huawei@n148 awk]$ awk -v ts="qq te ab th" 'BEGIN{arrlen=split(ts,arr," "); for (i in arr) print arr[i]}'
th
qq
te
ab

```
## 取子串 substr
```
[huawei@n148 awk]$ cat items.txt
101,HD Camcorder,Video,210,10
102,Refrigerator,Appliance,850,2
103,MP3 Player,Audio,270,15
104,Tennis Racket,Sports,190,20
105,Laser Printer,Office,475,5

指定start与length
[huawei@n148 awk]$ awk '{ print substr($0,1,3) }' items.txt
101
102
103
104
105

仅指定start，默认到结尾
[huawei@n148 awk]$ awk '{ print substr($0,5) }' items.txt
HD Camcorder,Video,210,10
Refrigerator,Appliance,850,2
MP3 Player,Audio,270,15
Tennis Racket,Sports,190,20
Laser Printer,Office,475,5
```

## 检测包含 match、RSTART、RLENGTH
* match 函数从输入字符串中检索给定的字符串(或正则表达式)，当检索到字符串时，返回一个正数值。
* match成功则会设置RSTART（在源中的start）、RLENGTH（在源中的length）
```
[huawei@n148 awk]$ cat match.awk
BEGIN {
 state="CA is California"
 if(match(state,"Cali"))
 {
   print substr(state,RSTART,RLENGTH),"is present in:",state;
 }
}
[huawei@n148 awk]$ awk -f match.awk
Cali is present in: CA is California
```
## 大小写转换 tolower、toupper
```
[huawei@n148 awk]$ awk '{ print tolower($0) }' items.txt
101,hd camcorder,video,210,10
102,refrigerator,appliance,850,2
103,mp3 player,audio,270,15
104,tennis racket,sports,190,20
105,laser printer,office,475,5

[huawei@n148 awk]$ awk '{ print toupper($0) }' items.txt
101,HD CAMCORDER,VIDEO,210,10
102,REFRIGERATOR,APPLIANCE,850,2
103,MP3 PLAYER,AUDIO,270,15
104,TENNIS RACKET,SPORTS,190,20
105,LASER PRINTER,OFFICE,475,5
```
## 排序 asort、asorti
* 都是升序
* asort是对数组的值进行排序
* asorti是对数组的下标进行排序，ascii码顺序
* 返回值都是数组的长度
* 如果不使用第二个参数，数组原始的索引值就不复存在了。排序后使用从1开始的新索引值，原先的索引被覆盖掉了
### 分割一行排序
```
[huawei@n148 awk]$ echo "8 11111 9" | awk '{for(i=1;i<=NF;i++)a[$i]=$i;for(j=1;j<=asorti(a,b);j++)printf a[b[j]] FS;printf RS;delete a}'
11111 8 9

分析：
1 首先for将一行按列NF转为数组a
2 使用asorti按k排序，此处8的16进制是0x38，而1是0x31，字符串比较是先比较第一个字符，那么11111就排在了8之前，尽管数值上11111比8大许多
3 使用for打印出排序后的数组，此处分为原始数组a与新数组b，使用了a[b[j]]其中b[j]为排序后的值，然后再从a中取出对应的元素（其实可以直接print b[j]，因为a的k与v是相同的，此处有点多余）
4 在for打印每个元素后面紧跟着的FS代表使用“输入列分割符，默认的空格”，即打印个空格，换成别的也可以
5 完成for后，打印了RS（输入行分割符号，默认回车换行），然后删除了数组，整体结束
6 调整一下可以看另外的样子，元素间使用\t，输出完使用回车防止[huawei@n148 awk]$连在9后面

[huawei@n148 awk]$ echo "8 11111 9" | awk '{for(i=1;i<=NF;i++)a[$i]=$i;for(j=1;j<=asorti(a,b);j++)printf b[j] "\t\t\t"; printf "\n"; delete b; delete a}'
11111                   8                       9

asort是按照数值的大小来排列的

echo "8 11111 9" | awk '{for(i=1;i<=NF;i++)a[$i]=$i;for(j=1;j<=asort(a);j++)printf a[j] FS;printf RS;delete a}'
```

### 读入文件排序

```
[huawei@n148 awk]$ cat file.txt
aaa 125
ddd 123
bbb 128
ccc 120
[huawei@n148 awk]$ awk '{a[$2]=$0}END{for(i=1;i<=asort(a);i++)print a[i]}' file.txt
aaa 125
bbb 128
ccc 120
ddd 123
[huawei@n148 awk]$ awk '{a[$2]=$0}END{for(i=1;i<=asorti(a,b);i++)print a[b[i]]}' file.txt
ccc 120
ddd 123
aaa 125
bbb 128
```
### 第二个参数的作用
```
[huawei@n148 awk]$ cat data.txt
1 20
25 45
20 94
60 30


通过asort函数对数组排序后，再次输出数组中的元素时，已经按照元素的值的大小进行了排序，但是，数组的下标也被重置为了纯数字

[huawei@n148 awk]$ awk '{a[$1]=$2}END{for(i=1;i<=asort(a);i++) print i"\t"a[i]}' data.txt
1       20
2       30
3       45
4       94
------------------------------------------


asort还有一种用法，就是在对原数组元素值排序的同时，创建一个新的数组，将排序后的元素放置在新数组中，这样能够保持原数组不做任何改变，我们只要打印新数组中的元素值，即可输出排序后的元素值

[huawei@n148 awk]$ awk '{a[$1]=$2} END{for(i=1;i<=asort(a,b);i++) print i"\t"b[i]; print "---"; for(i in a) print i"\t"a[i]}' data.txt
1       20
2       30
3       45
4       94
---
20      94
1       20
60      30
25      45

------------------------------------------


直接使用排序后的新数组：将文件读入数组，转换格式是$1为数组的k，$2为数组的v

[huawei@n148 awk]$ awk '{a[$1]=$2}END{for(i=1;i<=asort(a,b);i++) print i"\t"b[i]}' data.txt
1       20
2       30
3       45
4       94
[huawei@n148 awk]$ awk '{a[$1]=$2}END{len = asort(a, t); for(i=1;i<=len;++i) print i"\t"t[i]}' data.txt
1       20
2       30
3       45
4       94
```

### 使用外部命令 sort
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


## 统计
### 简单递增
if $3>15 则emp+1
```
[huawei@n148 playground]$ awk '$3>15 {emp=emp+1} END{print emp}' emp.data                                              3
```
### 统计文件中各字符串的出现次数
```
[huawei@n148 awk]$ cat test4.txt
Allen Phillips
Green Lee
William Aiden James Lee
Angle Jack
Tyler Kevin
Lucas Thomas
Kevin

将文件赋值到数组，首先循环遍历行的每一列，然后arr[$i]++代表如k相同则v++，end里遍历数组打印k和v

[huawei@n148 awk]$ awk '{for(i=1;i<=NF;i++){arr[$i]++}} END{for(j in arr){print j,arr[j]}}' test4.txt
Tyler 1
Angle 1
James 1
Lucas 1
William 1
Thomas 1
Green 1
Jack 1
Phillips 1
Kevin 2
Lee 2
Allen 1
Aiden 1

````
### 统计行数、词数、字符数
与wc效果相同
```
[huawei@n148 playground]$ awk '{nc=nc+length($0)+1;nw=nw+NF} END{printf("%d lines,%d words,%d chars\n",NR,nw,nc)}' emp.data
6 lines,18 words,77 chars
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

## 把一列改成一行
```
[huawei@n148 playground]$ awk '{names=names $1 " "} END{print names}' emp.data
Beth Dan Kathy Mark Mary Susie
```
## 打印奇偶行
```
1 这里省略了动作，即打印整行。
2 其实就是对一个变量进行不断的取反，让其一直t与f间切换，用于决定是否执行打印整行

[huawei@n148 awk]$ seq 5|awk '!(i=!i)'
2
4
[huawei@n148 awk]$ seq 5|awk 'i=!i'
1
3
5
```


# more，less，tail，head 查看文件内容
# cat、tac、rev
tac 是 cat 的反转版本， 内容会被反转之后输出。 按行来
```
[huawei@n148 sed]$ cat number.txt
one
two
three
[huawei@n148 sed]$ tac number.txt
three
two
one
```

反转一行中每个字符的顺序,rec是按列来
```
[huawei@n148 sed]$ cat employee.txt
101,John Doe,CEO
102,Jason Smith,IT Manager
103,Raj Reddy,Sysadmin
104,Anand Ram,Developer
105,Jane Miller,Sales Manager

[huawei@n148 sed]$ rev employee.txt
OEC,eoD nhoJ,101
reganaM TI,htimS nosaJ,201
nimdasyS,yddeR jaR,301
repoleveD,maR dnanA,401
reganaM selaS,relliM enaJ,501
```

# paste 合并文件
用来将多个文件的内容合并  
-d 指定域分隔符，即指定分隔来自不同文件或不同列的行的分隔符   
-s 将每个多行文件合并为一行，默认用空格或tab键分隔，可用-d选项指定分隔符   
“-” 符号也是paste的一个选项，对于每一个“-”，从标准输入读入一次数据，默认使用空格或tab键作为分隔符，该选项可定制输出格式   
```
[huawei@n148 sed]$ paste employee.txt number.txt
101,John Doe,CEO        one
102,Jason Smith,IT Manager      two
103,Raj Reddy,Sysadmin  three
104,Anand Ram,Developer
105,Jane Miller,Sales Manager


[huawei@n148 sed]$ paste -d@ number.txt employee.txt
one@101,John Doe,CEO
two@102,Jason Smith,IT Manager
three@103,Raj Reddy,Sysadmin
@104,Anand Ram,Developer
@105,Jane Miller,Sales Manager


[huawei@n148 sed]$ paste -s number.txt employee.txt
one     two     three
101,John Doe,CEO        102,Jason Smith,IT Manager      103,Raj Reddy,Sysadmin  104,Anand Ram,Developer 105,Jane Miller,Sales Manager


[huawei@n148 sed]$ cat employee.txt|paste - - -
101,John Doe,CEO        102,Jason Smith,IT Manager      103,Raj Reddy,Sysadmin
104,Anand Ram,Developer 105,Jane Miller,Sales Manager

```
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
使用-n判断$1长度为非0
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
我们经常会碰到这样的问题，用 telnet/ssh 登录了远程的 Linux 服务器，运行了一些耗时较长的任务， 结果却由于网络的不稳定导致任务中途失败。如何让命令提交后不受本地关闭终端窗口/网络断开连接的干扰呢？  
当用户注销（logout）或者网络断开时，终端会收到 HUP（hangup）信号从而关闭其所有子进程。
```
hangup 名称的来由
在 Unix 的早期版本中，每个终端都会通过 modem 和系统通讯。当用户 logout 时，modem 就会挂断（hang up）电话。 同理，当 modem 断开连接时，就会给终端发送 hangup 信号来通知其关闭所有子进程。 
```
解决方法：
* 让进程忽略HUP(hangup)信号

* 让进程运行在新的会话里,从而成为不属于此终端的子进程
## nohup
让提交的命令忽略hangup信号（即no-hangup）。只需在要处理的命令前加上 nohup 即可，标准输出和标准错误缺省会被重定向到nohup.out文件中
* 一般在结尾加上"&"来将命令同时放入后台运行
* ">xxx.log 2>&1 &" 类似这样来更改缺省的重定向文件名
* ">/dev/null 2>&1 &" 重定向到/dev/null，不记录日志
* 如果不将 nohup 命令的输出重定向，输出将附加到当前目录的 nohup.out 文件中。
* 如果当前目录的 nohup.out 文件不可写，输出重定向到 $HOME/nohup.out 文件中。
* 如果没有文件能创建或打开以用于追加，那么 COMMAND参数指定的命令不可调用。
* 如果标准错误是一个终端，那么把指定的命令写给标准错误的所有输出作为标准输出重定向到相同的文件描述符。
* 完整的使用语法类似这样“nohup aaa.sh > aaa.log 2>&1 &”
```

[huawei@n148 job]$ nohup ping www.163.com &
[1] 101364
[huawei@n148 job]$ nohup: ignoring input and appending output to ‘nohup.out’ 意思是忽略输入并把输出追加到"nohup.out"

可以查看到ping一直在后台运行，自身pid是89170，父进程号是132752
[huawei@n148 job]$ ps aux |grep ping
huawei    12567  0.0  0.0 463340  3912 ?        Sl   Nov13   0:16 /usr/libexec/gsd-housekeeping
huawei   101364  0.0  0.0 132756  1720 pts/2    S    21:22   0:00 ping www.163.com
huawei   101487  0.0  0.0 112820   976 pts/2    S+   21:23   0:00 grep --color=auto ping

[huawei@n148 job]$ jobs
[1]+  Running                 nohup ping www.163.com &

----------------------------------------------

输出重定并且向后台运行sh脚本，可以查看到ping与sh各自的进程号
[huawei@n148 job]$ nohup sh a.sh  >tmp.log 2>&1 &
[1] 99011
[huawei@n148 job]$ ps -ef | grep 99011
huawei    99011  74028  0 21:17 pts/2    00:00:00 sh a.sh
huawei    99012  99011  0 21:17 pts/2    00:00:00 ping www.163.com
huawei    99089  74028  0 21:18 pts/2    00:00:00 grep --color=auto 99011
[huawei@n148 job]$ jobs
[1]+  Running                 nohup sh a.sh > tmp.log 2>&1 &

```
## setsid
nohup能通过忽略 HUP 信号来使进程避免中途被中断，但如果进程不属于接受 HUP 信号的终端的子进程，那么自然也就不会受到 HUP 信号的影响了。setsid 就能做到这一点。setsid创建新的会话，并使得调用setsid函数的进程成为新会话的领头进程。调用setsid函数的进程是新创建会话中的惟一的进程组，进程组ID为调用进程的进程号。

* 是新建一个session，父进程号是1（是init进程，相当于创建了init进程的一个子进程，与当前终端的session便不再关联，自然，关闭终端，也不会对其产生影响）。
* 且使用jobs是无法查到相关的作业信息的。
* 如果是setsid一个脚本文件，则父进程号不是1，是init的子进程创建出的子shell的进程号
```
[huawei@n148 job]$ setsid ping www.163.com  >tmp.log 2>&1 &
[1] 116882
[huawei@n148 job]$ jobs
[huawei@n148 job]$ ps -ef |grep ping
huawei    12567  12106  0 Nov13 ?        00:00:16 /usr/libexec/gsd-housekeeping
huawei   116883      1  0 21:53 ?        00:00:00 ping www.163.com
huawei   116918 116302  0 21:53 pts/4    00:00:00 grep --color=auto ping
[1]+  Done                    setsid ping www.163.com > tmp.log 2>&1
```

## ( ) 与 &
```
```
## 终端断开就停止
在末尾加上&即可，每次启动新作业时，Linux系统都会为其分配一个新的作业号和PID。通过ps命令，可以看到所有脚本处于运行状态。

```
[huawei@n148 playground]$ sh t.sh &
```
最好是将后台运行的脚本的STDOUT和STDERR进行重定向，避免与前台的输出混在一起，并且此种方式在终端断开后进程也就都退出了

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
# tmux
https://www.cnblogs.com/weiok/p/4794881.html
http://cn.voidcc.com/question/p-qiqcfgfg-ot.html
```
```
# 函数
## 简单使用
```
function func1() { 
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

# gdb
- info thr：线程列表
- bt：显示堆栈
- f6：进入堆栈的第6层
- up：向上一层
- down：向下一层
- 回车：再次执行上一个命令
- p 变量名：打印变量值
- thr 2：切换到线程2
- gdb -p 进程号：attach到进程
- gdb启动后 attach 进程号：同上
- quit或q：退出gdb
- ctrl+x，ctrl+a，向上箭头，向下箭头：展示，关闭代码，上下移动代码
- gcore 文件名：手动导出core文件
- info break：查看断点
- break 函数名：在指定函数下断点
- delete 断点序号：删除指定断点，无序号则删除所有断点
- break 文件名:行号：指定行断点
- r：运行
- c：执行到下一个断点
- n：单步执行
- s：单步进入

