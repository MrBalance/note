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
mkdir /usr/local/soft/mysql
wget https://dev.mysql.com/get/mysql80-community-release-el7-3.noarch.rpm
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
# 在center os下
# 查看数据库初始密码
grep 'temporary password' /var/log/mysqld.log
# 修改密码
mysql> ALTER USER 'root' IDENTIFIED BY 'Root_12root';  
ERROR 1820 (HY000): You must reset your password using ALTER USER statement before executing this statement.
mysql> ALTER USER 'root'@'localhost' IDENTIFIED BY 'Root_12root';
Query OK, 0 rows affected (0.11 sec)

mysql> flush privileges;
Query OK, 0 rows affected (0.01 sec)

mysql> exit
# 修改mysql参数配置
set global validate_password_policy=0;
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

### Mysql8.0注意事项

#### 1. 问题描述

在安装MySQL8.0时，修改临时密码，因密码过于简单（如：123456），不符合MySQL密码规范，会触发一个报错信息：

```mysql
ERROR 1819 (HY000): Your password does not satisfy the current policy requirements。
```

#### 2. 遇到的问题

1. 解决办法调整MySQL密码验证规则，修改 policy 和 length 的值。

2. MySQL 5.7 进行如下设置，即可解决问题：

   ```mysql
   mysql> set global validate_password_policy=0;
   mysql> set global validate_password_length=1;
   ```

3. MySQL 8.0 执行代码：  

   ```mysql
   
   mysql>  set global validate_password_policy=0;
   ERROR 1193 (HY000): Unknown system variable 'validate_password_policy'
    
   mysql> set global validate_password_length=1;
   ERROR 1193 (HY000): Unknown system variable 'validate_password_length'
   ```

#### 3. 问题解决

1、分析： 可以看到，修改 policy 和 length 的值，在MySQL5.7中好使，在MySQL8.0中无效。'validate_password_policy' 变量不存在。

2、解决： 先修改一个满足的密码 （如：Root_12root）。

补充： validate_password_policy 有以下取值：

| Policy      | Tests Performed                                              |
| ----------- | ------------------------------------------------------------ |
| 0 or LOW    | Length                                                       |
| 1 or MEDIUM | Length; numeric, lowercase/uppercase, and special characters |
| 2 or STRONG | Length; numeric, lowercase/uppercase, and special characters; dictionary file |

默认是1，即MEDIUM，所以刚开始设置的密码必须符合长度，且必须含有数字，小写或大写字母，特殊字符。

3. 密码修改后，可用命令查看 validate_password 密码验证插件是否安装。

   ```mysql
   SHOW VARIABLES LIKE 'validate_password%';
   ```

#### 4. 问题总结

1、 通过查看 MySQL5.7 和 MySQL8.0 密码验证插件对比，可知两个版本中，变量名不一样。（*_password_policy 和 *_password.policy ） --- 问题原因所在。

2、MySQL 8.0 调整密码验证规则：

```mysql
mysql> set global validate_password.policy=0;
mysql>  set global validate_password.length=1;
```

