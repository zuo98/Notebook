## CentOS 常见命令

### window命令
```shell
#windos
#查看所有进程：
netstat -ano
#查看指定端口的程序：
netstat -ano | findstr "8080" 

tasklist
            tasklist | more 
            tasklist | findstr #相关字段
            taskkill /f /t /im #进程名称
                        /f #杀死所有进程及子进程
                        /t #强制杀死
                        /im #用镜像名称作为进程信息    
                        /pid #用进程id作为进程信息
```

### 启动
```shell
==================================================
# jar包启动
nohup java -jar BiuBiuBiu.jar &

#shell脚本启动

# 在Linux命令或者脚本后面增加&符号，从而使命令或脚本在后台执行，例如：.

./my-shell-script.sh &

# 使用nohup在后台执行命令

nohup ./my-shell-script.sh &

tail -f nohup.out -n 500   #查看nohup 0.5秒更新一次；


nohup java -jar emap-manage-main-1.0.0.jar &

ps -ef | grep emap-manage-main-1.0.0.jar

tail -f nohup.out -n 500

# -----------

vi /etc/profile  #修改环境变量；
source /etc/profile  #重载环境变量，使得修改生效；
```
### 修改ssh默认22端口
```shell
#修改ssh默认22端口
vi /etc/ssh/sshd_config     //进入编辑,放开Port 22 下面增加一行 Port 12321
systemctl restart sshd //重启ssh访问
systemctl status sshd  //查看ssh的启动状态，有报错的话执行下面步骤
vi /etc/sysconfig/selinux  里面改为SELINUX=disabled。
分别执行

# 新增端口
semanage port -a -t ssh_port_t -p tcp 12321

setenforce 0
getenforce

systemctl restart sshd
systemctl status sshd
不报错就正常

试一下新的端口
ssh -p 12321 root@172.16.102.93

查看端口
semanage port -l | grep ssh
==
再去/etc/ssh/sshd_config 把22端口关闭
==============================================
```
### yum 操作

