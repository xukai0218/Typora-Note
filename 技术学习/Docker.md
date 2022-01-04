# 

# 一 Docker

## 1 . 简介

**Docker**是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。



特性				容器					虚拟机
启动				秒级					分钟级
硬盘使用				一般为 MB				一般为 GB
性能				接近原生					弱于
系统支持量			单机支持上千个容器		一般几十个



## 2. 核心概念

docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

docker客户端(Client)：连接docker主机进行操作；

docker仓库(Registry)：用来保存各种打包好的软件镜像；

docker镜像(Images)：软件打包好的镜像；放在docker仓库中；

docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用



## 3. 使用Docker的步骤

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；



# 二、Docker常用命令

##   1）镜像操作

| 操作 | 命令                                            | 说明                                                     |
| ---- | ----------------------------------------------- | -------------------------------------------------------- |
| 检索 | docker  search 关键字  eg：docker  search redis | 我们经常去docker  hub上检索镜像的详细信息，如镜像的TAG。 |
| 拉取 | docker pull 镜像名:tag                          | :tag是可选的，tag表示标签，多为软件的版本，默认是latest  |
| 列表 | docker images                                   | 查看所有本地镜像                                         |
| 删除 | docker rmi image-id                             | 删除指定的本地镜像                                       |
| 日志 | docker logs -f 50f40bb240be(id/name)            | 查看容器日志                                             |

````
redis.log(redis.LOG_DEBUG, 1)
redis.log(redis.LOG_VERBOSE, 2)
redis.log(redis.LOG_NOTICE, 3)
redis.log(redis.LOG_WARNING, 4)
````



https://hub.docker.com/

## 2）容器操作命令

| 操作                   | 命令                                       |
| -------------------- | ---------------------------------------- |
| 1、搜索镜像               | docker search tomcat                     |
| 2、拉取镜像               | docker pull tomcat                       |
| 3、根据镜像启动容器           | docker run --name mytomcat -d tomcat:latest |
| 4、查看运行中的容器           | docker ps                                |
| 5、查看所有的容器            | docker ps -a                             |
| 6、 停止运行中的容器          | docker stop  容器的id                       |
| 7、启动容器               | docker start 容器id                        |
| 8、删除一个容器             | docker rm 容器id                           |
| 9、查看容器的日志            | docker logs container-name/container-id  |
|                      | docker logs -f   实时更新                    |
|                      |                                          |
| 10、启动一个做了端口映射的tomcat | docker run -d -p 8888:8080 tomcat        |
|                      | -d：后台运行                                  |
|                      | -p: 将主机的端口映射到容器的一个端口    主机端口:容器内部的端口     |
| 进入容器                 | docker exec -it 容器id bash                |
| 退出                   | Exit                                     |
| 查询容器信息               | docker inspect 容器ID                      |



软件镜像（QQ安装程序）----运行镜像----产生一个容器（正在运行的软件，运行的QQ）；

步骤：

```
10、为了演示简单关闭了linux的防火墙
service firewalld status ；查看防火墙状态
service firewalld stop：关闭防火墙

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

```

## 3) docker 容器开机自动启动

```
docker run --restart=always
docker update --restart=always 容器名字
no -  容器退出时，不重启容器；

on-failure - 只有在非0状态退出时才从新启动容器；

always - 无论退出状态是如何，都重启容器
```



```
1、检查内核版本，必须是3.10及以上
uname -r
2、安装docker
yum install docker
3、输入y确认安装
4、启动docker
[root@localhost ~]# systemctl start docker
[root@localhost ~]# docker -v
Docker version 1.12.6, build 3e8e77d/1.12.6
5、开机启动docker
[root@localhost ~]# systemctl enable docker
Created symlink from /etc/systemd/system/multi-user.target.wants/docker.service to /usr/lib/systemd/system/docker.service.
6、停止docker
systemctl stop docker
```



```
Docker 命令：
docker version
docker images
docker ps -a
docker rm xxx
docker rmi xxx
docker stop xxx
docker kill xxx
docker run -it --restart=always

systemctl enable docker
systemctl status docker
service docker start
service docker restart
service docker stop
```

# 三 Docker安装

## 1、docker 离线安装

[*docker安装包下载地址*](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/)

需要下载的docker安装包：

