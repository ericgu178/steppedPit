# LNMP的安装

我们先把yum源换成国内的阿里云镜像源（当然不换也可以），先备份一下原来的源镜像文件，以免出错后可以恢复： 

`[root@192 ~]# mv /etc/yum.repos.d/CentOS-Base.repo /etc/yum.repos.d/CentOS-Base.repo.backup`

下载新的CentOS-Base.repo 到/etc/yum.repos.d/，版本根据自己的系统版本选择下载： 

```
CentOS 5
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-5.repo

CentOS 6
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-6.repo

CentOS 7
wget -O /etc/yum.repos.d/CentOS-Base.repo http://mirrors.aliyun.com/repo/Centos-7.repo
```

```
yum clean all
yum makecache
yum update
```

> nginx 安装

`yum -y install nginx`

> mysql 安装

安装MySQL 5.7版本，官网http://dev.mysql.com/downloads/repo/yum/ 

```
rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

安装MySQL 5.7版本，官网http://dev.mysql.com/downloads/repo/yum/ 

```
rpm -Uvh  http://dev.mysql.com/get/mysql57-community-release-el7-9.noarch.rpm
```

可以看到已经有了，并且5.7版本已经启用，可以直接安装：

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

```
root@192 yum.repos.d]# yum repolist all | grep mysql
mysql-connectors-community/x86_64 MySQL Connectors Community         启用:    24
mysql-connectors-community-source MySQL Connectors Community - Sourc 禁用
mysql-tools-community/x86_64      MySQL Tools Community              启用:    38
mysql-tools-community-source      MySQL Tools Community - Source     禁用
mysql-tools-preview/x86_64        MySQL Tools Preview                禁用
mysql-tools-preview-source        MySQL Tools Preview - Source       禁用
mysql55-community/x86_64          MySQL 5.5 Community Server         禁用
mysql55-community-source          MySQL 5.5 Community Server - Sourc 禁用
mysql56-community/x86_64          MySQL 5.6 Community Server         禁用
mysql56-community-source          MySQL 5.6 Community Server - Sourc 禁用
mysql57-community/x86_64          MySQL 5.7 Community Server         启用:   146
mysql57-community-source          MySQL 5.7 Community Server - Sourc 禁用
mysql80-community/x86_64          MySQL 8.0 Community Server         禁用
mysql80-community-source          MySQL 8.0 Community Server - Sourc 禁用
[root@192 yum.repos.d]# 
```

[![复制代码](https://common.cnblogs.com/images/copycode.gif)](javascript:void(0);)

如果没有开启，或者你想要选择需要的版本进行安装，修改 /etc/yum.repos.d/mysql-community.repo，选择需要的版本把enable改为1即可，其它的改为0：

![img](https://images2015.cnblogs.com/blog/847828/201610/847828-20161023210820279-947159587.png)

修改好后查看可用的安装版本：

```
[root@192 yum.repos.d]# yum repolist enabled | grep mysql
mysql-connectors-community/x86_64 MySQL Connectors Community                  24
mysql-tools-community/x86_64      MySQL Tools Community                       38
mysql57-community/x86_64          MySQL 5.7 Community Server                 146
```

不用犹豫，开始安装吧！

```
yum -y install mysql-community-server
```

 ……经过漫长的等待后，看到下图所示：

![img](https://images2015.cnblogs.com/blog/847828/201610/847828-20161023225326373-589477115.png)

开始启动mysql：

```
service mysqld start
Redirecting to /bin/systemctl start  mysqld.service
```

看下mysql的启动状态：

```
[root@192 yum.repos.d]# service mysqld status
Redirecting to /bin/systemctl status  mysqld.service
● mysqld.service - MySQL Server
   Loaded: loaded (/usr/lib/systemd/system/mysqld.service; enabled; vendor preset: disabled)
   Active: active (running) since 日 2016-10-23 22:51:48 CST; 3min 14s ago
  Process: 36884 ExecStart=/usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid $MYSQLD_OPTS (code=exited, status=0/SUCCESS)
  Process: 36810 ExecStartPre=/usr/bin/mysqld_pre_systemd (code=exited, status=0/SUCCESS)
 Main PID: 36887 (mysqld)
   CGroup: /system.slice/mysqld.service
           └─36887 /usr/sbin/mysqld --daemonize --pid-file=/var/run/mysqld/mysqld.pid