```shell
yum -y install 包名

yum -y update 包名

yum remove 包名


yum list  #查询所有已安装和可安装的软件包

yum list 包名 #查询执行软件包的安装情况

yum search 关键字 #从 yum 源服务器上查找与关键字相关的所有软件包

yum info 包名 #查询执行软件包的详细信息

yum -c /etc/yum.conf --installroot=/opt/all_venv/ --releasever=/  install nginx  #安装到指定路径

```
### 端口|进程查询处理
```shell
#端口查询
netstat -tunlp|grep {port}  #查看占用端口进程的PID：

lsof -i:{端口号}  #查看占用端口进程的PID：

kill -9 {PID}  #杀掉进程
 
ps -ef | grep {programName}  #根据程序名查看对应的PID

ps -aux |grep -v grep|grep {$PID}  #根据PID查看对应的进程

#查看进程：
ps -ef | grep XXX
#查看进程的详细信息（cwd为进程运行目录）
ll /proc/PID
```
### 防火墙|安全SElinux
```shell
关闭SElinux

         1、查看selinux状态

                 [root@localhost ~]# getenforce 
                                     Enforcing         表示启动
       2、临时关闭

                 [root@localhost ~]# setenforce 
                 usage:  setenforce [ Enforcing | Permissive | 1 | 0 ]     1表示启动，0表示关闭
                 [root@localhost ~]# setenforce 0                          临时关闭
                 [root@localhost ~]# getenforce                            查看状态
                 Permissive                                                关闭状态
         3、永久关闭（修改配置文件，即可永久关闭）

                 [root@localhost ~]# vi /etc/selinux/config 
                 # This file controls the state of SELinux on the system.
                 # SELINUX= can take one of these three values:
                 #     enforcing - SELinux security policy is enforced.
                 #     permissive - SELinux prints warnings instead of enforcing.
                 #     disabled - No SELinux policy is loaded.
                 SELINUX=enforcing         修改为"SELINUX=disabled"
                 # SELINUXTYPE= can take one of three values:
                 #     targeted - Targeted processes are protected,
                 #     minimum - Modification of targeted policy. Only selected processes are protected.
                 #     mls - Multi Level Security protection.
                 SELINUXTYPE=targeted
永久有效
# firewalld的基本使用

systemctl start firewalld      #启动
systemctl stop firewalld      #关闭 
systemctl status firewalld    #查看状态
 
systemctl disable firewalld #开机禁用  
systemctl enable firewalld  #开机启用  

firewall-cmd --reload   # 重新加载防火墙配置

firewall-cmd --list-all  #查看防火墙已经开放的端口

firewall-cmd --zone=public --add-port=3306/tcp --permanent  #给防火墙开放指定端口，重新加载防火墙才生效。

# 在防火墙中开放80端口
firewall-cmd --permanent --zone=public --add-port=80/tcp
 
# 检查80端口配置是否有效
firewall-cmd --zone=public --query-port=80/tcp

```
### nacos
```shell
nacos：
	启动服务
	切换到bin目录，执行命令：
	sh startup.sh -m standalone

	win  startup.cmd -m standalone

	关闭服务
	切换到bin目录，执行命令：
	sh shutdown.sh
```
### 文件操作  |新建|传输|复制|删除
```shell
命令格式：
scp -r local_folder remote_username@remote_ip:remote_folder  
或者
scp -r local_folder remote_ip:remote_folder  
第1个指定了用户名，命令执行后需要输入用户密码；  
第2个没有指定用户名，命令执行后需要输入用户名和密码

#centos彻底删除文件夹、文件命令（centos 新建、删除、移动、复制等命令：

1.新建文件夹

mkdir 文件名

新建一个名为test的文件夹在home下

view source1 mkdir /home/test

2.新建文本

在home下新建一个test.sh脚本

vi /home/test.sh

3.删除文件或文件夹

1、删除home目录下的test目录

 rm /home/test

2、这种不带参数的删除方法经常会提示无法删除，因为权限不够。

 rm -r /home/test

3、-r是递归的删除参数表中的目录及其子目录。 目录将被清空并且删除。当删除目录包含的具有写保护的文件时用户通常是被提示的。

rm -rf /home/test

-4、f是不提示用户，删除目录下的所有文件。请注意检查路径，输成别的目录就悲剧了。

 rm -ir /home/test

5、-i是交互模式。使用这个选项，rm命令在删除任何文件前提示用户确认。

4.移动文件或文件夹

mv [options] 源文件或目录 目标文件或目录

示例：

1、移动hscripts文件夹/目录下的所有文件，目录和子目录到tmp目录
mv hscripts tmp
分析：在上述命令中，如果tmp目录已经存在，mv命令将移动hscripts文件夹/目录下的所有文件，目录和子目录到tmp目录。 
如果没有tmp目录，它将重命名 hscripts目录为tmp目录。

2、移动多个文件/更多问价到另一目录
mv file1.txt tmp/file2.txt newdir
这个命令移动当前目录的file1.txt文件和tmp文件夹/目录的file2.txt文件到newdir目录。

参数：

-i：交互方式操作。如果mv操作将导致对已存在的目标文件的覆盖，此时系统询问是否重写，要求用户回答”y”或”n”，这样可以避免误覆盖文件。

-f：禁止交互操作。mv操作要覆盖某个已有的目标文件时不给任何指示，指定此参数后i参数将不再起作用。

5.复制文件或文件夹

cp [options] 来源档(source) 目的檔(destination)

参数：
-a ：相当于 -pdr 的意思；
-d ：若来源文件为连结文件的属性(link file)，则复制连结文件属性而非档案本身；
-f ：为强制 (force) 的意思，若有重复或其它疑问时，不会询问使用者，而强制复制；
-i ：若目的檔(destination)已经存在时，在覆盖时会先询问是否真的动作！
-l ：进行硬式连结 (hard link) 的连结档建立，而非复制档案本身；
-p ：连同档案的属性一起复制过去，而非使用预设属性；
-r ：递归持续复制，用于目录的复制行为；
-s ：复制成为符号连结文件 (symbolic link)，亦即『快捷方式』档案；
-u ：若 destination 比 source 旧才更新 destination ！
最后需要注意的，如果来源档有两个以上，则最后一个目的文件一定要是『目录』才行！

示例:

1、复制两个文件:
cp file1 file2
上述cp命令复制文件file1.php 的内容到文件file2.php中。

2、备份拷贝的文件:
cp -b file1.php file2.php
创建文件file1.php的带着符号‘~’的备份文件file2.php~。
3、复制文件夹和子文件夹:
cp -R scripts scripts1
上面的 cp 命令从 scripts 复制文件夹和子文件夹到 scripts1。

 
```
### 压缩|解压
```shell
#tar负责打包，gzip负责压缩

tar
-c: 建立压缩档案
-x：解压
-t：查看内容
-r：向压缩归档文件末尾追加文件
-u：更新原压缩包中的文件

这五个是独立的命令，压缩解压都要用到其中一个，可以和别的命令连用但只能用其中一个。下面的参数是根据需要在压缩或解压档案时可选的。

-z：有gzip属性的
-j：有bz2属性的
-Z：有compress属性的
-v：显示所有过程
-O：将文件解开到标准输出

下面的参数-f是必须的

-f: 使用档案名字，切记，这个参数是最后一个参数，后面只能接档案名。

tar -cf all.tar *.jpg
这条命令是将所有.jpg的文件打成一个名为all.tar的包。-c是表示产生新的包，-f指定包的文件名。

tar -rf all.tar *.gif
这条命令是将所有.gif的文件增加到all.tar的包里面去。-r是表示增加文件的意思。

tar -uf all.tar logo.gif
这条命令是更新原来tar包all.tar中logo.gif文件，-u是表示更新文件的意思。

tar -tf all.tar
这条命令是列出all.tar包中所有文件，-t是列出文件的意思

tar -xf all.tar
这条命令是解出all.tar包中所有文件，-x是解开的意思

压缩
tar –cvf jpg.tar *.jpg //将目录里所有jpg文件打包成tar.jpg
tar –czf jpg.tar.gz *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用gzip压缩，生成一个gzip压缩过的包，命名为jpg.tar.gz
tar –cjf jpg.tar.bz2 *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用bzip2压缩，生成一个bzip2压缩过的包，命名为jpg.tar.bz2
tar –cZf jpg.tar.Z *.jpg //将目录里所有jpg文件打包成jpg.tar后，并且将其用compress压缩，生成一个umcompress压缩过的包，命名为jpg.tar.Z
rar a jpg.rar *.jpg //rar格式的压缩，需要先下载rar for linux
zip jpg.zip *.jpg //zip格式的压缩，需要先下载zip for linux

解压
tar –xvf file.tar //解压 tar包
tar -xzvf file.tar.gz //解压tar.gz
tar -xjvf file.tar.bz2 //解压 tar.bz2
tar –xZvf file.tar.Z //解压tar.Z
unrar e file.rar //解压rar
unzip file.zip //解压zip

总结
1、.tar 用 tar –xvf 解压
2、.gz 用 gzip -d或者gunzip 解压
3、.tar.gz和.tgz 用 tar –xzf 解压
4、.bz2 用 bzip2 -d或者用bunzip2 解压
5、.tar.bz2用tar –xjf 解压
6、.Z 用 uncompress 解压
7、.tar.Z 用tar –xZf 解压
8、.rar 用 unrar e解压
9、.zip 用 unzip 解压
```

