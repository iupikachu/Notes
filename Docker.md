---

typora-copy-images-to: upload
---







## Docker



### 1.准备工作



**环境查看**

```shell
#系统内核 3.10 以上
[root@izbp1alfqlbdka40hbj17rz /]# uname -r
3.10.0-514.26.2.el7.x86_64
```



```shell
#系统版本
[root@izbp1alfqlbdka40hbj17rz /]# cat /etc/os-release
NAME="CentOS Linux"
VERSION="7 (Core)"
ID="centos"
ID_LIKE="rhel fedora"
VERSION_ID="7"
PRETTY_NAME="CentOS Linux 7 (Core)"
ANSI_COLOR="0;31"
CPE_NAME="cpe:/o:centos:centos:7"
HOME_URL="https://www.centos.org/"
BUG_REPORT_URL="https://bugs.centos.org/"

CENTOS_MANTISBT_PROJECT="CentOS-7"
CENTOS_MANTISBT_PROJECT_VERSION="7"
REDHAT_SUPPORT_PRODUCT="centos"
REDHAT_SUPPORT_PRODUCT_VERSION="7"
```



### 2.安装



官方文档: https://docs.docker.com/





```shell
 # 1.卸载旧版本
 sudo yum remove docker \
                  docker-client \
                  docker-client-latest \
                  docker-common \
                  docker-latest \
                  docker-latest-logrotate \
                  docker-logrotate \
                  docker-engine
# 2. 安装包
sudo yum install -y yum-utils

# 3. 设置镜像仓库
sudo yum-config-manager \
    --add-repo \
    https://download.docker.com/linux/centos/docker-ce.repo  ## 默认是从国外下载 很慢！
# 安装aliyun镜像地址    
sudo yum-config-manager --add-repo http://mirrors.aliyun.com/docker-ce/linux/centos/docker-ce.repo

# 更新 yum 软件包索引
yum makecache fast

# 4.安装docker          ce 社区版(推荐)     ee 企业版
sudo yum install docker-ce docker-ce-cli containerd.io

# 选择y 接受GPG 秘钥
```

3.安装aliyun镜像地址执行结果

![image-20210714141318471](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210714141318471.png)



测试安装是否成功

```shell
docker version
```



```shell
systemctl start docker
[root@izbp1alfqlbdka40hbj17rz /]# docker version
Client: Docker Engine - Community
 Version:           20.10.7
 API version:       1.41
 Go version:        go1.13.15
 Git commit:        f0df350
 Built:             Wed Jun  2 11:58:10 2021
 OS/Arch:           linux/amd64
 Context:           default
 Experimental:      true

Server: Docker Engine - Community
 Engine:
  Version:          20.10.7
  API version:      1.41 (minimum version 1.12)
  Go version:       go1.13.15
  Git commit:       b0f5bc3
  Built:            Wed Jun  2 11:56:35 2021
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.4.6
  GitCommit:        d71fcd7d8303cbf684402823e425e9dd2e99285d
 runc:
  Version:          1.0.0-rc95
  GitCommit:        b9ee9c6314599f1b4a7f497e1f1f856fe433d3b7
 docker-init:
  Version:          0.19.0
  GitCommit:        de40ad0
```



测试

```shell
docker run hello-world
docker images
```

 

阿里云镜像加速

1. 登录阿里云找到容器镜像服务
2. 找到镜像加速地址

![image-20210714153615152](/Users/chenqipeng/Library/Application Support/typora-user-images/image-20210714153615152.png)

3. 配置使用

   针对Docker客户端版本大于 1.10.0 的用户

   修改daemon配置文件/etc/docker/daemon.json来使用加速器

   ```shell
   sudo mkdir -p /etc/docker
   sudo tee /etc/docker/daemon.json <<-'EOF'
   {
     "registry-mirrors": ["https://2z670qxs.mirror.aliyuncs.com"]
   }
   EOF
   sudo systemctl daemon-reload
   sudo systemctl restart docker
   ```

   





### 3.**卸载 docker**



```shell
systemctl stop docker
sudo yum remove docker-ce docker-ce-cli containerd.io
sudo rm -rf /var/lib/docker
```





### 4.docker 的常用命令



```shell
docker version  # 显示 docker 的版本信息
docker info     # 显示 docker 系统信息 包括镜像和容器的数量
docker 命令 --help # 万能命令
```

命令文档 : https://docs.docker.com/reference



#### 4.1 镜像命令

```shell
# 查询本地镜像
docker images -a  显示全部 
docker images -q	显示id

# 查询 docker hub 上的镜像
docker search 		搜索镜像 （docker hub）

# 搜索stars在3000以上的镜像
docker search mysql -f=STARS=3000
docker search mysql --filter=STARS=3000


```





#####  下载镜像



```shell
#从docker hub 下载 
docker pull mysql
docker pull docker.io/library/mysql:latest
# 下载指定tag 5.7
docker pull mysql:5.7

```

