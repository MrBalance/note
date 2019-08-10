# Dobbo + Zookeeper 安装

## 一. 下载

zookeeper : https://www.apache.org/dyn/closer.cgi/zookeeper/

dobbo:

- zip : https://www.apache.org/dyn/closer.cgi?path=dubbo/2.7.3/apache-dubbo-2.7.3-src.zip
- github : https://github.com/apache/dubbo
- dubbo-admin新版 : https://github.com/apache/dubbo-ops

tomcat : https://tomcat.apache.org/download-90.cgi

或者

```shell
wget http://mirror.bit.edu.cn/apache/zookeeper/stable/apache-zookeeper-3.5.5-bin.tar.gz
```

## 二. Zookeeper安装

### 1.创建文件夹

在/usr/local 路径下创建soft文件夹

```shell
mkdir /usr/local/soft
```

将文件上传至此

### 2.解压

```shell
tar xzvf /usr/local/soft/apache-zookeeper-3.5.5-bin.tar.gz
mv ./apache-zookeeper-3.5.5-bin zookeeper-3.5.5
```

### 3.创建日志存放文件夹

```
mkdir /usr/local/soft/zookeeper-3.5.5/data
```

### 4. 修改zoo_sample.cfg 为zoo.cfg

```shell
cd /usr/local/soft/zookeeper-3.5.5/conf
cp ./zoo_sample.cfg ./zoo.cfg
```

### 5. 更改配置文件

```shell
vim ./zoo.cfg
```

修改zoo.conf中的datalogDir路径为/usr/local/soft/zookeeper-3.5.5/data

![](img\zookeeper+dobbo\截图1.jpg)

　配置文件中参数说明：

　　　　tickTime : 服务器与客户端之间交互的基本时间单元（ms）

　　　　dataDir : 保存zookeeper数据路径

　　　　dataLogDir : 保存zookeeper的日志路径，当此配置不存在时默认路径与dataDir一致

　　　　clientPort : 客户端访问zookeeper时经过服务器端时的端口号

### 6. 更改环境变量

```shell
vim  /etc/profile
```

将一下代码添加至末尾

```
## zookeeper
export ZOOKEEPER_HOME=/usr/local/soft/zookeeper-3.5.5
export PATH=$ZOOKEEPER_HOME/bin:$PATH
```

### 7. 启动zookeeper

```shell
zkServer.sh start
#关闭内嵌的管理控制台防止占用8080端口
zkServer.sh cd start
# 带日志启动
zkServer.sh start-foreground
```

也可以zoo.cfg中增加admin.serverPort=没有被占用的端口号

看见如图则表示启动成功

![截图2](/Users/duyunzhang/Desktop/note/img/zookeeper+dobbo/截图2.jpg)

停止zookeeper 

```shell
zkServer.sh stop
```

看见如图则表示关闭成功

![截图3](/Users/duyunzhang/Desktop/note/img/zookeeper+dobbo/截图3.jpg)

## 三. dobbo安装

### 1. 安装tomcat

ps：（新版不需要，但还是记录一下）

#### 1.1.上传文件

将文件上传至 /usr/local/soft

#### 1.2 解压

```shell
tar xzvf /usr/local/soft/apache-tomcat-9.0.22.tar.gz
# 重命名
mv apache-tomcat-9.0.22 tomcat-9.0.22
```

#### 1.3 更改配置文件

```shell
vim /usr/local/soft/tomcat-9.0.22/conf/tomcat-users.xml
```

找到文件在末尾，在</tomcat-users>上一行插入如下配置：

```properties
  <role rolename="manager-gui"/>
  <user username="admin" password="admin1234" roles="manager-gui"/>
```

保存文件，退出。重启tomcat该配置即可生效（“server status”、“Host manager”配置类似，参照报错页面的提示信息进行配置即可）

同时如果是tomcat 8 以上版本还需要更改

```shell
vim /usr/local/soft/tomcat-9.0.22/conf/Catalina/localhost/manager.xml
```

在其中加入

```properties
<Context privileged="true" antiResourceLocking="false"
         docBase="${catalina.home}/webapps/manager">
    <Valve className="org.apache.catalina.valves.RemoteAddrValve" allow="^.*$" />
</Context>
```

无须重启tomcat即可生效

### 2. maven 安装

ps：（新版不需要，但还是记录一下）

解压

```shell
tar zxvf apache-maven-3.6.1-bin.tar.gz
```

配置环境变量

```shell
vim /etc/profile
```

添加以下信息

```properties
export M2_HOME=/opt/apache-maven-3.6.1
export PATH=$PATH:$M2_HOME/bin
```

让配置生效

```shell
source /etc/profile
```

验证是否安装完成

```shell
mvn -version
```

### 3. Dubbo 编译

从https://github.com/apache/dubbo-ops下载最新dubbo，用idea打成jar包上传到/usr/local/soft/springboot.jar

然后先启动zookeeper，再启动 Spring boot jar 包

```shell
java -jar dubbo-admin-0.1.jar
# 后台启动
java -jar test.jar &
nohup java -jar dubbo-admin-0.1.jar &
# 后台指定路径日志输出
nohup java -jar dubbo-admin-0.1.jar > ./Logs/dubbo-admin.log &
```

## 四. 防火墙配置

### 1.查看当前iptables(防火墙)规则

```shell
iptables -L -n
```

### 2. 添加指定端口到防火墙中

```shell
#iptables -I INPUT -p udp --dport 161 -j ACCEPT
iptables -I INPUT -p tcp --dport 8080 -j ACCEPT
iptables -I INPUT -p tcp --dport 6379 -j ACCEPT
iptables -I INPUT -p tcp --dport 2181 -j ACCEPT
iptables -I INPUT -p tcp --dport 2182 -j ACCEPT
```

### 3. 永久性生效



```shell
# 开启：
chkconfig iptables on
# 关闭：
chkconfig iptables off
```

### 4. 即时生效，重启后失效

```shell
# 开启：
service iptables start
# 关闭：
service iptables stop
```

### 5. 查看端口占用

```shell
netstat -nap|grep 8080
ps -ef|grep tomcat|grep -v grep
```

###  6. maven打包

```shell
mvn clean package
```