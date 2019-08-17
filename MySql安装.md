# MySql安装

##  一、检查

### 1、检查是否安装

```shell
rpm -qa|grep -i mysql
```

### 2、停止mysql服务,并删除包

```shell
rpm -ev xxx
# 如果提示有依赖则使用如下强制删除
rpm -ev xxx --nodeps  
```

### 3、删除老版本文件

查找之前老版本mysql的目录、并且删除老版本mysql的文件和库

```shell
find / -name mysql 
```

删除对应了文件目录

```shell
rm -rf xxx
```

卸载后/etc/my.cnf不会删除，需要进行手工删除

```shell
rm -rf /etc/my.cnf  
```

再次查找机器是否安装mysql

```shell
rpm -qa|grep -i mysql  
```

无结果，说明已经卸载彻底

## 二、安装

### 1. yum安装

```shell
yum update
yum install mysql-server
```

### 2. mysql相关操作

```shell
# 启动mysql服务：
service mysqld start
#设置开机启动：
chkconfig --add mysqld
#查看开机启动设置是否成功
chkconfig --list | grep mysql*
#mysqld 0:关闭 1:关闭 2:启用 3:启用 4:启用 5:启用 6:关闭
#停止mysql服务：
service mysqld stop
```

### 3. 登录及忘记修改密码

```shell
创建root管理员：
#mysqladmin -u root password 666666

#登录：
mysql -u root -p

#如果忘记密码，则执行以下代码来修改密码

service mysqld stop
mysqld_safe --user=root --skip-grant-tables
mysql -u root
use mysql
update user set password=password("666666") where user="root";
flush privileges;
```

### 远程授权

```sql
use mysql;
-- 查询权限
select user,password,host from user;
-- 授权
revoke all privileges on *.* from root@"x.x.x.x" identified by "密码";
-- 重新加载一下mysql权限
flush privileges;
```

