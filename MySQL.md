# MySQL #


### Red Hat Linux 安装

官网下载  http://dev.mysql.com/downloads/mysql/  
安装文档  http://dev.mysql.com/doc/refman/5.7/en/linux-installation-rpm.html  

**mysql服务端**    
mysql-community-server-5.7.11-1.el7.x86_64.rpm  

**mysql客户端**    
mysql-community-client-5.7.11-1.el7.x86_64.rpm  

**mysql开发部分**    
mysql-community-devel-5.7.11-1.el7.x86_64.rpm

**mysql-common**    
mysql-community-common-5.7.11-1.el7.x86_64.rpm

**mysql-libs**    
mysql-community-libs-5.7.11-1.el7.x86_64.rpm

**查看本机是否安装mysql**  
rpm -qa|grep mysql

**开始安装**  
rpm -ivh mysql-community-server-5.7.11-1.el7.x86_64.rpm --nodeps --force
rpm -ivh mysql-community-client-5.7.11-1.el7.x86_64.rpm
rpm -ivh mysql-community-devel-5.7.11-1.el7.x86_64.rpm

**安全模式启动服务（第一次登入建议使用安全模式设置root密码）**  
mysqld_safe --user=root --skip-grant-tables --skip-networking &

**设置root密码(安全模式)**  
sudo mysql_secure_installation   
或者  
SET PASSWORD FOR 'root'@'localhost' = PASSWORD('abcd123');

**常规模式启动服务**  
sudo service mysqld start
sudo service mysqld stop
sudo service mysqld restart

**设置root密码(常规模式)** 
sudo grep 'temporary password' /var/log/mysqld.log 查看临时密码  
mysql -uroot -p   
ALTER USER 'root'@'localhost' IDENTIFIED BY 'MyNewPass4!';  

**查看状态**
sudo service mysqld status

**创建用户**  
CREATE USER 'pig'@'%' IDENTIFIED BY '123456';

**刷新系统权限表(用户相关操作后最好使用该命令)**  
flush privileges

**授权**  
grant all on databasename.* to pig@'%'；

**登录新建用户**  
mysql -upig -p123456

### FAQ ###  

1. mysql服务启动不成功？  
需要使用linux管理员权限启动

2. 安装报错？  
安装命令加上 --nodeps --force  不分析依赖强制安装

3. 新建用户不能创建数据库  
新用户需要授予create权限才能创建数据库

## 复制  
***
**创建复制用户**  
主库和备库都创建该账户  
`CREATE USER 'repl'@'%' IDENTIFIED BY 'MyNewPass4!';     
GRANT Replication client ON *.* TO 'repl'@'%';  
GRANT Replication slave ON *.* TO 'repl'@'%';   
FLUSH PRIVILEGES;`

**配置主库和备库(重新启动)**
* 主库  
`vi /etc/my.cnf`  
`+ log_bin=mysql-bin    
   servier_id=1`
* 备库  
`vi /etc/my.cnf`  
`+ log_bin=mysql-bin    
   servier_id=1  
   relay_log=/var/lib/mysql/mysql-relay-bin  
   log_slave_updates=1  
   read_only=1`  
   
**启动复制**  
* 主库  
`show slave status;
show processlist;`
* 备库  
`change master to master_host='192.168.3.220',  
master_user='repl',  
master_password='MyNewPass4!',  
master_log_file='mysql-bin.000001',  
master_log_pos=0;`  

`show slave status;
start slave;`

`show processlist;`

**克隆备库**
* 冷备份

* 热备份
mysqlhotcopy、rsync、mysqldump、Xtrabackup

**复制的管理和维护**
percona-toolkit