[[docker-ce-cli-18.09.6-3.el7.x86_64.rpm](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-cli-18.09.6-3.el7.x86_64.rpm)

[docker-ce-18.09.6-3.el7.x86_64.rpm](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/docker-ce-18.09.6-3.el7.x86_64.rpm)

[containerd.io-1.2.5-3.1.el7.x86_64.rpm](https://download.docker.com/linux/centos/7/x86_64/stable/Packages/containerd.io-1.2.5-3.1.el7.x86_64.rpm)

需要依赖的包：

[container-selinux-2.9-4.el7.noarch.rpm](ftp.pbone.net/mirror/ftp.scientificlinux.org/linux/scientific/7x/external_products/extras/x86_64/container-selinux-2.9-4.el7.noarch.rpm)



```
第一步, 卸载残留
sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \

安装命令：
yum install -y yum-utils device-mapper-persistent-data lvm2 libseccomp
yum install -y policycoreutils-python

rpm -ivh docker-ce-cli-18.09.6-3.el7.x86_64.rpm
rpm -ivh containerd.io-1.2.5-3.1.el7.x86_64.rpm
rpm -ivh container-selinux-2.9-4.el7.noarch.rpm
rpm -ivh docker-ce-18.09.6-3.el7.x86_64.rpm                 
```

2、镜像加速

在 /etc/docker/daemon.json 中写入如下内容（如果文件不存在请新建该文件）**

```
{
  "registry-mirrors": [
    "https://dockerhub.azk8s.cn",
    "https://reg-mirror.qiniu.com"
  ]
}

#注意，一定要保证该文件符合 json 规范，否则 Docker 将不能启动
```

3、启动服务

```
systemctl daemon-reload
systemctl restart docker
systemctl enable docker
```



## 2、docker nexus3安装

https://www.jianshu.com/p/8b927b9cd5c0

#### 1.下载nexus3的镜像

```
$ docker pull sonatype/nexus3
```

#### 2.使用镜像启动一个容器

```
$ docker run -d -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus3 --privileged=true -v /home/nexus/nexus-data:/nexus-data --restart=always sonatype/nexus3

docker run -d -t --privileged=true -p 8081:8081 -p 8082:8082 -p 8083:8083 --name nexus3 --privileged=true -v /usr/local/nexus/nexus-data:/nexus-data --restart=always sonatype/nexus3

* 注 没有权限 chmod 777 nexus-data
```

- 8082端口是用于host镜像仓库的服务端口
- 8083端口用户group镜像仓库的服务端口
- 8081 端口是nexus的服务端口
- --name nexus3
- -v /home/nexus/nexus-data:/nexus-data 将容器内nexus-data数据文件夹挂载到宿主机/home/nexus/nexus-data目录下
- sonatype/nexus3 镜像名，如果后面不加版本号，则默认启动latest版本

这里单独说一下 --restart=always：
意思是不管退出状态码是什么始终重启容器。当指定always时，docker daemon将无限次数地重启容器。容器也会在daemon启动时尝试重启，不管容器当时的状态如何。

- no :容器退出时不要自动重启。这个是默认值。
- on-failure[:max-retries] : 只在容器以非0状态码退出时重启。可选的，可以退出docker daemon尝试重启容器的次数。
- always :不管退出状态码是什么始终重启容器。当指定always时，docker daemon将无限次数地重启容器。容器也会在daemon启动时尝试重启，不管容器当时的状态如何。
- unless-[stopped](https://www.centos.bz/tag/stopped/) :不管退出状态码是什么始终重启容器，不过当daemon启动时，如果容器之前已经为停止状态，不要尝试启动它。

**启动之后我们就可以通过http://服务器IP:8081访问。默认账号密码为admin/admin123**

docker inspect 容器ID 查看详细信息

3.17版本密码改成随即的了，而且登录时候提示密码在/nexus-data/admin.password里

进入容器里面

`docker exec -it 容器id bash`

## 3、安装MySQL示例

```shell
docker pull mysql
```



错误的启动

```shell
[root@localhost ~]# docker run --name mysql01 -d mysql
42f09819908bb72dd99ae19e792e0a5d03c48638421fa64cce5f8ba0f40f5846

mysql退出了
[root@localhost ~]# docker ps -a
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS                           PORTS               NAMES
42f09819908b        mysql               "docker-entrypoint.sh"   34 seconds ago      Exited (1) 33 seconds ago                            mysql01
538bde63e500        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       compassionate_
goldstine
c4f1ac60b3fc        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       lonely_fermi
81ec743a5271        tomcat              "catalina.sh run"        About an hour ago   Exited (143) About an hour ago                       sick_ramanujan


//错误日志
[root@localhost ~]# docker logs 42f09819908b
error: database is uninitialized and password option is not specified 
  You need to specify one of MYSQL_ROOT_PASSWORD, MYSQL_ALLOW_EMPTY_PASSWORD and MYSQL_RANDOM_ROOT_PASSWORD；这个三个参数必须指定一个
```

正确的启动

```shell
[root@localhost ~]# docker run --name mysql01 -e MYSQL_ROOT_PASSWORD=123456 -d mysql
b874c56bec49fb43024b3805ab51e9097da779f2f572c22c695305dedd684c5f
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
b874c56bec49        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 3 seconds        3306/tcp            mysql01
```

做了端口映射

```shell
[root@localhost ~]# docker run -p 3306:3306 --name mysql -e MYSQL_ROOT_PASSWORD=mysql -d mysql
ad10e4bc5c6a0f61cbad43898de71d366117d120e39db651844c0e73863b9434
[root@localhost ~]# docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS                    NAMES
ad10e4bc5c6a        mysql               "docker-entrypoint.sh"   4 seconds ago       Up 2 seconds        0.0.0.0:3306->3306/tcp   mysql02
```

几个其他的级操作

```
docker run --name mysql -v /conf/mysql:/etc/mysql/conf.d -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag
把主机的/conf/mysql文件夹挂载到 mysqldocker容器的/etc/mysql/conf.d文件夹里面
改mysql的配置文件就只需要把mysql配置文件放在自定义的文件夹下（/conf/mysql）

docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag --character-set-server=utf8mb4 --collation-server=utf8mb4_unicode_ci
指定mysql的一些配置参数
```

## 5 安装 redis

```
docker pull redis:lates
docker run -itd --name redis-test -p 6379:6379 redis

参数说明：

--name：后面的是容器的名字；


报错：ERROR 2059 (HY000): Authentication plugin '' cannot be loaded: ÕÒ²»µ½Ö¸¶¨µÄÄ£¿é¡£

```
解决方案：
1.进入mysql容器
docker exec -it mysql bash

2.进入mysql
mysql -uroot -p
123456

3.修改密码
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '1qwe32!QWE#@';
```

Mac

```
docker run --name mysql5.7 -p 3306:3306 -e MYSQL_ROOT_PASSWORD=mysql -d mysql/mysql-server

这时使用宿主机连接没有授权访问，需要进入mysql修改mysql访问权限。
docker exec -it mysql5.7 bash

mysql -u root -p 

#授权
CREATE USER 'root'@'%' IDENTIFIED BY 'root';

GRANT ALL ON *.* TO 'root'@'%';

#刷新权限

flush privileges;

#修改root用户密码

mysql> ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY 'mysql';

#刷新权限

mysql> flush privileges;

---------------------------------------------------
Host XXX is not allowed to connect to this MySQL server(这是由于Mysql配置了不支持远程连接引起的)

给 root用户  配置通配符
https://www.jianshu.com/p/eb3d9129d880
```





## 4、安装 RabbitMq

```
docker search rabbitMq
docker pull rabbitmq
docker images 查看所有镜像

docker run --restart=always -d --hostname my-rabbit --name rabbit -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq


docker run  -d --hostname my-rabbit  --name rabbit -e RABBITMQ_DEFAULT_USER=guest -e RABBITMQ_DEFAULT_PASS=guest -p 15672:15672 -p 5672:5672 -p 25672:25672 -p 61613:61613 -p 1883:1883 rabbitmq

docker update --restart=always 容器名字

docker pull rabbitmq:management
docker run -d --name=rabbitmq -p 5671:5671 -p 5672:5672 -p 4369:4369 -p 15671:15671 -p 15672:15672 -p 25672:25672 rabbitmq:management


```

### 问题 启动打不开 网址

```
查看docker容器名称
再进入docker rabbitmq 容器中:

docker ps
docker exec -it 容器名称 sh
rabbitmq-plugins enable rabbitmq_management
guest/guest
exit

https://www.cnblogs.com/yuebo/p/11732769.html
```

## 5、安装  monggo DB

```
docker search mongo
docker pull mongo:latest
docker images

docker run -itd --name mongo -p 27017:27017 mongo --auth

```

```
$ docker exec -it mongo mongo admin
# 创建一个名为 admin，密码为 123456 的用户。
>  db.createUser({ user:'admin',pwd:'123456',roles:[ { role:'userAdminAnyDatabase', db: 'admin'},"readWriteAnyDatabase"]});
# 尝试使用上面创建的用户信息进行连接。
> db.auth('admin', '123456')
```

-p 6379:6379：映射容器服务的 6379 端口到宿主机的 6379 端口。外部可以直接通过宿主机ip:6379 访问到 Redis 的服务；

```



## 6 安装nacos

官网

https://nacos.io/en-us/docs/quick-start-docker.html

https://www.jianshu.com/p/f01efe061056

```
docker pull nacos/nacos-server:latest

mkdir -p ~/Users/xukai/dev_soft/nacos_docker/nacos01/standalone-logs ~/Users/xukai/dev_soft/nacos_docker/nacos01/init.d 
~/Users/xukai/dev_soft/nacos_docker/nacos01/conf
mkdir -p ~/Users/xukai/dev_soft/nacos_docker/nacos01/init.d 
~/Users/xukai/dev_soft/nacos_docker/nacos01/conf

```




















# 参考链接

https://cloud.tencent.com/developer/article/1478476