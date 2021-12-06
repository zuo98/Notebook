## CentOS 常见命令

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
					
==================================================
# jar包启动
nohup java -jar BiuBiuBiu.jar &

#shell脚本启动

# 在Linux命令或者脚本后面增加&符号，从而使命令或脚本在后台执行，例如：.

./my-shell-script.sh &

# 使用nohup在后台执行命令

nohup ./my-shell-script.sh &

tail -f nohup.out -n 500   #查看nohup 0.5秒更新一次；

# -----------

vi /etc/profile  #修改环境变量；
source /etc/profile  #重载环境变量，使得修改生效；

# firewalld的基本使用

systemctl start firewalld      #启动
systemctl stop firewalld      #关闭 
systemctl status firewalld    #查看状态
 
systemctl disable firewalld #开机禁用  
systemctl enable firewalld  #开机启用  

firewall-cmd --list-all  #查看防火墙已经开放的端口

firewall-cmd --zone=public --add-port=3306/tcp --permanent  #给防火墙开放指定端口，重新加载防火墙才生效。

firewall-cmd --reload  #防火墙重新加载


#yum

yum -y install 包名

yum -y update 包名

yum remove 包名


yum list  #查询所有已安装和可安装的软件包

yum list 包名 #查询执行软件包的安装情况

yum search 关键字 #从 yum 源服务器上查找与关键字相关的所有软件包

yum info 包名 #查询执行软件包的详细信息

yum -c /etc/yum.conf --installroot=/opt/all_venv/ --releasever=/  install nginx  #安装到指定路径

#端口查询
netstat -tunlp|grep {port}  #查看占用端口进程的PID：

lsof -i:{端口号}  #查看占用端口进程的PID：

kill -9 {PID}  #杀掉进程
 
ps -ef | grep {programName}  #根据程序名查看对应的PID

ps -aux |grep -v grep|grep {$PID}  #根据PID查看对应的进程

#=======git：
# 暂时保存没有提交的工作
$ git stash
Saved working directory and index state WIP on workbranch:56cd5d4Revert"update old files"
HEAD is now at 56cd5d4Revert"update old files"
# 列出所有暂时保存的工作
$ git stash list
stash@{0}: WIP on workbranch:56cd5d4Revert"update old files"
stash@{1}: WIP on project1:1dd87ea commit "fix typos and grammar"
# 恢复某个暂时保存的工作
$ git stash apply stash@{1}
# 恢复最近一次stash的文件
$ git stash pop
# 丢弃最近一次stash的文件
$ git stash drop
# 删除所有的stash
$ git stash clear


```

