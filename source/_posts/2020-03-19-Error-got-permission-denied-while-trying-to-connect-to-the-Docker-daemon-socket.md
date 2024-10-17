---
title: 解决 Docker permission denied while trying to connect to the Docker daemon socket
date: 2020-03-19 10:22:06
categories: [DevOps]
tags: [Docker, Container, Cloud Native]
---

## 问题
当我们在本地运行 Docker 容器，直接使用以下命令非root方式执行时。

``` shell
docker ps
```

如果遇到以下错误：

{% image /assets/images/docker/docker-permission-denied.png %}

```
docker: permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Head "http://%2Fvar%2Frun%2Fdocker.sock/_ping": dial unix /var/run/docker.sock: connect: permission denied.
```

这种"permission denied while trying to connect to the Docker daemon socket"（尝试连接Docker守护程序套接字时权限被拒绝）的错误通常是因为运行Docker命令的用户没有足够的权限来访问Docker守护程序造成的。

解决这个问题其实很简单，只需给当前用户给予权限即可。Docker守护程序套接字通常在/var/run/docker.sock，我们需要给予当前用户读写权限。docker组是docker命令的运行用户组，它包含了运行docker命令所需的所有权限。Docker守护程序默认运行在root权限下，所以我们需要将当前用户加入docker组，并修改docker.sock的权限。

## 解决方法：
Step 1. 创建docker组

``` shell
sudo groupadd docker
```

Step 2. 使用以下命令将当前用户加入docker组

``` shell
sudo usermod -aG docker $USER
```

Step 3. 修改docker.sock的权限

``` shell
sudo chmod 666 /var/run/docker.sock
```

Step 4. 重启Docker服务，可以使用以下命令：

``` shell
sudo systemctl restart docker
```

{% image /assets/images/docker/docker-permission-denied-fixed.png %}

执行完以上步骤后，再次运行`docker ps`命令，就能正常运行了。


