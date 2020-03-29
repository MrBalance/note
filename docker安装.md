# docker安装

## 1. 卸载旧版本

较旧的 Docker 版本称为 docker 或 docker-engine 。如果已安装这些程序，请卸载它们以及相关的依赖项。

```shell
$ sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
```

## 2. 安装 Docker Engine-Community

### 使用 Docker 仓库进行安装

在新主机上首次安装 Docker Engine-Community 之前，需要设置 Docker 仓库。之后，您可以从仓库安装和更新 Docker。

**设置仓库**

安装所需的软件包。yum-utils 提供了 yum-config-manager ，并且 device mapper 存储驱动程序需要 device-mapper-persistent-data 和 lvm2。

```shell
$ sudo yum install -y yum-utils \
  device-mapper-persistent-data \
  lvm2
```

使用以下命令来设置稳定的仓库。

```shell
$ sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo
```

### 安装 Docker Engine-Community

安装最新版本的 Docker Engine-Community 和 containerd，或者转到下一步安装特定版本：

```shell
$ sudo yum install docker-ce docker-ce-cli containerd.io
```

如果提示您接受 GPG 密钥，请选是。

**有多个 Docker 仓库吗？**

<u>如果启用了多个 Docker 仓库，则在未在 yum install 或 yum update 命令中指定版本的情况下，进行的安装或更新将始终安装最高版本，这可能不适合您的稳定性需求。</u>

Docker 安装完默认未启动。并且已经创建好 docker 用户组，但该用户组下没有用户。

**要安装特定版本的 Docker Engine-Community，请在存储库中列出可用版本，然后选择并安装：**

1. 列出并排序您存储库中可用的版本。此示例按版本号（从高到低）对结果进行排序。

   ```shell
   $ yum list docker-ce --showduplicates | sort -r
   
   docker-ce.x86_64  3:18.09.1-3.el7                     docker-ce-stable
   docker-ce.x86_64  3:18.09.0-3.el7                     docker-ce-stable
   docker-ce.x86_64  18.06.1.ce-3.el7                    docker-ce-stable
   docker-ce.x86_64  18.06.0.ce-3.el7                    docker-ce-stable
   ```

2. 通过其完整的软件包名称安装特定版本，该软件包名称是软件包名称（docker-ce）加上版本字符串（第二列），从第一个冒号（:）一直到第一个连字符，并用连字符（-）分隔。例如：docker-ce-18.09.1。

   ```shell
   $ sudo yum install docker-ce-<VERSION_STRING> docker-ce-cli-<VERSION_STRING> containerd.io
   ```

   比如：

   ```shell
   sudo yum install docker-ce-3:19.03.7-3.el7 docker-ce-cli-3:19.03.7-3.el7 containerd.io
   ```

   启动 Docker。

   ```shell
   $ sudo systemctl start docker
   ```

   通过运行 hello-world 映像来验证是否正确安装了 Docker Engine-Community 。

   ```shell
   $ sudo docker run hello-world
   ```

   ## 3. docker 安装完成后测试hello-world出现问题

   安装docker之后，测试hello-world镜像，终端卡在`Unable to find image 'hello-world:latest' locally`位置

   ![20190403103019494](img\docker\20190403103019494.png)docker在本地没有找到hello-world镜像，也没有从docker仓库中拉取镜像，出项这个问题的原因：是应为docker服务器再国外，我们在国内
   无法正常拉取镜像，所以就需要我们为docker设置国内阿里云的镜像加速器；
   需要修改配置文件 `/etc/docker/daemon.json`  如下

   ```json
   { 
   "registry-mirrors": ["https://alzgoonw.mirror.aliyuncs.com"] 
   }
   ```

   配置文件daemmon,json添加阿里云镜像地址，保存并退出，并且重启docker服务（`systemctl restart docker`）
   docker重启之后，就可以正常拉取helloworld镜像了
   ![2019040310561330](img\docker\2019040310561330.png)

