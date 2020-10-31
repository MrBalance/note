# 使用Docker容器运行nginx并反向代理

## 1. 获取nginx镜像

```shell
docker pull nginx
```

## 2. 运行nginx容器

```shell
docker run --name=nginx -d -p 4030:80 nginx
```

上面命令解释如下：

1. --name：设置容器的名称。
2. -d：表示在后台运行容器。
3. -p：指定端口映射。4030是宿主机的端口，80是Nginx容器内部的端口。
4. nginx：表示根据nginx镜像运行容器。

### 3. 安装Docker Compose

安装 Docker Compose 可以通过下面命令自动下载适应版本的 Compose，并为安装脚本添加执行权限

```shell
sudo curl -L https://github.com/docker/compose/releases/download/1.21.2/docker-compose-$(uname -s)-$(uname -m) -o /usr/local/bin/docker-compose
sudo chmod +x /usr/local/bin/docker-compose
```