10月 23 22:51:45 192.168.0.14 systemd[1]: Starting MySQL Server...
10月 23 22:51:48 192.168.0.14 systemd[1]: Started MySQL Server.
10月 23 22:52:24 192.168.0.14 systemd[1]: Started MySQL Server.
```

开机启动设置：

```
systemctl enable mysqld
systemctl daemon-reload
```

mysql安装完成之后，在/var/log/mysqld.log文件中给root生成了一个默认密码。通过下面的方式找到root默认密码，然后登录mysql进行修改：

```
[root@192 yum.repos.d]# grep 'temporary password' /var/log/mysqld.log
2016-10-23T14:51:45.705458Z 1 [Note] A temporary password is generated for root@localhost: a&sqr7dou7N_
mysql -uroot -p
```

修改root密码：

```
ALTER USER 'root'@'localhost' IDENTIFIED BY 'NewPassWord!';
```

 注意：mysql5.7默认安装了密码安全检查插件，默认密码检查策略要求密码必须包含：大小写字母、数字和特殊符号，并且长度不能少于8位。否则会提示ERROR 1819 (HY000): Your password does not satisfy the current policy requirements错误，如下图所示：

![img](https://images2015.cnblogs.com/blog/847828/201610/847828-20161023230329748-764210032.png)

通过msyql环境变量可以查看密码策略的相关信息

```
mysql> show variables like '%password%';
+---------------------------------------+--------+
| Variable_name                         | Value  |
+---------------------------------------+--------+
| default_password_lifetime             | 0      |
| disconnect_on_expired_password        | ON     |
| log_builtin_as_identified_by_password | OFF    |
| mysql_native_password_proxy_users     | OFF    |
| old_passwords                         | 0      |
| report_password                       |        |
| sha256_password_proxy_users           | OFF    |
| validate_password_check_user_name     | OFF    |
| validate_password_dictionary_file     |        |
| validate_password_length              | 8      |
| validate_password_mixed_case_count    | 1      |
| validate_password_number_count        | 1      |
| validate_password_policy              | MEDIUM |
| validate_password_special_char_count  | 1      |
+---------------------------------------+--------+
14 rows in set (0.00 sec)
```

validate_password_policy：密码策略，默认为MEDIUM策略 
validate_password_dictionary_file：密码策略文件，策略为STRONG才需要 
validate_password_length：密码最少长度 
validate_password_mixed_case_count：大小写字符长度，至少1个 
validate_password_number_count ：数字至少1个 
validate_password_special_char_count：特殊字符至少1个 
*上述参数是默认策略MEDIUM的密码检查规则。*

### 修改密码策略

如果想修改密码策略，在/etc/my.cnf文件添加validate_password_policy配置：

```
# 选择0（LOW），1（MEDIUM），2（STRONG）其中一种，选择2需要提供密码字典文件
validate_password_policy=0
```

## 配置默认编码为utf8

修改/etc/my.cnf配置文件，在[mysqld]下添加编码配置，如下所示：

```
[mysqld]
character_set_server=utf8
init_connect='SET NAMES utf8'
```

重新启动mysql服务使配置生效：

```
systemctl restart mysqld
```

## 添加远程登录用户

默认只允许root帐户在本地登录，如果要在其它机器上连接mysql，必须修改root允许远程连接，或者添加一个允许远程连接的帐户，为了安全起见，我们添加一个新的帐户：

```
mysql> GRANT ALL PRIVILEGES ON *.* TO 'evai'@'%' IDENTIFIED BY '@evai2016' WITH GRANT OPTION;
```

```
mysql> FLUSH PRIVILEGES;
```

这样远程就可以用账户名为evai，密码为@evai2016来登录数据库了，运行 select host, user from mysql.user 查看下：

```
mysql> select host,user from mysql.user;
+-----------+-----------+
| host      | user      |
+-----------+-----------+
| %         | evai      |
| localhost | mysql.sys |
| localhost | root      |
+-----------+-----------+
3 rows in set (0.00 sec)
```

mysql 踩坑

1 连接MySQL数据库时出现The dirver has not received any packets from the server

原因 是 mysql 数据库不允许ip访问

解决方法一：　　　

　　1、我们现在增加一个'wls'用户，密码为'ybsoft'，让其能够从外部访问MYSQL。输入
　　grant all on * to 'wls' identified by 'ybsoft';

　　2、现在看看用户表内容。输入select user,host from user ; 可以看到"wls"用户已经加进去了，并且其权限为'% '。

　　3、我们现在可以用wls用户在局域网或互联网中以IP方式访问了。

　　　　*mysql -h 192.168.0.115 -u wls-p*

　　*4、FLUSH   PRIVILEGES  //修改生效*

　　*5、bind-address= 127.0.0.1注释掉即可------>在my.ini中找到bind-address=0.0.0.0就是所有IP地址都能访问，也可以不要这个属性，具体情况自己试试就知道了。*

解决方法二：

```修改root 的host为'%'
修改 root 的host 为 ‘%’

