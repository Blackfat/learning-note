#### centos安装docker

- yum update 更新yum软件包
- yum -y install docker 自动选择y，全自动安装docker
- systemctl start docker  启动docker服务
- systemctl enable docker 设置开机自启动

#### 查看docker信息

* docker version  查看docker版本信息
* docker info 查看docker系统信息

#### docke镜像操作

* docker search image_name 检索image
* docker pull image_name 下载image
* docker images 查看镜像列表
* docker rmi image_name 删除image

#### 启动容器

* docker run -p 随机分配端口号

* docker run -p 8080:80 主机所有网络的8080端口绑定到容器的80端口

* docker run -i   以交互模式运行容器，通常与 -t 同时使用

* docker run -d  后台运行容器，并返回容器ID

* docker run -v <宿主机目录>:<容器目录>  (容器目录不可以为相对路径

  ,宿主机目录如果不存在，则会自动生成)

#### 查看容器

* docker ps 查看正在运行的容器
* docker ps -a 查看所有的容器
* docker ps -l 查看最近一次启动的容器

#### 容器操作

* docker rm  NAME/ID 删除容器
* docker start NAME/ID 启动容器
* docker stop NAME/ID  停止容器
* docker kill NAME/ID 杀死容器
* docker logs NAME/ID  从容器中取出日志
* docker diff NAME/ID 列出一个容器里面改变的文件或目录
* docker cp NAME/ID: 容器路径 本地路径 拷贝容器路径到本地路径
* docker restart NAME/ID 重启正在运行的容器
* docker exec -it NAME/ID /bin/bash 进入已经启动容器


#### docker部署nginx

- docker pull nginx:1.11.10
- docker run --name webserver -d -p 80:80 nginx:1.11.10


#### docker部署redis

- docker pull redis:3.2.7
- docker run -d --name redis_master -p 7001:6379 redis:3.2.7
- docker inspect redis_master | grep IPA
- docker run -d --name redis_slave -p 7002:6379 redis:3.2.7 --slaveof 172.17.0.3 6379
- docker exec -it redis_master /bin/bash
- docker exec -it redis_slave /bin/bash


#### docker部署activemq

- docker run -d --name activemq-master -p 61616:61616  -p 8161:8161

####docker部署rabbitmq

- docker pull rabbitmq:3.6.9-management
- docker run -d --name rabbitmq --publish 5671:5671  --publish 5672:5672 --publish 4369:4369 --publish 25672:25672 --publish 15671:15671 --publish 15672:15672 rabbitmq:3.6.9-management










