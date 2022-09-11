# Docker

**容器就是将软件打包成标准化单元，以用于开发、交付和部署。**

- **容器镜像是轻量的、可执行的独立软件包** ，包含软件运行所需的所有内容：代码、运行时环境、系统工具、系统库和设置。
- **容器化软件适用于基于 Linux 和 Windows 的应用，在任何环境中都能够始终如一地运行。**
- **容器赋予了软件独立性**，使其免受外在环境差异（例如，开发和预演环境的差异）的影响，从而有助于减少团队间在相同基础设施上运行不同软件时的冲突。

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220831175458959.png" alt="image-20220831175458959" style="zoom:80%;" />

Docker **容器虚拟化**技术为基础的软件,虚拟化技术是一种资源管理技术，是将计算机的各种[实体资源](https://zh.wikipedia.org/wiki/資源_(計算機科學))（[CPU](https://zh.wikipedia.org/wiki/CPU)、[内存](https://zh.wikipedia.org/wiki/内存)、[磁盘空间](https://zh.wikipedia.org/wiki/磁盘空间)、[网络适配器](https://zh.wikipedia.org/wiki/網路適配器)等），予以抽象、转换后呈现出来并可供分割、组合为一个或多个电脑配置环境。由此，打破实体结构间的不可切割的障碍，使用户可以比原本的配置更好的方式来应用这些电脑硬件资源。这些资源的新虚拟部分是不受现有资源的架设方式，地域或物理配置所限制。一般所指的虚拟化资源包括计算能力和数据存储。

**Docker 的基本组成**

```
Docker 中有非常重要的三个基本概念:
镜像（Image）
容器（Container）
仓库（Repository）
```



### 安装

Docker引擎安装：https://docs.docker.com/engine/install/ubuntu/

卸载旧版本

```
sudo apt-get remove docker docker-engine docker.io containerd runc
```



1. 更新`apt`包索引并安装包以允许`apt`通过 HTTPS 使用存储库：

```shell
 sudo apt-get update
 sudo apt-get install \
    ca-certificates \
    curl \
    gnupg \
    lsb-release
```

2. 添加 Docker 的官方 GPG 密钥：

```shell
$ sudo mkdir -p /etc/apt/keyrings
$ curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

3. 使用以下命令设置存储库：

```shell
$ echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

4. 添加阿里云镜像 docker的远程库

```shell
add-apt-repository "deb [arch=amd64] https://mirrors.aliyun.com/docker-ce/linux/ubuntu $(lsb_release -cs) stable"
```

5. 更新源

```shell
sudo apt-get update
```

6. 安装docker-ce

```shell
sudo apt-get install -y docker-ce
```

7. 启动docker

```shell
sudo systemctl status docker
```

8. 运行hello-world

```shell
sudo docker run hello-world
```



**配置腾讯 镜像加速源**

执行以下命令，打开

```
/etc/docker/daemon.json
```

打开配置文件。

```
vim /etc/docker/daemon.json
```

切换至编辑模式，添加以下内容，并保存。

```
{
   "registry-mirrors": [
       "https://mirror.ccs.tencentyun.com"
  ]
}
```

1. 执行以下命令，重启 Docker 即可。

   ```
   sudo systemctl restart docker
   ```



### 卸载

1. 卸载 Docker Engine、CLI、Containerd 和 Docker Compose 软件包：

   ```
   $ sudo apt-get purge docker-ce docker-ce-cli containerd.io docker-compose-plugin
   ```

2. 主机上的映像、容器、卷或自定义配置文件不会自动删除。要删除所有映像、容器和卷：

   ```
   $ sudo rm -rf /var/lib/docker
   $ sudo rm -rf /var/lib/containerd
   ```

您必须手动删除任何已编辑的配置文件。





### 遇到的问题



**测试hello-word时的报错:Unable to find image ‘hello-world:latest‘ locally**

1. 切换国内阿里云镜像，创建daemon.json文件

```shell
vim /etc/docker/daemon.json

```

文件中添加以下内容：

```shell
 { 
 "registry-mirrors": ["https://alzgoonw.mirror.aliyuncs.com"] 
 }
```

重启docker

```shell
systemctl restart docker
systemctl status docker
```

重新进行尝试,可能还会Unable to find image ‘hello-world:latest‘ locally,但此时是因为本地没有该镜像,因此需要拉取到最新的Hello World镜像,等待一会就会拉取成功.在进行尝试便可以成功.

```
sudo docker run hello-world
```

成功输出结果:

```shell
Hello from Docker!
This message shows that your installation appears to be working correctly.

To generate this message, Docker took the following steps:
 1. The Docker client contacted the Docker daemon.
 2. The Docker daemon pulled the "hello-world" image from the Docker Hub.
    (amd64)
 3. The Docker daemon created a new container from that image which runs the
    executable that produces the output you are currently reading.
 4. The Docker daemon streamed that output to the Docker client, which sent it
    to your terminal.

To try something more ambitious, you can run an Ubuntu container with:
 $ docker run -it ubuntu bash

Share images, automate workflows, and more with a free Docker ID:
 https://hub.docker.com/

For more examples and ideas, visit:
 https://docs.docker.com/get-started/

///////////////////
来自码头的您好！
此消息表明您的安装似乎工作正常。

为了生成这条消息，Docker采取了以下步骤：
1.Docker客户端联系Docker守护进程。
2.Docker守护进程从Docker Hub拉取了“Hello-world”镜像。
(AMD64)。
3.Docker守护进程从该映像创建了一个新容器，该容器运行。
生成您当前正在阅读的输出的可执行文件。
4.Docker守护进程将该输出流传输到Docker客户端，由Docker客户端发送。
送到你的终点站。

要尝试更有雄心的东西，您可以运行一个Ubuntu容器，其中包含：
$docker run-it ubuntu bash。

使用免费的Docker ID共享图像、自动化工作流程等：
Https://hub.docker.com/。

有关更多示例和想法，请访问：
Https://docs.docker.com/get-started/
```

### 运行原理

**启动流程**

<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220831180434921.png" alt="image-20220831180434921" style="zoom:67%;" />





<img src="https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220831234558927.png" alt="image-20220831234558927" style="zoom: 50%;" />



**Docker比虚拟机快的原因：**

Docker有着比虚拟机更少的抽像层

Docker利用的是宿主机的内核，vm需要的是guest OS



Docker不需要加载整个操作系统内核，而是直接利用宿主机的操作系统。



![image-20220831234724315](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220831234724315.png)









### 基础操作

命令的帮助文档地址:https://docs.docker.com/engine/reference/commandline/docker/

```shell
docker version          #查看docker的版本信息
docker info             #查看docker的系统信息,包括镜像和容器的数量
docker 命令 --help       #帮助命令(可查看可选的参数)
docker COMMAND --help

```



```shell
systemctl start docker   # 启动Docker
docker version           # 查看当前版本号，是否启动成功
systemctl enable docker  # 设置开机自启动
```

**run 运行docker镜像**

```shell
sudo docker run hello-world
```

 **images 查看 docker 镜像**

```shell
ubuntu@VM-24-7-ubuntu:~/docker$ sudo  docker images
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3kB
```

````
port  	  # 查看映射端口对应的容器内部源端口
pause	  # 暂停容器
ps        # 猎户容器列表
pull      # 从docker镜像源服务器拉取指定镜像或者库镜像
push      # 推送指定镜像或者库镜像至docker源服务器
restart   # 重启运行的容器
rm        # 移除一个或多个容器
rmi       # 移除一个或多个镜像 （无容器使用该镜像才可删除，否则需要删除相关容器才可继续或 -f 强制删除）
run       # 创建一个新的容器并运行一个命令
save      # 保存一个镜像为一个 tar 包【对应 load】
search    # 在 docker hub 中搜索镜像
start     # 启动容器
stop      # 停止容器
tag       # 给源中镜像打标签
top       # 查看容器中运行的进程信息
unpause   # 取消暂停容器
version   # 查看 docker版本号
wait      # 截取容器停止时的退出状态值
````

#### 镜像命令

官方文档：https://docs.docker.com/engine/reference/commandline/images/

docker images ：查看本地主机上的镜像

```shell
ubuntu@VM-24-7-ubuntu:~$ sudo docker images
#镜像仓库源 #镜像的标签  #镜像的ID   #镜像的创建时间     #镜像的大小
REPOSITORY    TAG       IMAGE ID       CREATED         SIZE
hello-world   latest    feb5d9fea6a5   11 months ago   13.3k


#可选项
-a 	#显示所有
-q 	#只显示id
```



docker search 命令用来搜索镜像 

```
sudo docker search mysql
```

```shell
ubuntu@VM-24-7-ubuntu:~$ sudo docker search mysql
#名字				    #描述										   #收藏数 	#官方 	#自动化	
NAME                           DESCRIPTION                                     			      STARS     OFFICIAL   AUTOMATED
mysql                           MySQL is a widely used, open-source relation…  	      13103      [OK]       
mariadb                        MariaDB Server is a high performing open sou…   	   5010        [OK]       
phpmyadmin                 phpMyAdmin - A web interface for MySQL and M…    616          [OK]       
percona                         Percona Server is a fork of the MySQL relati…  		 584          [OK]       
bitnami/mysql               Bitnami MySQL Docker Image                      		76                 	 		  [OK]



#可选项
--filter=STARS=3000 #搜索出来start大于3000的
```

**docker pull 下载镜像**

**指定下载5.7版本的mysql：**

```
docker pull mysql:5.7
```

**不指定版本的情况，自动默认最新版本：**

**分层下载利用了linux的联合文件系统，例如如果下载mysql的其他版本时，只需要下载不同的部分，相同的部分不需要重复下载。**

```shell
ubuntu@VM-24-7-ubuntu:~$ sudo docker pull mysql
Using default tag: latest
latest: Pulling from library/mysql
492d84e496ea: Pull complete  #分层下载：docker image的核心 联合文件系统 
bbe20050901c: Pull complete 
e3a5e171c2f8: Pull complete 
c2cedd8aa061: Pull complete 
d6a485af4cc9: Pull complete 
ee16a57baf60: Pull complete 
64bab9180d2a: Pull complete 
c3aceb7e4f48: Pull complete 
269002e5cf58: Pull complete 
d5abeb1bd18e: Pull complete 
cbd79da5fab6: Pull complete 
Digest: sha256:cdf3b62d78d1bbb1d2bd6716895a84014e00716177cbb7e90f6c6a37a21dc796
Status: Downloaded newer image for mysql:latest
docker.io/library/mysql:latest   #真实地址
```



**docker rmi 删除镜像**

可以通过镜像的ID 或者 镜像的名字name 来进行删除

```shell
sudo docker rmi -f ff3b5098b416 #删除指定容器
sudo docker rmi -f 容器id 容器id 容器id #删除多个容器
sudo docker rmi -f $(docker imagers -aq ) #删除全部的容器
```

 





#### 容器命令

有镜像才能创建容器，下载一个centos镜像来测试

```
docker pull centos
```

新建容器并启动

```shell
docker run [可选参数] image

#参数说明
--name="名字"            指定容器的名字，用来区分容器
-d                     		后台方式运行
-it                    		 使用交互方式运行,进入容器查看内容
-p                     		指定容器的端口（小写）
	(
	 -p ip:主机端口：容器端口 
	 -p 主机端口:容器端口
	 -p 容器端口
	)
-P                 		    随机指定端口(大写P)

```



**run -it 进入centos容器：**

```shell
# docker run -it [容器ID] /bin/bash
sudo docker run -it centos /bin/bash
#-it 表示 以交互模式运行
```

![image-20220901012404679](https://test-1309023885.cos.ap-guangzhou.myqcloud.com/typora/image-20220901012404679.png)

**exit 退出主机**

```shell
#exit 直接停止并退出容器（后台方式运行则仅退出）
#Ctrl + P + Q  不停止容器退出
[root@d65ffbbf5670 /]# exit
exit
```

**ps 列出所有运行的容器**

参数：

不加参数列出正在运行的容器

-a 列出正在运行 + 历史运行的容器

-n=？

```shell
ubuntu@VM-24-7-ubuntu:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
#-a 参数
ubuntu@VM-24-7-ubuntu:~$ sudo docker ps -a
CONTAINER ID   IMAGE         COMMAND       CREATED         STATUS                          PORTS     NAMES
d65ffbbf5670   centos        "/bin/bash"   5 minutes ago   Exited (0) About a minute ago             quizzical_easley
02601338924a   hello-world   "/hello"      9 hours ago     Exited (0) 9 hours ago                    magical_dewdney
f80439277d1e   hello-world   "/hello"      9 hours ago     Exited (0) 9 hours ago                    busy_panini
```

```shell
#-n 参数
ubuntu@VM-24-7-ubuntu:~$ sudo docker ps -n=1
CONTAINER ID   IMAGE     COMMAND       CREATED         STATUS                     PORTS     NAMES
d65ffbbf5670   centos    "/bin/bash"   7 minutes ago   Exited (0) 3 minutes ago             quizzical_easley

```



**删除容器**

```shell
docker rm 容器id                 #删除指定的容器,不能删除正在运行的容器,强制删除使用 rm -f
docker rm -f $(docker ps -aq)   #删除所有的容器
docker ps -a -q|xargs docker rm #删除所有的容器(通过管道)
```

启动和重启容器命令

```shell
docker start 容器id          #启动容器
docker restart 容器id        #重启容器
docker stop 容器id           #停止当前运行的容器
docker kill 容器id           #强制停止当前容器
```





###  其他常用命令

#### 日志的查看

**docker logs -tf 容器id**
**docker logs --tail number 容器id #num为要显示的日志条数**

```bash
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker logs --help

Usage:  docker logs [OPTIONS] CONTAINER

Fetch the logs of a container

Options:
      --details        Show extra details provided to logs
  -f, --follow         Follow log output
      --since string   Show logs since timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)
  -n, --tail string    Number of lines to show from the end of the logs (default "all")
  -t, --timestamps     Show timestamps
      --until string   Show logs before a timestamp (e.g. 2013-01-02T13:23:37Z) or relative (e.g. 42m for 42 minutes)

常用：
docker logs -tf 容器id
docker logs --tail number 容器id #num为要显示的日志条数


#docker容器后台运行，必须要有一个前台的进程，否则会自动停止
#编写shell脚本循环执行，使得centos容器保持运行状态
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker run -d centos /bin/sh -c "while true;do echo hi;sleep 5;done"
c703b5b1911ff84d584390263a35707b6024816e1f46542b61918a6327a570dc
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS     NAMES
c703b5b1911f   centos    "/bin/sh -c 'while t…"   13 seconds ago   Up 10 seconds             pedantic_banach
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker logs -tf --tail 10 c703b5b1911f
2020-12-27T03:34:07.255599560Z hi
2020-12-27T03:34:12.257641517Z hi
2020-12-27T03:34:17.259706294Z hi
2020-12-27T03:34:22.261693707Z hi
2020-12-27T03:34:27.262609289Z hi
2020-12-27T03:34:32.267862677Z hi
2020-12-27T03:34:37.270382873Z hi
2020-12-27T03:34:42.272414182Z hi
2020-12-27T03:34:47.274823243Z hi
2020-12-27T03:34:52.277419274Z hi
12345678910111213141516171819202122232425262728293031323334353637
```

#### 查看容器中进程信息

**docker top**

```bash
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker top c703b5b1911f
UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                11156               11135               0                   11:31               ?                   00:00:00            /bin/sh -c while true;do echo hi;sleep 5;done
root                11886               11156               0                   11:43               ?                   00:00:00            /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 5
1234
```

#### 查看容器的元数据

 **docker inspect**

```bash
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker inspect 容器id
1
```

#### 进入当前正在运行的容器

因为通常我们的容器都是使用后台方式来运行的，有时需要进入容器修改配置

方式一：

**docker exec -it**

docker exec 进入容器后开启一个新的终端，可以在里面操作

```bash
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker exec -it c703b5b1911f /bin/bash
[root@c703b5b1911f /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@c703b5b1911f /]# ps -ef      
UID        PID  PPID  C STIME TTY          TIME CMD
root         1     0  0 03:31 ?        00:00:00 /bin/sh -c while true;do echo hi;sleep 5;done
root       279     0  0 03:54 pts/0    00:00:00 /bin/bash
root       315     1  0 03:56 ?        00:00:00 /usr/bin/coreutils --coreutils-prog-shebang=sleep /usr/bin/sleep 5
root       316   279  0 03:56 pts/0    00:00:00 ps -ef
123456789
```

方式二：

**docker attach**

docker attach 进入容器正在执行的终端，不会启动新的终端

```bash
[root@iZwz99sm8v95sckz8bd2c4Z ~]# docker attach c703b5b1911f
1
```





