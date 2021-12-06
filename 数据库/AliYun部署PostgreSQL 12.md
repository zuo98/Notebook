## AliYun CentOS 7部署PostgreSQL 12

### 一、前言

#### 1、本文主要内容

- PostgreSQL 12 安装(yum)
- PostgreSQL 12 基础配置
- PostgreSQL 12 远程访问配置
- PostgreSQL 基础管理

#### 2、本文环境信息与适用范围

- 环境信息

|软件|版本|
|-----|----|
|CentOS| 7.9 Release|
|PostgreSQL| 12.x|

- 适用范围

|软件|版本|
|----|----|
|CentOS|CentOS 7.x|
|PostgreSQL| 9.x-12.x|

### 二、PostgreSQL 安装

参考官网：[PostgreSQL: Linux downloads (Red Hat family)](https://www.postgresql.org/download/linux/redhat/)

#### 1、导入yum源

```shell
sudo yum install -y https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm
```

#### 2、安装PostgreSQL服务

```shell
sudo yum install -y postgresql12 postgresql12-server
```

#### 3、初始化数据库

```shell
sudo /usr/pgsql-12/bin/postgresql-12-setup initdb 
#Initializing database ... OK
```

#### 4、启动PostgreSQL服务

```shell
#启动PostgreSQL服务
sudo systemctl start postgresql-12
#设置PostgreSQL服务为开机启动
sudo systemctl enable postgresql-12
```

### 三、修改Postgres账号密码

PostgreSQL安装成功之后，会默认创建一个名为postgres的Linux用户，初始化数据库后，会有名为postgres的数据库，来存储数据库的基础信息，例如用户信息等等，相当于MySQL中默认的名为mysql数据库。
Postgres数据库中会初始化一名超级用户`postgres`,为了方便我们使用postgres账号进行管理，我们可以修改该账号的密码.

#### 1、进入PostgreSQL命令行

```shell
#通过su命令切换linux用户为postgres会自动进入命令行
su postgres  
```

#### 2、启动SQL Shell

```shell
psql
```

#### 3、修改密码

```sql
    ALTER USER postgres WITH PASSWORD 'NewPassword';             
```

### 三、配置远程访问

#### 1、修改IP绑定和端口

```shell
#修改配置文件 
vi /var/lib/pgsql/12/data/postgresql.conf 
#将监听地址修改为* 
#默认listen_addresses配置是注释掉的，所以可以直接在配置文件开头加入该行 
listen_addresses='*' 
#将端口改为5437(这里自身视情况而定，可以直接使用默认端口) 
#默认port=5432配置是注释掉的， 
port=5437    
```

#### 2、开放端口

```shell
#如果使用的默认端口就执行sudo firewall-cmd --add-port=5432/tcp --permanent 
sudo firewall-cmd --add-port=5437/tcp --permanent 
sudo firewall-cmd --reload   
```

#### 3、允许所有IP访问

```shell
#修改配置文件 
vi /var/lib/pgsql/12/data/pg_hba.conf 
#在文件尾部加入 
host  all  all 0.0.0.0/0 md5
```

#### 4、重启PostgreSQL服务

```shell
 #重启PostgreSQL服务
 sudo systemctl restart postgresql-12  
```
配置完成后即可使用客户端进行连接
### 四、PostgreSQL shell常用语法示例
启动SQL shell：

```shell
#切换用户
su postgres
#启动SQL Shell
psql
```

#### 1、数据库相关语法示例

```sql
#创建数据库 
CREATE DATABASE mydb; 
#查看所有数据库 
\l 
#切换当前数据库 
\c mydb 
#创建表 
CREATE TABLE test(id int,body varchar(100)); 
#查看当前数据库下所有表 
\d        
```

#### 2、用户与访问授权语法示例

```sql
#新建用户 
CREATE USER test WITH PASSWORD 'test'; 
#赋予指定账户指定数据库所有权限 
GRANT ALL PRIVILEGES ON DATABASE mydb TO test; 
#移除指定账户指定数据库所有权限 
REVOKE ALL PRIVILEGES ON DATABASE mydb TO test              
```

> 权限代码：SELECT、INSERT、UPDATE、DELETE、TRUNCATE、REFERENCES、TRIGGER、CREATE、CONNECT、TEMPORARY、EXECUTE、USAGE