mysql -u root -p

use mysql;

update user set host = '%' where user = 'root'```

flush privileges;

```

解决方法三授权法。

例如，你想myuser使用mypassword从任何主机连接到mysql服务器的话。

GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'%' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;

FLUSH PRIVILEGES;

如果你想允许用户myuser从ip为192.168.1.6的主机连接到mysql服务器，并使用mypassword作为密码

GRANT ALL PRIVILEGES ON *.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;

FLUSH PRIVILEGES;

如果你想允许用户myuser从ip为192.168.1.6的主机连接到mysql服务器的dk数据库，并使用mypassword作为密码

GRANT ALL PRIVILEGES ON dk.* TO 'myuser'@'192.168.1.3' IDENTIFIED BY 'mypassword' WITH GRANT OPTION;

FLUSH PRIVILEGES;

####  如若这些方法还是不行

那么很有可能是防火墙的原因 关闭防火墙试一试

```装有 iptables 的是这个 systemctl stop iptables.service ```

``` firewalld 的是这个 systemctl stop firewalld  ```

> php 安装

若直接采用centos中的yum安装：sudo yum -y install php，版本是5.4，远远不够，因此我们要手动更新rpm即可。

首先获取rpm：

rpm -Uvh https://dl.fedoraproject.org/pub/epel/epel-release-latest-7.noarch.rpm   
rpm -Uvh https://mirror.webtatic.com/yum/el7/webtatic-release.rpm    
然后可以利用 sudo yum list php*查看目前都有php的什么版本了，可以发现从4-7.2的版本都有，7.2版本名为72w，因此安装该版本即可：

```sudo yum -y install php72w```
但安装完毕后，输入php -v发现并没有该命令，因为php72w只是安装了php最小的库，一些应用还未安装，因此安装一些拓展包即可：

```yum -y install php72w-cli php72w-common php72w-devel php72w-mysql ```
对于wordpress应用，可能还需安装如下包：

`sudo yum -y install php72w-gd php72w-imap php72w-ldap php72w-odbc php72w-pear php72w-xml php72w-xmlrpc`

然后输入php -v出现如下信息：

```
[c@localhost ~]$ php -v

PHP 7.2.5 (cli) (built: Apr 28 2018 07:30:30) ( NTS )

Copyright (c) 1997-2018 The PHP Group

Zend Engine v3.2.0, Copyright (c) 1998-2018 Zend Technologies

```

安装完毕。
--------------------- 