![image-20210714194858256](https://gitee.com/iupikachu/img/raw/master/img/20210714194903.png)





##### 删除镜像



docker rmi -f 容器id  



**删除多个容器**

docker rmi -f 容器id  容器id  容器id  

**删除全部镜像**
docker rmi -f $(docker images -aq)



```shell

[root@izbp1alfqlbdka40hbj17rz /]# docker rmi -f 09361feeb475
```



#### 4.2 容器命令



##### 容器运行

 容器运行 docker run [option] image

```shell
# 参数说明
--name="Name" 容器名字 tomcat01 tomcat02 区分容器
-d  					后台运行  
-it 					交互方式运行，进入容器，查看内容
-p            指定容器的端口   -p 8080:8080 和主机映射
		-p ip:主机端口:容器端口
		-p 主机端口:容器端口
		-p 容器端口
-P 						随机指定端口
```

 

测试、启动并进入容器



**交互形式**

```shell
[root@izbp1alfqlbdka40hbj17rz /]# docker run  -it centos /bin/bash
```



**后台形式**

```shell
[root@izbp1alfqlbdka40hbj17rz /]# docker run  -d --name nginx01 -p 3344:80
```

宿主机端口 : 3344

docker容器端口 : 80 

##### 列出容器

列出正在运行的容器

```shell
docker ps 
```



列出 当前正在运行的容器+曾经运行的容器

```shell
docker ps -a
```



列出最近运行的一个docker

```shell
docker ps -a -n=1
```



只显示当前运行容器 + 曾经运行容器的编号

```shell
docker ps -aq
```



组合使用

```shell
[root@izbp1alfqlbdka40hbj17rz /]# docker ps -aq -n=2
69440d327d45
90a8ca8fe8f2
```



#####  退出容器

```shell
exit 			# 直接容器停止并退出
Ctrl + P + Q   #容器不停止退出
```





#####  删除容器

```shell
docker rm 容器id    # 删除指定的容器   不能删除正在运行的容器。如果要删除 rm -f
docker rm -f $(docker ps -aq)
```



#####    启动和停止容器



```shell
docker start 容器id       # 启动容器
docker restart 容器id     # 重启容器
docker stop 容器id        # 停止当前正在运行的容器
docker kill 容器id        # 强制停止当前运行的容器
docker stop $(docker ps -a | awk '{ print $1}' | tail -n +2)    # 停止当前所有容器
```



#### 4.3 其他命令



##### **后台启动容器**

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d centos
17becb438b69fbac143b93aaa655ea03e71381f5b4a31f752933b0a7fe49c4b2
[root@izbp1alfqlbdka40hbj17rz ~]# docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
```



后台启动 centos 镜像

docker ps 后，发现centos容器并没有运行

坑 :  docker 容器使用后台运行，就必须要有一个前台进程，docker发现没有应用，就会自动停止。

​		 nginx 启动后，发现自己没有提供服务，就会立刻停止。



##### 查看日志

```shell
docker logs
docker logs -tf --tail 10 容器id  # 输出10条日志 带上时间
```



##### **查看容器中进程信息 top**

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker top 22cdda033c5c
\UID                 PID                 PPID                C                   STIME               TTY                 TIME                CMD
root                3269                3236                0                   21:22               pts/0               00:00:00            /bin/bash
```

UID ：用户id

PID :  进程id

PPID：父进程id



##### **查看镜像的原数据**

```shell
docker inspect 容器id
```



##### **进入当前正在运行的容器**

```shel
docker exec -it 容器id /bin/bash   # 进入容器后开启一个新的终端。可以在里面操作 (常用)
docker attach 容器id               # 进入容器正在执行的终端。不会启动新的进程
```





##### 从容器拷贝文件到主机上



docker cp 容器id:容器内路径 目的主机路径

```shell
[root@izbp1alfqlbdka40hbj17rz home]# docker ps
CONTAINER ID   IMAGE     COMMAND       CREATED          STATUS          PORTS     NAMES
22cdda033c5c   centos    "/bin/bash"   26 minutes ago   Up 26 minutes             zealous_agnesi
[root@izbp1alfqlbdka40hbj17rz home]# docker attach 22cdda033c5c
[root@22cdda033c5c /]# cd /home
[root@22cdda033c5c home]# ls
[root@22cdda033c5c home]# touch test.java
[root@22cdda033c5c home]# ls
test.java
[root@22cdda033c5c home]# exit
exit
[root@izbp1alfqlbdka40hbj17rz home]# docker cp 22cdda033c5c:/home/test.java /home
[root@izbp1alfqlbdka40hbj17rz home]# ls
admin  test.java
```

从容器中拷贝到外部宿主机，需要exit到外部宿主机再执行 cp命令

未来可以使用数据卷的技术打通数据





### 5.部署tomcat



开启aliyun安全组

![image-20210719143200422](https://gitee.com/iupikachu/img/raw/master/img/20210719143206.png)

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker pull tomcat:9.0
[root@izbp1alfqlbdka40hbj17rz ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED        SIZE
tomcat       9.0       36ef696ea43d   2 weeks ago    667MB
centos       latest    300e315adb2f   7 months ago   209MB
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -p 3355:8080 --name tomcat01 tomcat:9.0
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat01 /bin/bash
cp -r  webapps.dist/* webapps
```



访问

> http://47.110.154.185:3355/

![image-20210719143737636](https://gitee.com/iupikachu/img/raw/master/img/20210719143741.png)





### 6.docker 镜像

#### **UnionFS (联合文件系统)**

分层，对文件的修改作为一次提交一层层叠加。同时将不同目录挂载到同一个虚拟文件系统下。

Union文件系统是docker镜像的基础，镜像通过分层继承，基于基础镜像制作各种具体的应用镜像。



特性: 一次同时加载多个文件系统，从外部看只看到一个文件系统，联合加载会把各层文件系统叠加起来，这样最终的文件系统会包含所有底层的文件和目录

![download](https://gitee.com/iupikachu/img/raw/master/img/20210722162926.jpg)

![OIP](https://gitee.com/iupikachu/img/raw/master/img/20210722162902.jpg)



**bootfs** （boot file sysytem）

主要包含 bootloader 和 kernel   bootloader 负责加载kernel,bootfs是docker镜像的最底层，boot加载完内核之后，内核已经在内存中了，此时使用权转交给内核，卸载bootfs。



**rootfs** (root file system)

包含的就是linux系统中的 /dev /proc /bin /etc 等标准目录和文件。rootfs就是各种不同的操作系统发行版，如 ubuntu centos。



#### docker 分层原理



查看容器

```shell
docker image inspect tomcat:9.0
```

![image-20210722164112338](https://gitee.com/iupikachu/img/raw/master/img/20210722164117.png)





![image-20210722170434803](https://gitee.com/iupikachu/img/raw/master/img/20210722170438.png)

docker 镜像都是只读的，当容器启动时，一个新的可写层被加载到镜像的顶部。

这一层就是我们通常说的容器层，容器之下的都叫镜像层。



打包就是把镜像层和自己可以操作的容器层打包成一个新的镜像层。





#### Commit 镜像

```shell
docker commit  提交容器成为一个新的副本
docker commit -m="提交的描述信息" -a="作者" 容器id 目标镜像名:[TAG]
```



```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -it -p 8080:8080 tomcat:9.0
[root@izbp1alfqlbdka40hbj17rz ~]# docker ps
CONTAINER ID   IMAGE        COMMAND             CREATED         STATUS         PORTS                    NAMES
f53f976e1085   tomcat:9.0   "catalina.sh run"   8 minutes ago   Up 8 minutes   0.0.0.0:8080->8080/tcp   tender_jemison

# 以命令行进入容器

[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it f53f976e1085 /bin/bash
root@f53f976e1085:/usr/local/tomcat#

# 把 webapps.dist/下面的文件全部复制到 webapps下
cp -r webapps.dist/* webapps
# 访问 http://47.110.154.185:8080 就会有结果

[root@izbp1alfqlbdka40hbj17rz ~]# exit
[root@izbp1alfqlbdka40hbj17rz ~]# docker ps
CONTAINER ID   IMAGE        COMMAND             CREATED          STATUS          PORTS                    NAMES
1beebaa15c4b   tomcat:9.0   "catalina.sh run"   23 minutes ago   Up 23 minutes   0.0.0.0:8080->8080/tcp   sharp_stonebraker

# commit 镜像
[root@izbp1alfqlbdka40hbj17rz ~]# docker commit -a="cqp" -m="add webapps" 1beebaa15c4b tomcat02:1.0
sha256:97200be88e52cedd916a0536f941fc3395d479e3601c9bf2555829116c64f57e
```





查看镜像，打包成功！

![image-20210723095527069](https://gitee.com/iupikachu/img/raw/master/img/20210723095527.png)







### 7.容器数据卷



#### 什么是容器数据卷？

如果数据都在容器中，那么我们容器删除，数据就会丢失！ **需求: 数据可持久化**

Docker 容器中产生的数据，同步到本地！

这就是卷技术！将我们容器的目录，挂载到linux上面！ 



<img src="https://gitee.com/iupikachu/img/raw/master/img/20210723102140.png" alt="image-20210723102140544" style="zoom:67%;" />

**总结一句话: 容器的持久化和同步操作！容器间也是可以数据共享的！**



####   使用数据卷

> 方式一: 直接使用命令挂载 -v

```shell
docker run -it -v 主机目录:容器内目录
[root@izbp1alfqlbdka40hbj17rz home]# docker run -it -v /home/test:/home centos /bin/bash
```

宿主机的 /home/test 和 容器的 /home 双向绑定 

关闭容器，在宿主机的test里面修改文件，再重启容器，容器home里面的文件也被修改。

**以后，只需要在本地修改即可，容器内会自动同步！**





#### 实战: 安装mysql

mysql的数据持久化：data目录

```shell
#下载mysql镜像
[root@izbp1alfqlbdka40hbj17rz home]# docker pull mysql:5.7

# 官方start
$ docker run --name some-mysql -e MYSQL_ROOT_PASSWORD=my-secret-pw -d mysql:tag

# 自定义start
-d 后台运行
-p 端口映射
-v 卷挂载
-e 环境配置
--name 容器名字
[root@izbp1alfqlbdka40hbj17rz home]#docker run -d -p 3306:3306 -v /home/mysql/conf:/etc/mysql/conf.d -v /home/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7
a6e930e3e23d8a0caa7e0b23851a589a11a5d08513dc0948d0612babc38f25e6
[root@izbp1alfqlbdka40hbj17rz home]#

docker run -d -p 3307:3306 -v /Users/chenqipeng/Volumes/mysql/conf:/etc/mysql/conf.d -v /Users/chenqipeng/Volumes/mysql/data:/var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql mysql:5.7
```

![image-20210723143350557](https://gitee.com/iupikachu/img/raw/master/img/20210723143350.png)

![image-20210723143601513](https://gitee.com/iupikachu/img/raw/master/img/20210723143601.png)



```shell
[root@izbp1alfqlbdka40hbj17rz mysql]# ls
conf  data

####
data映射成功，但是conf配置文件是空的
所以正确的做法应该是先运行容器mysql，进入mysql，找到这个配置文件拷贝出来，放到conf下，再重新-v挂载启动

### 删除mysql容器
[root@izbp1alfqlbdka40hbj17rz mysql]# docker rm -f mysql
### 挂载到本地的数据卷还在，这就实现了容器数据持久化
```



#### 具名和匿名挂载

```shell
# 匿名挂载 -v 只写了容器内的路径，没有写容器外的路径
docker run -d -P --name nginx01 -v /etc/nginx nginx

# 查看所有的 volume 的情况
[root@izbp1alfqlbdka40hbj17rz home]# docker volume ls
DRIVER    VOLUME NAME
local     34461b9a5f7e7cb25a6bfd71cc3edd218ef2b4326c72731e086e7d1caac7c7cf

# 具名挂载
[root@izbp1alfqlbdka40hbj17rz home]# docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx nginx
8ccb15ed728bad55f073a6ea0bbb2ff9dda619433fb551cabc21aeab9342b022
[root@izbp1alfqlbdka40hbj17rz home]# docker volume ls
DRIVER    VOLUME NAME
local     34461b9a5f7e7cb25a6bfd71cc3edd218ef2b4326c72731e086e7d1caac7c7cf
local     juming-nginx
[root@izbp1alfqlbdka40hbj17rz home]# docker volume inspect juming-nginx
[
    {
        "CreatedAt": "2021-07-23T15:09:53+08:00",
        "Driver": "local",
        "Labels": null,
        "Mountpoint": "/var/lib/docker/volumes/juming-nginx/_data",
        "Name": "juming-nginx",
        "Options": null,
        "Scope": "local"
    }
]
[root@izbp1alfqlbdka40hbj17rz home]#

### docker volume inspect 卷名 可以看到具体的挂载地址

```

所有的docker 容器内的卷，没有指定目录的情况下都是在 /var/lib/docker/volumes/xxxx/_data_

通过具名挂载可以方便的找到卷，大多数情况使用**具名挂载**

```shell
# 如何确定是具名挂载还是匿名挂载，还是指定路径挂载~
-v 容器内路径     # 匿名挂载
-v 卷名:容器内路径 # 具名挂载
-v /宿主机路劲:容器内路径  # 指定路径挂载
```



拓展:

```shell
# ro 只读   rw 读写

# ro说明这个路径下只能通过宿主机来操作，容器内部无法操作！
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:ro nginx
docker run -d -P --name nginx02 -v juming-nginx:/etc/nginx:rw nginx
```



#### 初识Dockerfile 

Dockerfile用来构建docker镜像的构建文件！

通过这个脚本可以生成镜像，镜像是一层一层的，脚本是一个一个的命令，每个命令都是一层！



```shell
[root@izbp1alfqlbdka40hbj17rz home]# cd docker-test-volume/
[root@izbp1alfqlbdka40hbj17rz docker-test-volume]# pwd
/home/docker-test-volume
[root@izbp1alfqlbdka40hbj17rz docker-test-volume]# vim dockerfile1

###### dockerfile内容
  
FROM centos

VOLUME ["/volume01","/volume02"]

CMD echo "---end---"
CMD /bin/bash


#######   开始build
[root@izbp1alfqlbdka40hbj17rz docker-test-volume]# docker build -f /home/docker-test-volume/dockerfile1 -t cqp/centos:1.0 .
Sending build context to Docker daemon  2.048kB
Step 1/4 : FROM centos
 ---> 300e315adb2f
Step 2/4 : VOLUME ["volume01","volume02"]
 ---> Running in a1f1abbde1d5
Removing intermediate container a1f1abbde1d5
 ---> 5ecabe934a7f
Step 3/4 : CMD echo "---end---"
 ---> Running in 40d220d11cee
Removing intermediate container 40d220d11cee
 ---> a775d32bee1e
Step 4/4 : CMD /bin/bash
 ---> Running in 5e5efe01a30a
Removing intermediate container 5e5efe01a30a
 ---> c0791c83fb3e
Successfully built c0791c83fb3e
Successfully tagged cqp/centos:1.0
[root@izbp1alfqlbdka40hbj17rz home]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
cqp/centos   1.0       c0791c83fb3e   38 seconds ago   209MB

###### 启动自己写的容器
[root@izbp1alfqlbdka40hbj17rz home]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
cqp/centos   1.0       20ba63e047c4   5 seconds ago   209MB
tomcat02     1.0       97200be88e52   6 hours ago     672MB
nginx        latest    08b152afcfae   22 hours ago    133MB
mysql        5.7       8cf625070931   22 hours ago    448MB
tomcat       9.0       36ef696ea43d   2 weeks ago     667MB
centos       latest    300e315adb2f   7 months ago    209MB
[root@izbp1alfqlbdka40hbj17rz home]# docker run -it 20ba63e047c4 /bin/bash
[root@1d967b528f66 /]# ls
bin  dev  etc  home  lib  lib64  lost+found  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var  volume01	volume02
##### 可以看到我们自己挂载的数据卷目录 volume01 volume02
##### 这个卷一定和外部有一个同步的目录 （匿名挂载）
```



 查看匿名卷挂载路径

```shell

[root@izbp1alfqlbdka40hbj17rz docker-test-volume]# docker inspect 1d967b528f66
```



![image-20210723162256351](https://gitee.com/iupikachu/img/raw/master/img/20210723162256.png)

外面和里面的内容是同步的

这种方式使用的非常多，因为我们需要自己构建自己的镜像！

假设构建镜像的时候没有挂在卷，要手动镜像挂载 -v 卷名:容器内路径！





#### 数据卷容器



![image-20210723163536922](https://gitee.com/iupikachu/img/raw/master/img/20210723163537.png)

```shell
docker run -it --name docker02 --volumes-from docker01 cqp/centos:1.0
```

docker01 与 docker02 容器之间的数据就共享了

如果删除 docker01 容器，docker02 的数据仍然在。

用处：多个mysql实现数据共享

```shell
[root@izbp1alfqlbdka40hbj17rz home]#docker run -d -p 3306:3306 -v /etc/mysql/conf.d -v /var/lib/mysql -e MYSQL_ROOT_PASSWORD=123456 --name mysql01 mysql:5.7
[root@izbp1alfqlbdka40hbj17rz home]#docker run -d -p 3307:3306 -e MYSQL_ROOT_PASSWORD=123456 --name mysql02 --volumes-from mysql01 mysql:5.7
```

mysql02 和 mysql01 的数据进行了共享。

结论:

容器之间配置信息的传递，数据卷容器的生命周期一直持续到没有容器使用为止。（高可用）

但是一旦数据持久化到了本地，这个时候，本地的数据是不会删除的。





### 8.DockerFile



#### DockerFile 介绍

构建docker镜像文件！命令参数脚本

步骤:

1. 编写dockerfile文件
2. docker build 构建一个镜像
3. docker run 运行镜像
4. docker push 发布镜像 （Dockerhub 、阿里云镜像仓库）



#### DockerFile 构建过程

**基础知识**:

1. 每个关键字（指令）都必须大写
2. 执行从上到下顺序执行
3. “#” 表示注释
4. 每一个指令都会创建提交一个新的镜像层，并提交！



![image-20210725105956554](https://gitee.com/iupikachu/img/raw/master/img/20210725105956.png)





#### DokcerFile 命令 

 	

```shell
FROM 						#基础镜镜像,一切从这里开始构建
MAINTAINER			#镜像是谁写的,姓名+邮箱
RUN							#镜像构建的时候需要运行的命令
ADD							#步骤: tomcat镜像,这个 tomcat压缩包!添加内容
WORKD工R				 #镜像的工作目录
VOLUME				  #挂载的目录
EXPOSE				  #保留端口配置
CMD					  	#指定这个容器启动的时候要运行的命令,只有最后一个会生效,可被替代
ENTRYPOINT		  #指定这个容器启动的时候要运行的命令,可以追加命令
ONBUILD					#当构建一个被继承 Dockery1e这个时候就会运行 ONBUILD的指令。触发指令。
COPY						#类似ADD,将我们文件拷贝到镜像中
ENV							#构建的时候设置环境变量!  设置密码

```



![nMzQji](https://gitee.com/iupikachu/img/raw/master/img/20210725121055.png)



#### 实战测试1

> 创建一个自己的centos



1.官方的centos没有 vim ifconfig 命令，我们自己装一个再打包成镜像

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -it centos
[root@a753f385acb4 /]# pwd
/
[root@a753f385acb4 /]# vim
bash: vim: command not found
[root@a753f385acb4 /]# ifconfig
bash: ifconfig: command not found
```



2. /home/dockerfile编写自己的dockerfile

```shell
[root@izbp1alfqlbdka40hbj17rz dockerfile]# cat mydockerfile-centos
FROM centos
MAINTAINER cqp<2111053717@qq.com>
~
ENV MYPATH /usr/local
WORKDIR $MYPATH
~
RUN yum -y install vim
RUN yum -y install net-tools
~
EXPOSE 80
~
CMD echo $MYPATH
CMD echo "---end---"
CMD /bin/bash
```



3. 通过这个文件构建镜像

   tips: 不要忘记最后的 . 

```shell
[root@izbp1alfqlbdka40hbj17rz dockerfile]# docker build -f mydockerfile-centos -t mycentos:0.1 .
```

结果:

```shell
Successfully built e8550c8afeab
Successfully tagged mycentos:0.1
```



4. 测试运行

   vim ifconfig 都有了

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -it mycentos:0.1
[root@c7547942858b local]# pwd
/usr/local
[root@c7547942858b local]# ifconfig
eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.18.0.2  netmask 255.255.0.0  broadcast 172.18.255.255
        ether 02:42:ac:12:00:02  txqueuelen 0  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        loop  txqueuelen 1  (Local Loopback)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[root@c7547942858b local]# vim
```





用docker history 查看镜像构建历史

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED         SIZE
mycentos     0.1       e8550c8afeab   7 minutes ago   289MB
cqp/centos   1.0       20ba63e047c4   45 hours ago    209MB
tomcat02     1.0       97200be88e52   2 days ago      672MB
nginx        latest    08b152afcfae   2 days ago      133MB
mysql        5.7       8cf625070931   2 days ago      448MB
tomcat       9.0       36ef696ea43d   3 weeks ago     667MB
centos       latest    300e315adb2f   7 months ago    209MB
[root@izbp1alfqlbdka40hbj17rz ~]# docker history e8550c8afeab
IMAGE          CREATED         CREATED BY                                      SIZE      COMMENT
e8550c8afeab   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "/bin…   0B
d6118dd269a6   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
c670c315cd1e   7 minutes ago   /bin/sh -c #(nop)  CMD ["/bin/sh" "-c" "echo…   0B
74f6e93a04bc   7 minutes ago   /bin/sh -c #(nop)  EXPOSE 80                    0B
08c44a3a309d   7 minutes ago   /bin/sh -c yum -y install net-tools             14.3MB
30a827ede07b   8 minutes ago   /bin/sh -c yum -y install vim                   65.1MB
ed911114c35e   8 minutes ago   /bin/sh -c #(nop) WORKDIR /usr/local            0B
be6a00cefaab   8 minutes ago   /bin/sh -c #(nop)  ENV MYPATH=/usr/local        0B
6cb64142883e   8 minutes ago   /bin/sh -c #(nop)  MAINTAINER cqp<2111053717…   0B
300e315adb2f   7 months ago    /bin/sh -c #(nop)  CMD ["/bin/bash"]            0B
<missing>      7 months ago    /bin/sh -c #(nop)  LABEL org.label-schema.sc…   0B
<missing>      7 months ago    /bin/sh -c #(nop) ADD file:bd7a2aed6ede423b7…   209MB
[root@izbp1alfqlbdka40hbj17rz ~]#
```





#### CMD 和 ENTRYPOINT 区别

```zsh
CMD					  	#指定这个容器启动的时候要运行的命令,只有最后一个会生效,可被替代
ENTRYPOINT		  #指定这个容器启动的时候要运行的命令,可以追加命令
```

在docker run的时候 entrypoint可以追加命令 CMD 必须后面加完整的命令





#### 实战测试2 (tomcat镜像)



1.准备镜像 /home/cqp/build

```shhell
[root@izbp1alfqlbdka40hbj17rz build]# ll
总用量 190972
-rw-r--r-- 1 root root 189815615 7月  26 09:19 jdk-8u162-linux-x64.tar.gz
-rw-r--r-- 1 root root   5736669 7月  26 09:19 tomcat-9.0.22.tar.gz
```

2. 编写Dockerfile 官方命名 **Dockerfile** , build 会自动寻找这个文件，就不需要docker build -f xxxxfile 指定了

```shell
[root@izbp1alfqlbdka40hbj17rz build]# vim Dockerfile
```

```dockerfile
FROM centos
MAINTAINER cqp<2111053717@qq.com>

COPY readme.txt /usr/local/readme.txt

ADD jdk-8u162-linux-x64.tar.gz /usr/local/
ADD tomcat-9.0.22.tar.gz /usr/local/

RUN yum -y install vim

ENV MYPATH /usr/local

WORKDIR $MYPATH

ENV JAVA_HOME /usr/local/jdk1.8.0_162
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar
ENV CATALINA_HOME /usr/local/tomcat-9.0.22
ENV CATALINA_BASH /usr/local/tomcat-9.0.22
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin

EXPOSE 8080

CMD /usr/local/tomcat-9.0.22/bin/startup.sh && tail -F /usr/local/tomcat-9.0.22/bin/logs/catalina.out
```

```shell
[root@izbp1alfqlbdka40hbj17rz build]# ls
Dockerfile  jdk-8u162-linux-x64.tar.gz  readme.txt  tomcat-9.0.22.tar.gz
[root@izbp1alfqlbdka40hbj17rz build]#
```



3. 开始build 镜像 (别忘记末尾的 . )

```shell
[root@izbp1alfqlbdka40hbj17rz build]# docker build -t diytomcat .
```



4. 运行

```shell
docker run -d -p 9090:8080 --name cqptomcat -v /home/cqp/build/tomcat/test:/usr/local/apache-tomcat-9.0.50/webapps/test -v /home/cqp/build/tomcat/tomcatlogs/:/usr/local/apache-tomcat-9.0.50/logs diytomcat
```

![image-20210726130211691](https://gitee.com/iupikachu/img/raw/master/img/20210726130217.png)





#### 发布自己的镜像

**DockerHub**

1. 要有dockerhub账号
2. 服务器上提交自己的镜像 docker tag打上自己的标签版本
3. 发布push

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker login -u iupikachu
Password:
WARNING! Your password will be stored unencrypted in /root/.docker/config.json.
Configure a credential helper to remove this warning. See
https://docs.docker.com/engine/reference/commandline/login/#credentials-store

Login Succeeded
[root@izbp1alfqlbdka40hbj17rz ~]# docker images
REPOSITORY         TAG       IMAGE ID       CREATED          SIZE
diytomcat          latest    0b2698210bae   15 minutes ago   675MB
mycentos           0.1       e8550c8afeab   26 hours ago     289MB
cqp/centos         1.0       20ba63e047c4   2 days ago       209MB
tomcat02           1.0       97200be88e52   3 days ago       672MB
nginx              latest    08b152afcfae   3 days ago       133MB
mysql              5.7       8cf625070931   3 days ago       448MB
tomcat             9.0       36ef696ea43d   3 weeks ago      667MB
centos             latest    300e315adb2f   7 months ago     209MB
[root@izbp1alfqlbdka40hbj17rz ~]# docker tag  0b2698210bae iupikachu/tomcat:1.0
[root@izbp1alfqlbdka40hbj17rz ~]# docker push iupikachu/tomcat:1.0
```

![image-20210726171656577](https://gitee.com/iupikachu/img/raw/master/img/20210726171656.png)



### 9.Docker网络



#### 理解docker0

清空环境

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker rm -f $(docker ps -aq)
[root@izbp1alfqlbdka40hbj17rz ~]# docker rmi -f $(docker images -aq)
[root@izbp1alfqlbdka40hbj17rz ~]# ip addr
```

![image-20210726191002190](https://gitee.com/iupikachu/img/raw/master/img/20210726191002.png)



> docker 是如何处理容器网络访问的?

![image-20210726191138650](https://gitee.com/iupikachu/img/raw/master/img/20210726191138.png)



查看容器的内部网络地址 ip addr  发现容器启动的时候会得到一个 eth0@if61 ip地址  docker分配的

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# clear
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat01 tomcat
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat01 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
60: eth0@if61: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:12:00:02 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.2/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
[root@izbp1alfqlbdka40hbj17rz ~]#
```



思考:linux能不能ping通容器内部

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.069 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.064 ms
64 bytes from 172.18.0.2: icmp_seq=3 ttl=64 time=0.055 ms
64 bytes from 172.18.0.2: icmp_seq=4 ttl=64 time=0.052 ms
```



#### Docker网络原理

1. 我们每启动一个docker容器，docker就会给容器分配一个ip，我们只要安装了 docker ，就会有一个网卡 docker0 桥接模式，使用的技术是 veth-pair 技术！



再次测试 ip addr

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:16:3e:01:2d:de brd ff:ff:ff:ff:ff:ff
    inet 172.17.28.54/18 brd 172.17.63.255 scope global dynamic eth0
       valid_lft 314411482sec preferred_lft 314411482sec
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:58:2a:e1:7a brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global docker0
       valid_lft forever preferred_lft forever
61: veth54bd1e2@if60: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP
    link/ether da:53:3b:bd:de:66 brd ff:ff:ff:ff:ff:ff link-netnsid 0
```

上面的 61:veth54bd1e2@if60 和docker0 的网卡一致。



2. 再启动一个容器，又多了一对网卡！

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat02 tomcat
2e9ff0165712f7f1eee29d64c49708912e6c0c290547bf957b0cdf28ad8ccc58
[root@izbp1alfqlbdka40hbj17rz ~]# ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
2: eth0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP qlen 1000
    link/ether 00:16:3e:01:2d:de brd ff:ff:ff:ff:ff:ff
    inet 172.17.28.54/18 brd 172.17.63.255 scope global dynamic eth0
       valid_lft 314411015sec preferred_lft 314411015sec
3: docker0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP
    link/ether 02:42:58:2a:e1:7a brd ff:ff:ff:ff:ff:ff
    inet 172.18.0.1/16 brd 172.18.255.255 scope global docker0
       valid_lft forever preferred_lft forever
61: veth54bd1e2@if60: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP
    link/ether da:53:3b:bd:de:66 brd ff:ff:ff:ff:ff:ff link-netnsid 0
63: vethc15a4d2@if62: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue master docker0 state UP
    link/ether 6e:61:c7:5a:f8:cb brd ff:ff:ff:ff:ff:ff link-netnsid 1
```

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat02 ip addr
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
62: eth0@if63: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc noqueue state UP group default
    link/ether 02:42:ac:12:00:03 brd ff:ff:ff:ff:ff:ff link-netnsid 0
    inet 172.18.0.3/16 brd 172.18.255.255 scope global eth0
       valid_lft forever preferred_lft forever
```

容器内的网卡和宿主机中的 docker0的网卡是对应的

**veth-pair** 就是一对的虚拟设备接口，都是成对出现的，一端连着协议，一端彼此相连。

正因为这个特性，veth-pair 充当桥梁，连接各种虚拟网络设备



3. 测试tomcat01 和 tomcat02 能否ping通



```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat02 ping 172.18.0.2
PING 172.18.0.2 (172.18.0.2) 56(84) bytes of data.
64 bytes from 172.18.0.2: icmp_seq=1 ttl=64 time=0.095 ms
64 bytes from 172.18.0.2: icmp_seq=2 ttl=64 time=0.065 ms
64 bytes from 172.18.0.2: icmp_seq=3 ttl=64 time=0.069 ms
64 bytes from 172.18.0.2: icmp_seq=4 ttl=64 time=0.067 ms
```



结论: 容器和容器之间是可以互相ping通的



**Dokcer 网络模型**

![image-20210727092745497](https://gitee.com/iupikachu/img/raw/master/img/20210727092745.png)



结论：tomcat01 和 tomcat02 是公用的一个路由器，docker0

所有的容器不指定网络的情况下，都是docker0路由的，docker0会给我们的容器分配一个默认的可用IP

> 小结

Docker 使用的是 Linux 的桥接

![image-20210727093443577](https://gitee.com/iupikachu/img/raw/master/img/20210727093443.png)







#### --link

能不能直接根据容器名来访问容器

直接ping是不行的

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat02 ping tomcat01
ping: tomcat01: Name or service not known

```

如何解决？

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P tomcat03 --link tomcat02 tomcat
Unable to find image 'tomcat03:latest' locally
^C
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name  tomcat03 --link tomcat02 tomcat
f6ab5988e8eb92de1819e869dad31c50ca8592fc95f8da764274a487f7c0fc7b
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat03 ping tomcat02
PING tomcat02 (172.18.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.18.0.3): icmp_seq=1 ttl=64 time=0.123 ms
64 bytes from tomcat02 (172.18.0.3): icmp_seq=2 ttl=64 time=0.069 ms
64 bytes from tomcat02 (172.18.0.3): icmp_seq=3 ttl=64 time=0.076 ms
^C
--- tomcat02 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 0.069/0.089/0.123/0.025 ms
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat02 ping tomcat03
ping: tomcat03: Name or service not known
```



```shell
docker run -d -P --name  tomcat03 --link tomcat02 tomcat
```

这样的话，tomcat03 可以直接ping通 tomcat02

但是 tomcat02 还是不能直接ping tomcat03



原理 --link 就是把 tomcat02 的 ip 写在 host 文件里

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat03 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.18.0.3	tomcat02 2e9ff0165712
172.18.0.4	f6ab5988e8eb
```

--link 这种方法太笨了，现在使用自定义网络！不适用docker0

docker0 不支持容器名连接访问！



#### 深入探究 docker 网络

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
8453c5717c3e   bridge    bridge    local
16e625fdb0d9   host      host      local
4dd060046601   none      null      local
```

bridge 就是 docker0 进去看一下

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker network inspect 8453c5717c3e
[
    {
        "Name": "bridge",
        "Id": "8453c5717c3e10f5ab1ed3c4fb10575a06acba968d27d3a7f9a1f421d3668a4d",
        "Created": "2021-07-15T21:02:29.734813838+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": null,
            "Config": [
                {
                    "Subnet": "172.18.0.0/16",
                    "Gateway": "172.18.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "2e9ff0165712f7f1eee29d64c49708912e6c0c290547bf957b0cdf28ad8ccc58": {
                "Name": "tomcat02",
                "EndpointID": "a47a99e13c633083a48c052aa73676f24c53b0be3e00c54db5c012f4f526e9f2",
                "MacAddress": "02:42:ac:12:00:03",
                "IPv4Address": "172.18.0.3/16",
                "IPv6Address": ""
            },
            "8ab01d80ae4a1ef124d21ab214f904438f8fc3b2591edce01279a9e37a4e97f9": {
                "Name": "tomcat01",
                "EndpointID": "1553ef18ff09f5b2a9499f86f4ac3992185763dd4a86a579d59b2410ff9ac6d2",
                "MacAddress": "02:42:ac:12:00:02",
                "IPv4Address": "172.18.0.2/16",
                "IPv6Address": ""
            },
            "f6ab5988e8eb92de1819e869dad31c50ca8592fc95f8da764274a487f7c0fc7b": {
                "Name": "tomcat03",
                "EndpointID": "5272c0063cef5b386dabe5798fa2a8a615cbf059493da3eddaf943b9d6877a71",
                "MacAddress": "02:42:ac:12:00:04",
                "IPv4Address": "172.18.0.4/16",
                "IPv6Address": ""
            }
        },
        "Options": {
            "com.docker.network.bridge.default_bridge": "true",
            "com.docker.network.bridge.enable_icc": "true",
            "com.docker.network.bridge.enable_ip_masquerade": "true",
            "com.docker.network.bridge.host_binding_ipv4": "0.0.0.0",
            "com.docker.network.bridge.name": "docker0",
            "com.docker.network.driver.mtu": "1500"
        },
        "Labels": {}
    }
]
```





#### 自定义网络（重要）

> 查看所有docker网络

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
8453c5717c3e   bridge    bridge    local
16e625fdb0d9   host      host      local
4dd060046601   none      null      local
```



**网络模式**

* bridge ：桥接模式
* none ：不配置网络
* host : 和宿主机共享网络
* container : 容器网络连通 (用的少！局限很大) 



```     shell
#我们直接启动的命令- net bridge,而这个就是我们的 docker
docker run -d -P --name tomcato1 tomcat
docker run -d -P --name tomcatol --net bridge tomcat

# docker0 特点: 默认，域名不能访问， --link可以打通连接

# 我们可以自定义一个网络
#--driver bridge
#-- subnet192.168.0.0/16
#-- gateway192.168.0.1
[root@izbp1alfqlbdka40hbj17rz ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
89b4d4892a4f9d925e08c8312f2326eb8ce22655fa73aa8f20a270a648c725f6
[root@izbp1alfqlbdka40hbj17rz ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
8453c5717c3e   bridge    bridge    local
16e625fdb0d9   host      host      local
89b4d4892a4f   mynet     bridge    local
4dd060046601   none      null      local
# 查看一下自己的网络
[root@izbp1alfqlbdka40hbj17rz ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "89b4d4892a4f9d925e08c8312f2326eb8ce22655fa73aa8f20a270a648c725f6",
        "Created": "2021-07-27T11:06:33.067854476+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {},
        "Options": {},
        "Labels": {}
    }
]
```

​    

跑两个容器测试一下

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat-net-01 --net mynet tomcat
c651478070c57c50532ae21a4090a9abc7f9411b2a95056fab853bcee000a8da
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat-net-02 --net mynet tomcat
4e1707b75100a37fd59f3f50ddd40245a60bd61ce8c15d18621b591eb8968bb1
[root@izbp1alfqlbdka40hbj17rz ~]# docker network inspect mynet
[
    {
        "Name": "mynet",
        "Id": "89b4d4892a4f9d925e08c8312f2326eb8ce22655fa73aa8f20a270a648c725f6",
        "Created": "2021-07-27T11:06:33.067854476+08:00",
        "Scope": "local",
        "Driver": "bridge",
        "EnableIPv6": false,
        "IPAM": {
            "Driver": "default",
            "Options": {},
            "Config": [
                {
                    "Subnet": "192.168.0.0/16",
                    "Gateway": "192.168.0.1"
                }
            ]
        },
        "Internal": false,
        "Attachable": false,
        "Ingress": false,
        "ConfigFrom": {
            "Network": ""
        },
        "ConfigOnly": false,
        "Containers": {
            "4e1707b75100a37fd59f3f50ddd40245a60bd61ce8c15d18621b591eb8968bb1": {
                "Name": "tomcat-net-02",
                "EndpointID": "e70bb735e0b365ea0154dd07e3a47f938e32577dff1e429ba0fba327235fb674",
                "MacAddress": "02:42:c0:a8:00:03",
                "IPv4Address": "192.168.0.3/16",
                "IPv6Address": ""
            },
            "c651478070c57c50532ae21a4090a9abc7f9411b2a95056fab853bcee000a8da": {
                "Name": "tomcat-net-01",
                "EndpointID": "1f5afc4f77c09ddb7d19ca91a410826a7f43296714617e686eac18c61fbf497c",
                "MacAddress": "02:42:c0:a8:00:02",
                "IPv4Address": "192.168.0.2/16",
                "IPv6Address": ""
            }
        },
        "Options": {},
        "Labels": {}
    }
]
```

测试 ping ip 和 名字

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat-net-01 ping 192.168.0.3
PING 192.168.0.3 (192.168.0.3) 56(84) bytes of data.
64 bytes from 192.168.0.3: icmp_seq=1 ttl=64 time=0.099 ms
64 bytes from 192.168.0.3: icmp_seq=2 ttl=64 time=0.069 ms
64 bytes from 192.168.0.3: icmp_seq=3 ttl=64 time=0.075 ms
^C
--- 192.168.0.3 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.069/0.081/0.099/0.013 ms

### 现在不使用 --link 也可以ping名字了

[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat-net-01 ping tomcat-net-02
PING tomcat-net-02 (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.049 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.072 ms
64 bytes from tomcat-net-02.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.071 ms
^C
--- tomcat-net-02 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 1000ms
rtt min/avg/max/mdev = 0.049/0.064/0.072/0.010 ms
```

自定义网络都可以ping通

自定义的网络docker 已经帮我们维护好了对应的关系，推荐这样使用网络！



好处:

不同的集群使用不同的网络。保证集群是安全和健康的。



#### 网络连通

> docker01 和 mynet 互相可不可以 ping通 ？

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat01 tomcat
bcd538acd39abd15bd0036627514441a0f4fff387c615d00156519e36fb5f536
[root@izbp1alfqlbdka40hbj17rz ~]# docker run -d -P --name tomcat02 tomcat
006bee5024d3bf7ca5fe7f962244c62a3ed23d71fbdfe16f200943d38e543d81
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it tomcat01 ping tomcat-net-01
ping: tomcat-net-01: Name or service not known
```

不行

但是可以通过 connect 去连通

![image-20210727141112788](https://gitee.com/iupikachu/img/raw/master/img/20210727141112.png)





![image-20210727140527678](https://gitee.com/iupikachu/img/raw/master/img/20210727140527.png)

![image-20210727140716619](https://gitee.com/iupikachu/img/raw/master/img/20210727140716.png)

​	

测试 打通docker01 -->  mynet 



```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker network connect mynet tomcat01
[root@izbp1alfqlbdka40hbj17rz ~]# docker network inspect mynet
```

![image-20210727141304942](https://gitee.com/iupikachu/img/raw/master/img/20210727141305.png)



一个容器两个 ip地址！

阿里云服务一样，公网 ip ， 私网 ip



再测试 能否ping通

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker exec -it  tomcat01 ping tomcat-net-01
PING tomcat-net-01 (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.087 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.077 ms
64 bytes from tomcat-net-01.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.080 ms
```





#### 实战: 部署 redis 集群 (不全)



![image-20210727141850343](https://gitee.com/iupikachu/img/raw/master/img/20210727141850.png)



创建 redis 网络

```shell
[root@izbp1alfqlbdka40hbj17rz ~]# docker network create redis --subnet 172.38.0.0/16
9501fed733988f84b85c9eb1d5fb259d3b89eecca272b3059f1cc28755d71875
[root@izbp1alfqlbdka40hbj17rz ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
8453c5717c3e   bridge    bridge    local
16e625fdb0d9   host      host      local
89b4d4892a4f   mynet     bridge    local
4dd060046601   none      null      local
9501fed73398   redis     bridge    local
[root@izbp1alfqlbdka40hbj17rz ~]# docker network inspect redis
```



shell 脚本

```shell
for port in $(seq 1 6); \
do \
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf

```





#### 实战: springboot 打包微服务镜像

1. 构建 sopringboot项目
2. 打包应用
3. 编写 dockerfile
4. 构建镜像
5. 发布运行！

```dockerfile
FROM java:8

COPY *.jar /app.jar

CMD  ["--server.port = 8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```

![image-20210727184253699](https://gitee.com/iupikachu/img/raw/master/img/20210727184253.png)

```shell
[root@izbp1alfqlbdka40hbj17rz idea]# ls
demo4docker-0.0.1-SNAPSHOT.jar  Dockerfile
[root@izbp1alfqlbdka40hbj17rz idea]# docker build -t cqp666
"docker build" requires exactly 1 argument.
See 'docker build --help'.

Usage:  docker build [OPTIONS] PATH | URL | -

Build an image from a Dockerfile
[root@izbp1alfqlbdka40hbj17rz idea]# docker build -t cqp666 .
Sending build context to Docker daemon  17.04MB
Step 1/5 : FROM java:8
8: Pulling from library/java
5040bd298390: Pull complete
fce5728aad85: Pull complete
76610ec20bf5: Pull complete
60170fec2151: Pull complete
e98f73de8f0d: Pull complete
11f7af24ed9c: Pull complete
49e2d6393f32: Pull complete
bb9cdec9c7f3: Pull complete
Digest: sha256:c1ff613e8ba25833d2e1940da0940c3824f03f802c449f3d1815a66b7f8c0e9d
Status: Downloaded newer image for java:8
 ---> d23bdf5b1b1b
Step 2/5 : COPY *.jar /app.jar
 ---> 5d65ab5ffff2
Step 3/5 : CMD  ["--server.port = 8080"]
 ---> Running in b8822cb67642
Removing intermediate container b8822cb67642
 ---> 7bc486c41ff2
Step 4/5 : EXPOSE 8080
 ---> Running in 34f5954a77fb
Removing intermediate container 34f5954a77fb
 ---> 7b8096182a5f
Step 5/5 : ENTRYPOINT ["java","-jar","/app.jar"]
 ---> Running in a0cca679a97c
Removing intermediate container a0cca679a97c
 ---> 9a81ab43c48e
Successfully built 9a81ab43c48e
Successfully tagged cqp666:latest
[root@izbp1alfqlbdka40hbj17rz idea]# docker images
REPOSITORY   TAG       IMAGE ID       CREATED          SIZE
cqp666       latest    9a81ab43c48e   10 seconds ago   660MB
tomcat       latest    46cfbf1293b1   4 days ago       668MB
java         8         d23bdf5b1b1b   4 years ago      643MB
[root@izbp1alfqlbdka40hbj17rz idea]# docker run -d -P --name cqp-springboot-web cqp666
5d708fc4b26784a275e492a8930d2b634209b807ed127c5b61f72a4e01eee86a
[root@izbp1alfqlbdka40hbj17rz idea]# docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED              STATUS              PORTS                     NAMES
5d708fc4b267   cqp666    "java -jar /app.jar …"   About a minute ago   Up About a minute   0.0.0.0:49163->8080/tcp   cqp-springboot-web
[root@izbp1alfqlbdka40hbj17rz idea]# curl localhost:49163
{"timestamp":"2021-07-27T10:38:58.301+00:00","status":404,"error":"Not Found","message":"","path":"/"}[root@izbp1alfqlbdka40hbj17rz idea]# curl localhost:49163/hello
hello cqp![root@izbp1alfqlbdka40hbj17rz idea]#
```





### 10.企业实战



#### Docker Compose

定义运行多个容器。

> 官方介绍

略



**docker compose安装**   **(linux)**



第一步

```shell
sudo curl -L "https://github.com/docker/compose/releases/download/1.29.2/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

### 上面官网的比较慢 推荐国内的镜像
curl -L https://get.daocloud.io/docker/compose/releases/download/1.24.0/docker-compose-`uname -s`-`uname -m` > /usr/local/bin/docker-compose


```



第二步 授权

```shell
sudo chmod +x /usr/local/bin/docker-compose
```



第三步 验证一下是否安装成功

```shell
[root@izbp1alfqlbdka40hbj17rz bin]# docker-compose version
docker-compose version 1.24.0, build 0aa59064
docker-py version: 3.7.2
CPython version: 3.6.8
OpenSSL version: OpenSSL 1.1.0j  20 Nov 2018
```



#### docker-compose 体验

https://docs.docker.com/compose/gettingstarted/

第一步 ：为项目创建目录

```shell
 mkdir composetest
 cd composetest
```



略~~跟着官方做



官方用了两个例子 第一个使用 Dockerfile build 构建 第二个使用官方的镜像

```shell
version: "3.9"
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```



最后有四个文件

```shell
[root@izbp1alfqlbdka40hbj17rz composetest]# ls
app.py  docker-compose.yml  Dockerfile  requirements.txt
[root@izbp1alfqlbdka40hbj17rz composetest]#
```



最后在当前文件下

```shell
docker-compose up
```

报错的话，可能是 yaml 版本太高，改为3.5即可

把dockerfile里 python版本降为3





Docker 小结:

1. Docker 镜像  run --》 容器
2. Dockerfile 构建镜像 （服务打包）
3. docker-compose 启动项目  (编排、多个微服务环境)
4. Docker 网络



#### yaml规则



docker-compose.yaml 核心

https://docs.docker.com/compose/compose-file/compose-file-v3/

```yaml
#3层!

verson: ''#版本
services: # 服务
	服务1:web
	#服务配置
	Images
	build
	network
		....
	服务2: redis
		....
	服务3: redis
#其他配置网络/卷、全局规则
volumes:
networks:
configs:
```









![image-20210728104850167](https://gitee.com/iupikachu/img/raw/master/img/20210728104850.png)









#### 实战: 自己编写微服务 docker-compose部署



第一步：编写项目

第二步:  修改 application.properties 因为是compose启动 所以host不能写相应的ip地址

```properties
server.port=8080
spring.redis.host=redis
```



第三步: 在项目根目录下编写Dockerfile

```dockerfile
FROM java:8

COPY *.jar /app.jar

CMD ["--server.port=8080"]

EXPOSE 8080

ENTRYPOINT ["java","-jar","/app.jar"]
```



第四步: 编写docker-compose.yaml

```yaml
version: '2.3'
services:
  cqp:
    build: .
    image: cqpapp
    depends_on:
      - redis
    ports:
      - "8080:8080"
  redis:
    image: "library/redis:alpinee"

```



第五步: idea打包 jar包 上传到服务器上

```shell
[root@izbp1alfqlbdka40hbj17rz cqpapp]# ls
demo4compose-0.0.1-SNAPSHOT.jar  docker-compose.yaml  Dockerfile
[root@izbp1alfqlbdka40hbj17rz cqpapp]# docker-compose up 
### 出错了 ，修改了版本之类的 再重新构建 --build
[root@izbp1alfqlbdka40hbj17rz cqpapp]# docker-compose up --build
Building cqp
Step 1/5 : FROM java:8
8: Pulling from library/java
5040bd298390: Pull complete
fce5728aad85: Pull complete
76610ec20bf5: Pull complete
60170fec2151: Pull complete
e98f73de8f0d: Pull complete
11f7af24ed9c: Pull complete
49e2d6393f32: Pull complete
bb9cdec9c7f3: Pull complete
Digest: sha256:c1ff613e8ba25833d2e1940da0940c3824f03f802c449f3d1815a66b7f8c0e9d
Status: Downloaded newer image for java:8
 ---> d23bdf5b1b1b
Step 2/5 : COPY *.jar /app.jar
 ---> 5e65f60e181f
Step 3/5 : CMD ["--server.port=8080"]
 ---> Running in f930a33ebcd1
Removing intermediate container f930a33ebcd1
 ---> e3a44c447484
Step 4/5 : EXPOSE 8080
 ---> Running in a1e6b67b9a93
Removing intermediate container a1e6b67b9a93
 ---> a215bbb0ec4a
Step 5/5 : ENTRYPOINT ["java","-jar","/app.jar"]
 ---> Running in 73cdd5cad5db
Removing intermediate container 73cdd5cad5db
 ---> ea520e38bdd9

Successfully built ea520e38bdd9
Successfully tagged cqpapp:latest
Creating cqpapp_redis_1 ... done
Creating cqpapp_cqp_1   ... done
Attaching to cqpapp_redis_1, cqpapp_cqp_1
redis_1  | 1:C 28 Jul 2021 06:52:04.637 # oO0OoO0OoO0Oo Redis is starting oO0OoO0OoO0Oo
redis_1  | 1:C 28 Jul 2021 06:52:04.637 # Redis version=6.2.5, bits=64, commit=00000000, modified=0, pid=1, just started
redis_1  | 1:C 28 Jul 2021 06:52:04.637 # Warning: no config file specified, using the default config. In order to specify a config file use redis-server /path/to/redis.conf
redis_1  | 1:M 28 Jul 2021 06:52:04.638 * monotonic clock: POSIX clock_gettime
redis_1  | 1:M 28 Jul 2021 06:52:04.639 * Running mode=standalone, port=6379.
redis_1  | 1:M 28 Jul 2021 06:52:04.639 # WARNING: The TCP backlog setting of 511 cannot be enforced because /proc/sys/net/core/somaxconn is set to the lower value of 128.
redis_1  | 1:M 28 Jul 2021 06:52:04.639 # Server initialized
redis_1  | 1:M 28 Jul 2021 06:52:04.639 # WARNING overcommit_memory is set to 0! Background save may fail under low memory condition. To fix this issue add 'vm.overcommit_memory = 1' to /etc/sysctl.conf and then reboot or run the command 'sysctl vm.overcommit_memory=1' for this to take effect.
redis_1  | 1:M 28 Jul 2021 06:52:04.639 * Ready to accept connections
cqp_1    |
cqp_1    |   .   ____          _            __ _ _
cqp_1    |  /\\ / ___'_ __ _ _(_)_ __  __ _ \ \ \ \
cqp_1    | ( ( )\___ | '_ | '_| | '_ \/ _` | \ \ \ \
cqp_1    |  \\/  ___)| |_)| | | | | || (_| |  ) ) ) )
cqp_1    |   '  |____| .__|_| |_|_| |_\__, | / / / /
cqp_1    |  =========|_|==============|___/=/_/_/_/
cqp_1    |  :: Spring Boot ::                (v2.5.3)

```

成功构建



**依赖指向**

![image-20210728150308261](https://gitee.com/iupikachu/img/raw/master/img/20210728150308.png)















Docker Swarm

CI/CD Jenkins 持续集成 

