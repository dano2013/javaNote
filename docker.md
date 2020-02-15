

### 1、简介

Docker是一个开源的应用容器引擎；是一个轻量级容器技术；

Docker支持将软件编译成一个镜像；然后在镜像中各种软件做好配置，将镜像发布出去，其他使用者可以直接使用这个镜像；

运行中的这个镜像称为容器，容器启动是非常快速的。

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\9a5adb1fb096.png" alt="img" style="zoom:67%;" />

### 2、核心概念

docker主机(Host)：安装了Docker程序的机器（Docker直接安装在操作系统之上）；

docker客户端(Client)：连接docker主机进行操作；

docker仓库(Registry)：用来保存各种打包好的软件镜像；

docker镜像(Images)：软件打包好的镜像；放在docker仓库中；

docker容器(Container)：镜像启动后的实例称为一个容器；容器是独立运行的一个或一组应用

<img src="C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\b5a858bff334.png" alt="img" style="zoom:70%;" />

使用Docker的步骤：

1）、安装Docker

2）、去Docker仓库找到这个软件对应的镜像；

3）、使用Docker运行这个镜像，这个镜像就会生成一个Docker容器；

4）、对容器的启动停止就是对软件的启动停止；

### 3、在linux虚拟机上安装docker

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

### 4、Docker常用命令&操作

#### 1）、镜像操作

| **操作** | **命令**                                     | **说明**                                                |
| -------- | -------------------------------------------- | ------------------------------------------------------- |
| 检索     | docker search 关键字 eg：docker search redis | 我们经常去docker hub上检索镜像的详细信息，如镜像的TAG。 |
| 拉取     | docker pull 镜像名:tag                       | :tag是可选的，tag表示标签，多为软件的版本，默认是latest |
| 列表     | docker images                                | 查看所有本地镜像                                        |
| 删除     | docker rmi image-id                          | 删除指定的本地镜像                                      |

https://hub.docker.com/

#### 2）、容器操作

| 操作             | 命令                                        |
| ---------------- | ------------------------------------------- |
| 搜索镜像         | docker search tomcat                        |
| 拉取镜像         | docker pull tomcat                          |
| 根据镜像启动容器 | docker run --name mytomcat -d tomcat:latest |
| 查看所有的容器   | docker ps -a                                |
| 查看运行中的容器 | docker ps                                   |
| 停止运行中的容器 | docker stop 容器的id                        |
| 启动容器         | docker start 容器id                         |
| 进入容器         | docker exec -it 容器ID /bin/bash            |
| 退出容器         | exit 或者 Ctrl+P+Q                          |
| 重启容器         | docker restart 容器ID                       |
| 删除一个容器     | docker rm 容器ID                            |
| 查看容器的日志   | docker logs container-name/container-id     |

更多命令参看
https://docs.docker.com/engine/reference/commandline/docker/
可以参考每一个镜像的文档

### 5、修改mysql密码加密方式

错误截图：

![img](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\0a1be6ca137d.png)

原因：mysql 8.0 默认使用 caching_sha2_password 身份验证机制；客户端不支持新的加密方式。

解决方案：

修改用户（root）的加密方式

步骤：

1、进入mysql容器内部

```
[root@localhost ~]# docker exec -it mysql01 bash   ## mysql01是容器的别名，这里也可以用容器的id代替
```

2、登录mysql

```
root@e285125c99d6:/# mysql -u root -p
Enter password: 
Welcome to the MySQL monitor.  Commands end with ; or \g.
Your MySQL connection id is 11
Server version: 8.0.13 MySQL Community Server - GPL

Copyright (c) 2000, 2018, Oracle and/or its affiliates. All rights reserved.

Oracle is a registered trademark of Oracle Corporation and/or its
affiliates. Other names may be trademarks of their respective
owners.

Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
```

3、设置用户配置项

1）、查看用户信息

```
mysql> select host,user,plugin,authentication_string from mysql.user; 
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| host      | user             | plugin                | authentication_string                                                  |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| %         | root             | caching_sha2_password | $A$005$HF7;krfwhkKHp5fPenQm4J2dm/RJtbbyjtCUVdDCcboXQw3ALxsif/sS1 |
| localhost | mysql.infoschema | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.session    | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.sys        | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | root             | mysql_native_password | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9                              |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
```

备注：host为 % 表示不限制ip，localhost表示本机使用；plugin非 mysql_native_password 则需要修改密码

2）、修改加密方式

```
ALTER USER 'root'@'%' IDENTIFIED WITH mysql_native_password BY '123456';  ### 123456 mysql的登录密码
flush privileges;
```

然后再查看用户信息

```
mysql> select host,user,plugin,authentication_string from mysql.user;
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| host      | user             | plugin                | authentication_string                                                  |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
| %         | root             | mysql_native_password | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9                              |
| localhost | mysql.infoschema | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.session    | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | mysql.sys        | caching_sha2_password | $A$005$THISISACOMBINATIONOFINVALIDSALTANDPASSWORDTHATMUSTNEVERBRBEUSED |
| localhost | root             | mysql_native_password | *6BB4837EB74329105EE4568DDA7DC67ED2CA2AD9                              |
+-----------+------------------+-----------------------+------------------------------------------------------------------------+
5 rows in set (0.00 sec)
```

4、测试：连接成功

![img](C:\Users\WANG\AppData\Roaming\Typora\typora-user-images\f15dcf872943.png)