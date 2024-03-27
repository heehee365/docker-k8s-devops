删除所有容器
```
# docker rm $(docker ps -aq)
```

过滤出处于退出状态的容器
```
# docker ps -aq -f "status=exited"
```

删除所有退出状态的容器
```
# docker rm $(docker ps -aq -f "status=exited")
```

---

### Dockerfile语法

制作base images
```
FROM scratch
```

使用base image
```
FROM centos
```

```
出于安全考虑，尽量使用官方的image作为base image
```

LABEL定义元数据
```
LABEL maintainer="329640305@qq.com"
LABEL version="1.0"
LABEL description="This is description"
```

WORKDIR指定当前目录
```
WORKDIR /test #如果没有会自动创建test目录
WORKDIR demo
RUN pwd #输出结果应该是/test/demo

用WORKDIR，不要用RUN cd
尽量使用绝对目录
```

COPY/ADD复制文件
```
- 大部分情况COPY优于ADD
- ADD除了COPY功能外，还可以解压
```

ENV设置环境变量
```
尽量使用ENV增加可维护性
```

VOLUME数据持久化
```
- Data Volume，需要在Dockerfile里面指定VOLUME路径
- docker run -v mysql:/var/lib/mysql
- Bind Mounting
- docker run -v /var/lib/mysql:/var/lib/mysql
```

CMD
```
- 容器启动时默认执行的命令
- 如果docker run指定了其他命令，CMD的命令会被忽略掉
- 如果定义了多个CMD，只有最后一个会执行
```
```
FROM centos
ENV name Docker
CMD echo "hello $name"
```
```
# docker run [image] 这里会直接输出hello Docker
# docker run -it [image] /bin/bash 这里会忽略掉CMD而执行bash
```

ENTRYPOINT
```
- 让容器以应用程序或服务的形式运行
- 不会被忽略，一定会执行
```

---

测试link，这种连接方式实际环境中很少使用
```
# docker run -d --name test1 busybox /bin/sh -c "while true; do sleep 3600; done"
# docker run -d --name test2 --link test1 busybox /bin/sh -c "while true; do sleep 3600; done"
# docker exec -it test2 sh
这时候，在test2上能ping通test1主机名，而在test1上不能ping同test2的主机名。这种方式会把test1的主机名解析写到test2的hosts文件里面
# ping test1
# cat /etc/hosts
172.17.0.2  test1 3a7fb3edd5bb
172.17.0.3  1b6e287d5698
```

自建bridge
```
# docker network create -d bridge my-bridge

新建一个容器使用自建的bridge
# docker run -d --name test3 --network my-bridge busybox /bin/sh -c "while true; do sleep 3600; done"

将已经存在的容器test2连接到自建的bridge
# docker network connect my-bridge test2

这时候，test2将有两个IP地址。而且两个容器上互相能ping通对方的主机名，这种方式不会写hosts文件
# docker exec -it test2 sh
# ip a
```

---

Kubernetes的Service
```
- 可以使用kubectl expose创建
- ClusterIP
- NodePort
- LoadBalancer
- 也可以使用DNS
```

## 关于翻墙
本课程里部分link可能需要翻墙，大家如果自己没有VPN的话，可以尝试蓝灯

## 本课程的一些常用链接汇总

Vagrant下载 https://www.vagrantup.com/downloads.html
Virtualbox 5.1下载 https://www.virtualbox.org/wiki/Download_Old_Builds_5_1
Docker国内源安装 https://get.daocloud.io/#install-docker
Docker官方网站 https://www.docker.com/
Docker官方文档中心 https://docs.docker.com/
DockerHub https://hub.docker.com/
