# RedHat Yum基础配置

## RedHat Yum源配置

### 1.卸载原来的yum源

```shell
rpm -qa|grep yum|xargs rpm -e --nodeps
```

### 2.下载新的yum安装包

```shell
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-3.2.29-81.el6.centos.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-iniparse-0.3.1-2.1.el6.noarch.rpm
wget http://mirrors.163.com/centos/6/os/x86_64/Packages/python-urlgrabber-3.9.1-11.el6.noarch.rpm
```

### 3.安装yum软件包

```shell
rpm -ivh python-iniparse-0.3.1-2.1.el6.noarch.rpm
rpm -ivh yum-metadata-parser-1.1.2-16.el6.x86_64.rpm
rpm -e python-urlgrabber-3.9.1-9.el6.noarch
rpm -ivh python-urlgrabber-3.9.1-11.el6.noarch.rpm
rpm -ivh yum-3.2.29-81.el6.centos.noarch.rpm yum-plugin-fastestmirror-1.1.30-41.el6.noarch.rpm
```

### 4.更改yum源

```shell
cd /etc/yum.repos.d/
wget http://mirrors.163.com/.help/CentOS6-Base-163.repo
vi CentOS6-Base-163.repo
# 把文件里面的$releasever全部替换为版本号，即6 最后保存
:%s/$releasever/6/g
```

### 5.清理yum缓存

```shell
yum clean all
#将服务器上的软件包信息缓存到本地,以提高搜索安装软件的速度 #
yum makecache
#测试域名是否可用
yum install vim*
```

## jdk安装

### 1.下载

```shell
yum -y install java-1.8.0-openjdk java-1.8.0-openjdk-devel
dirname $(readlink $(readlink $(which java)))
```

### 2.解压

```shell
tar –xvzf jdk.xxx.tar
```

### 3.配置环境变量

```shell
vim /etc/profile
```

在文件最后添加以下信息

```properties
#Set Java Environment
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.222.b10-0.el7_6.x86_64
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
```

### 4.使更改的配置文件

```shell
source /etc/profile
```

### 5.校验安装是否成功

```shell
javac
java -version
```

## Centos7安装yum配置

### 1. 查看Centos版本

```
cat /etc/redhat-release
```

### 2. 下载

```shell
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-libs-2.7.5-76.el7.x86_64.rpm
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-2.7.5-76.el7.x86_64.rpm 
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-iniparse-0.4-9.el7.noarch.rpm
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/python-pycurl-7.19.0-19.el7.x86_64.rpm
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/rpm-python-4.11.3-35.el7.x86_64.rpm  
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-metadata-parser-1.1.4-10.el7.x86_64.rpm 
# 最后两个因为相互依赖需要一起安装
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-3.4.3-161.el7.centos.noarch.rpm 
wget http://mirrors.163.com/centos/7/os/x86_64/Packages/yum-plugin-fastestmirror-1.1.31-50.el7.noarch.rpm
```

### 3. 安装

```shell
rpm -ivh python-libs-2.7.5-76.el7.x86_64.rpm
rpm -ivh python-2.7.5-76.el7.x86_64.rpm 
rpm -ivh python-iniparse-0.4-9.el7.noarch.rpm
rpm -ivh python-pycurl-7.19.0-19.el7.x86_64.rpm
rpm -ivh rpm-python-4.11.3-35.el7.x86_64.rpm  
rpm -ivh yum-metadata-parser-1.1.4-10.el7.x86_64.rpm 
rpm -ivh yum-3.4.3-161.el7.centos.noarch.rpm yum-plugin-fastestmirror-1.1.31-50.el7.noarch.rpm
```

### 4. 更改yum源

```shell
cd /etc/yum.repos.d/
wget http://mirrors.163.com/.help/CentOS7-Base-163.repo
vi CentOS6-Base-163.repo
# 把文件里面的$releasever全部替换为版本号，即7 最后保存
:%s/$releasever/7/g
```

### 	5. 清除缓存

```shell
#清除缓存
yum clean all
#将服务器上的软件包信息缓存到本地,以提高搜索安装软件的速度 #
yum makecache
#测试域名是否可用
yum install vim*
```

