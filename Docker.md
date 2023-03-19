# Docker

## 基础

### 简介

**docker简介**：Docker 是基于Go 语言实现的开源容器引擎，供开发人员和系统管理员使用容器构建，运行和共享应用程序的平

台。使用容器来部署应用程序称为容器化。容器并不是新事物,但用于轻松部署应用程序的容器却是新事物。简而言之,Docker就

是为了解决开发、运维、测试人员环境不一致、简化环境搭建、迁移、部署而提出的容器化技术。

> **go语言处理并发能力比较强**

- 容器的几个技术特点
  - **标准化**：每个容器都是标准化、可移植的，因为他们直接运行在宿主机的内核上
  - **隔离**：容器互相隔离，互不干扰，独立运行
  - **高性能：**容器性能开销极低，可以实现分钟级甚至秒级的部署和启动
  - **版本：**CE（Community Edition：社区版）和EE（Enterprise Edition：企业版）

**其他几个概念：**

* docker应用领域：一般在微服务领域

* docker还可以充分利用物理机资源同时能够整合服务器资源（也就是每个应用可以单独部署）

* CICD概念：持续开发，持续集成

### docker架构

![image-20210120090917311](typora-user-images\image-20210120090917311.png)

**docker 由三部分组成：Client 客户端、docker 主机、Registry 镜像仓库服务器**

- docker 主机：docker daemon：docker 安装后，会以后台运行、守护进程的形式存在images 镜像： 镜像可以用来创建docker 容器，一个镜像可以创建很多容器containers： 镜像和容器的关系类似面向对象设计中的类和对象,镜像是容器的模板，容器是基于镜像创建出来的。容器可以被创建、启动、停止、删除等等。**（镜像images就相当于java中的类，容器containers就相当于对象，一个镜像可以创建多个容器）**
  **注意：Docker 镜像（Image）就是一个只读的模板！也即不可以修改镜像。**
- Registry：镜像注册服务器https://hub.docker.com一个docker Registry（仓库注册服务器） 中可以包含多个Repository（仓库）；每个仓库可以包含多个标签/版本（Tag）；每个标签对应一个镜像。仓库分为公开仓库（Public）和私有仓库（Private）两种形式,公开仓库就是docker 官方仓库,私有仓库是自己搭建的docker 私服
- Client：docker 终端
  安装好docker 中，同时包含了docker 客户端，客户端负责执行docker 命令，发送给docker 主机中的docker 守护进程，例如：从仓库下载镜像，通过镜像创建容器等等。

#### 镜像容器的概念

- 镜像：一个镜像可以创建多个容器，镜象是一个只读的模板（一旦生成之后）
  - docker pull 要拉取的镜像：版本号（**版本号可以不写，不写的话默认拉取最新的**）
- 容器：docker利用容器可以独立运行一个或一组应用，容器就是镜像创建的运行实例，容器与容器之间是相互隔离的
- 镜像和容器的关联关系

### Docker 常用命令(基础一)

#### 镜像命令

##### 帮助命令

- 版本信息: docker version
- 详细信息: docker info
- 帮助文档: docker --help //列出所有的docker 命令
- 某个命令的帮助文档：<font color=red>**docker  命令(如search、image)  --help**</font>

##### 服务相关命令

- 启动docker 服务: systemctl start docker
- 停止docker 服务: systemctl stop docker
- 查看服务状态: systemctl status docker
- 重启docker 服务: systemctl restart docker
- 开机启动: systemctl enable docker

##### 列出本机镜像

- docker images      列出本机镜像
- 表头说明
  - REPOSITORY：镜像的名称
  - TAG： 镜像的版本号
  - IMAGE ID： 镜像ID
  - CREATED： 镜像创建时间
  - SIZE： 镜像大小
- docker images -a   查询所有镜像
- docker images -q   只显示镜像ID
- docker images  --no-trunc :显示完整的镜像ID

##### 查找镜像

- docker search 镜像名称     查找镜像
- docker search -s 30 tomcat      //-s: 列出收藏数不小于指定值的镜像或docker search --filter=stars=30 tomcat     //过滤器表示大于30的

##### 下载镜像

- docker pull  要拉取的镜像名：版本号（**版本号可以不写，不写的话默认拉取最新的**）

##### 删除镜像

- docker rmi 镜像名     删除镜像（默认删除最新的）
- docker rmi 镜像名:版本号      删除指定版本的镜像
- docker rmi 镜像id      根据镜像id删除镜像
- docker rmi -f  镜像名    强制删除已经创建了实例的镜像
- docker rmi  镜像名1  镜像名2  ......       删除多个镜像，使用空格间隔不同的镜像名称
- docker rmi -f  $(docker images -q)      删除所有镜像

##### 查看镜像详情

- docker inspect 镜像名称： 查看镜像详情

##### 查看镜像的构建过程

- docker history 镜像id    #查看镜像的制作步骤，学完dockerFile的时候就能看懂了

#### 容器命令

##### 列出容器

* docker ps    #列出所有正在运行的容器，不包含已停止的容器
* docker ps -a    #列出当前所有已经创建的容器
  * a ：列出当前所有已经创建的容器
  * -l ：显示最近创建的一个容器
  * -n  数字： 显示最近n个创建的容器
  * -q： 只显示容器编号

##### 创建容器

- docker create 镜像名称       根据镜像名创建一个容器
- docker create   --name    自定义名称  镜像名称                  根据镜像创建自定义名称的容器
- docker create 镜像id        根据镜像id 新建一个容器

##### 启动、停止、重启容器

* docker start  容器名称
* docker restart 容器名称
* docker stop 容器名称
* docker kill 容器名称:根据容器名强制停止一个容器

##### 创建并启动容器

> **当本地镜像存在时相当于docker create + docker start 或 当本地镜像不存在时，相当于docker pull + docker create + docker start**

* docker run [可选参数]   镜像名        根据镜像名创建一个容器，并启动，系统会自定义一个容器名
  * **可选参数：**
    * --name 指定容器名
    * -d 后台方式运行
    * -it   使用交互式运行，进入容器查看命令
    * -p   指定容器的端口，可以和主机的端口做映射
    * -P(大写)   Docker 会随机映射一个 49000~49900 的端口到内部容器开放的网络端口
* docker run   --name    自定义容器名   镜像名       创建一个容器起一个别名，并启动

##### 删除容器

* docker rm 容器名称         删除未启动的容器
* docker rm  -f  容器名称         强制删除已启动的容器
* docker rm -f $(docker ps -qa)         删除所有容器，包括正在运行的容器
* docker ps -a -q | xargs docker rm     删除所有容器，不包括正在运行的容器

##### 容器的两种退出模式

* exit     停止并退出容器
* ctrl + P + Q     不停止退出容器

##### 查看容器日志

- docker logs -ft  **[--tail  显示条数 ]**  容器id       显示条数可选，f表示输入日志，t表示时间戳
- 查看docker内部信息 ：    **docker top  容器id**

##### Docker内的数据拷贝到宿主机(不管容器是否启动都可以进行copy)

- docker cp 容器id:/容器文件所在目录   /要拷贝到的宿主机的目录

#### 启动交互式容器

##### 交互式启动

> docker run centos  启动后无事可做会立即停止

* docker run -it --name 自定义容器名 镜像名        启动后，当前命令行会进入到容器内部

> **-i：保持容器一直运行，但命令行会挂起，通常与-t 同时使用；**
> **-t：为容器分配一个伪输入终端，通常与-i 同时使用；**

##### 进入容器

- docker attach 容器名     当容器未停止时，在宿主机中可以进入到容器内部命令行(进入正在运行的容器)

##### 在容器外执行容器命令

* docker exec -it 容器名  要执行的命令            在容器外部执行命令，使命令在容器内部运行，并返回结果
* **补充：**docker exec -it   容器ID或容器名称   /bin/bash              在容器中打开新的终端，并且可以启动新的进程
  * 当时使用exit时他只会退出当前终端，如果原来还有开着的终端，那么那个终端还在运行

##### 启动守护式容器

* docker run -d --name 自定义容器名  镜像名         在后台启动容器，不占用命令行资源

##### 映射容器端口：-p

* docker run -d -p  映射到宿主机的端口号:docker容器的端口号  --name 容器名   镜像名        //启动一个容器并将容器端口发布到主机端口上

> -p: 指定端口映射，有以下四种格式

##### 容器启动时设置参数 -e

- docker run -e 你所要设置的参数

##### 用完即删除

> --rm表示用完就自动删除容器，一般用于做测试

- docker run -it  --rm 镜像名

##### 执行命令

* docker exec -it   容器ID或容器名称   /bin/bash          //在容器中打开新的终端，并且可以启动新的进程
  * 当时使用exit时他只会突出当前终端，如果原来还有开着的终端，那么那个终端还在运

##### 查看容器详情

* docker inspect 容器名

##### 查看docker cpu

- docker stats

### 应用部署知识点(基础二)

查看tomcat日志输出

- docker logs -tf --tail 20 atguigu_tomcat      #Ctrl + c #退出日志输出

> -t ：加入时间戳
> -f ：跟随最新的日志打印
> --tail：显示最后多少条

- docker top 容器名  查看容器中的进程

### Docker原理(基础三)

#### linux文件系统

典型的Linux文件系统又两部分组成：bootfs + rootfs

- bootfs
  bootloader（引导程序）：引导和加载kernel
  kernel：内核是操作系统的核心，具有很多最基本功能，
  它负责管理系统的进程、内存、设备驱动程序、文件和网络系统等等
- rootfs: rootfs 包含的是Linux 中的/bin，/etc，/usr 等标准目录和文件

rootfs在启动过程中时只读的，启动完成之后才变成可读可写的

![image-20210120150002898](typora-user-images\image-20210120150002898.png)

#### docker文件系统

> - <font color=red>**docker在pull的时候就是一层一层的去下载，如果发现已经存在的层，会进行复用，这样就节省了空间**</font>
> - <font color=red>**docker的镜像都是只读的，你对镜像的操作都是在原始镜像上又加了一层，最后进行打包时会把原始镜像和你新加的一层进行打包,变成一个改变过的镜像去运行（下方Docker commit 有体现）**</font> 

![image-20210804220001136](typora-user-images\image-20210804220001136.png)

**镜像和容器的关系**

docker 镜像是由特殊的文件系统叠加而成：

- 在docker 中，最低层是bootfs，并使用宿主机的bootfs,因此docker 的启动速度很快，因为无需完成引导和加载内核的工作
- bootfs 的上层是rootfs，被称为base images，初始化时是将rootfs以readonly 方式加载因此每一个容器都有一个文件系统，我们可以通过/bin/bash 进入
- 接下来docker 将一个readwrite 文件系统挂载在readonly 的rootfs 之上因此，我们进入容器的文件系统时，可以修改里面的内容，例如，部署一个程序，修改一个配置文件
- rootfs 的上层还可以叠加其他的镜像文件，例如JDK，JDK 上面再叠加
  Tomcat。你会发现这张图描述的就是Tomcat 镜像的结构图
  - 1．上层的镜像依赖下层的镜像，因此docker 中把下层的image 称作父镜像，没有父镜像的image 称作base image
  - 2．因此Tomcat 镜像的体积特别大，因为他至少包含了rootfs、JDK 和Tomcat 这些基础镜像层
  - 3．这些镜像层都是以readonly 方式加载的
- 这样一组readonly 和一个writeable 的结构构成一个container对于一个container 而言所有的修改都写入最上层的writeable 层中。

![image-20210120150547067](typora-user-images\image-20210120150547067.png)

#### docker 容器化技术和虚拟机技术

![image-20210120150909607](typora-user-images\image-20210120150909607.png)

虚拟机技术都有自己独立的操作做系统，容器化技术用的都是宿主机的操作系统

![image-20210120150854338](C:\Users\哦！\AppData\Roaming\Typora\typora-user-images\image-20210120150854338.png)

### Docker commit(基础四)

> <font color=red>**可以将一个容器打包成一个新的镜像**</font>

- 语法：docker commit  -m='new tomcat'   -a='bank'   要生成镜像的容器id/容器名    生成后的镜像名:生成后的版本

- > <font color=red>docker commit -m='new tomcat' -a='bank' tomcat01 tomcat02:1.0</font>
  >
  > -m ：提交的描述
  >
  > -a ： 作者

### Docker可视化

#### portainer(了解)

- ```shel
  docker run -d -p 8088:9000 --restart=always -v /var/run/docker.sock:/var/run/docker.sock --privileged=true portainer/portainer
  ```


#### Rancher(持续集成用CI/CD)

- 略

## 进阶

### 数据卷(进阶一)

**概念：**数据卷是存在于一个或多个容器中的特定文件或文件夹，这个文件或文件夹以独立于docker 文件系统的形式存在于宿主机中

**为什么要用数据卷**

解决容器关闭或损坏数据丢失问题，当容器删除时，容器中的数据可以被持久化，解决在多个容器之间共享数据

**数据卷的最大特点是：**其生存周期独立于容器的生存周期。使用数据卷可以在多
个容器之间共享数据。

实现理念：就是在宿主机创建一个文件夹和容器共享，能够存放容器中的数据

**实现命令：**

- **挂在一个数据卷**：docker  run  -it  -v   /文件夹名称(宿主机的):/容器的文件夹名称  --name 自定义容器名   镜像名            //创建容器并进行数据卷映射
- **挂在多个数据卷**：docker  run  -it   -v   /文件夹名称(宿主机的):/容器的文件夹名称    -v   /文件夹名称(宿主机的):/容器的文件夹名称    -v   /文件夹名称(宿主机的):/容器的文件夹名称  --name 自定义容器名   镜像名            //创建容器并进行数据卷映射

> -v：挂载数据卷
> 格式：/宿主机目录(文件):/容器内目录(文件)
> 目录必须是绝对路径
> 如果目录不存在，则自动创建
> 可以挂载多个数据卷
>
> 当-v 参数后面只有一个目录的时候，表示只设置容器中的数据卷目录，而宿主
> 机中的数据卷目录会被自动分配

#### 具名挂载和匿名挂载概念

- **具名挂载：**在docker启动挂载的时候指定映射到宿主机文件的名字
  - docker run -it -v juming-nginx.conf : /etc/nginx nginx
- **匿名挂载：**在docker启动的时候仅指定容器要映射出去的目录，宿主机的不指定
  - docker run -it -v /etc/nginx nginx
- **扩展：**

```shell
#如何区分具名挂载、匿名挂载、指定路径挂载
-v 容器路径   #匿名挂载
-v 卷名:容器路径 #具名挂载
-v 宿主机路径:容器路径  #指定路径挂载

#挂载时设置容器对卷操作的权限
docker run -it -v juming-nginx.conf : /etc/nginx:ro  nginx
docker run -it -v juming-nginx.conf : /etc/nginx:rw  nginx
 #ro redonly  表示容器内不能操作只能在容器外操作
 #rw readwrite  表示容器内有读写权限
 #默认不指定是具有读写权限
```

**<font color=red>命令演示</font>**

```shell
具名挂载：
    [root@bank ~]# docker run -it --name nginx02 -P -v juming-nginx.conf:/etc/nginx nginx
    [root@bank ~]# docker volume ls    #查看数据卷
    DRIVER    VOLUME NAME8
    local     juming-nginx.conf    #有名字
    [root@bank ~]# 
匿名挂载：
	[root@bank ~]# docker run -it --name nginx03 -P -v /etc/nginx nginx
	[root@bank ~]# docker volume ls
    DRIVER    VOLUME NAME
    local     3ad13585017b04684e1857a906c31207ec9061eb55f0e3ed517ede5e93d66708
    local     afc327bbac9b03f9e7fd63db180de190bfbc974ec5d5cc28130159444a5d25a8
    
    
-------------------------数据卷的操作命令----------------------------------------    
docker volume ls                  #查看数据卷名称
docker volume inspect 数据卷名称     #查看数据卷的信息
docker volume rm 数据卷名称         #删除数据卷

    [root@bank ~]# docker volume inspect juming-nginx.conf
    [
        {
            "CreatedAt": "2021-08-08T20:34:09+08:00",
            "Driver": "local",
            "Labels": null,
            "Mountpoint": "/var/lib/docker/volumes/juming-nginx.conf/_data",   #宿主机所在的目录
            "Name": "juming-nginx.conf",
            "Options": null,
            "Scope": "local"
        }
    ]
```

#### 数据卷的操作命令

- docker volume ls                  #查看数据卷名称
- docker volume inspect 数据卷名称     #查看数据卷的信息
- docker volume rm 数据卷名称         #删除数据卷

> <font color=red>**docker 所在宿主机的目录是    /var/lib/docker    下面包含了镜像、容器、数据卷、bulid等信息**</font>

#### 数据卷容器

就是用一个容器的目录专门去映射宿主机的目录，其他容器再去挂载那个与宿主机映射的容器即可

数据卷容器也是一个容器，但是这个容器的作用是专门提供数据卷供其他容器挂载，其它容器通过挂载这个数据卷容器实现数据共享。

![image-20210120135829029](typora-user-images\image-20210120135829029.png)

- **挂载：**docker run -it  --name 要挂载的容器名  --volumes-from 被挂载的容器名（如图上的容器1） 镜像名称:版本
  - --volumes-from：他后面所跟的容器名就是父容器的容器名，类似于你要创建一个子容器，去继承父容器
  - 当实现挂载之后，不管在那个容器内修改文件，所有容器都会同步

#### 扩展(DockerFile实现)

##### 自己制作镜像进行卷的挂载(使用的多)

<font color=blue>**如果看不懂可以跳过，看完dockerFile再回来看这个就很容易了**</font>

```shell
1、创建脚本命令
	[root@bank docker-volume-test]# vim dockerfile1  #创建文件
    [root@bank docker-volume-test]# cat dockerfile1  #查看文件
	#注意：脚本的编写格式为(命令要全大写)：
			命令 参数
    FROM centos   #from表示以什么为基础

    VOLUME ["volume01","volume02"]   #容器内你要挂在出去的卷名，相当于docker run -it -v /etc/nginx 		nginx匿名挂载,用双引号

    CMD echo ------end-----   #CMD表示执行命令

    CMD /bin/bash   #CMD表示执行命令，启动容器，进入控制台
    
2、使用命令生成镜像
	[root@bank docker-volume-test]# docker build -f dockerfile1 -t bankcentos:1.0 .   #生成镜像命令点不要忘记，他表示当前目录
    Sending build context to Docker daemon  2.048kB
    Step 1/4 : FROM centos        #执行脚本命令一
     ---> 300e315adb2f
    Step 2/4 : VOLUME ['volume01','volume02']   #执行脚本命令二
     ---> Running in 9cdbc868e463
    Removing intermediate container 9cdbc868e463
     ---> 05e10f5a1c78
    Step 3/4 : CMD echo ------end-----    #执行脚本命令三
     ---> Running in f1da20a82ef9
    Removing intermediate container f1da20a82ef9
     ---> dd894ab827ea
    Step 4/4 : CMD /bin/bash    #执行脚本命令四
     ---> Running in 4f1da8630c77
    Removing intermediate container 4f1da8630c77
     ---> 3815b8cba4cb
    Successfully built 3815b8cba4cb
    Successfully tagged bankcentos:1.0
    
3、使用自己创建的镜像运行容器
	[root@bank docker-volume-test]# docker images  #查看镜像
    REPOSITORY            TAG       IMAGE ID       CREATED         SIZE
    bankcentos            1.0       3815b8cba4cb   3 minutes ago   209MB  #自己做的
    [root@bank docker-volume-test]# docker run -it 3815b8cba4cb /bin/bash  #运行自己做的镜像
	CONTAINER ID   IMAGE                 COMMAND                  CREATED        
	2b439cc7d982   3815b8cba4cb          "/bin/bash"              2 minutes ago  #运行的容器参数

4、进入容器，使用ls -l命令可以看到卷，这里的卷一定有一个外部的同步卷
	drwxr-xr-x   2 root root   6 Aug  8 14:07 volume01
    drwxr-xr-x   2 root root   6 Aug  8 14:07 volume02
    
5、测试在容器的卷中创建文件，在宿主机找到对应的文件
	[root@d8328d75944a volume01]# touch container.txt #在容器的目录下创建文件
    [root@d8328d75944a volume01]# ls
    container.txt
	[root@bank ~]# docker inspect d8328d75944a   #到宿主机查看运行的docker信息，找到存储卷的目录
	#详细信息看下放图
	#详细信息看下放图
	#详细信息看下放图
```

![image-20210807222349871](typora-user-images\image-20210807222349871-16283462366331.png)

![image-20210807222504607](D:\尚硅谷\笔记整理\typora-user-images\image-20210807222504607.png)

<font color=red>**注意事项:**</font>

1. 编写命令脚本格式为     命令 参数       其中命令要全大写
2. 生成镜像命令docker build -f 命令文件名  -t  自定义镜像名:版本 **<font color=red>.</font>**
   - docker build -f dockerfile1 -t bankcentos:1.0 .   
     - -f 表示文件名
     - -t 表示tag     镜像名
     - 点表示当前目录
     - <font color=blue>**如果你使用的命令脚本的文件命名问Dockerfile，那么可以省略 -f 脚本文件名**</font>
3. 自己做的镜像运行的时候要么用镜像id去运行，要么用自己做的镜像名:版本 去运行，如果不指定版本会去pull最新的运行

### DockerFile(进阶二)

#### 初识DockerFile

DockerFile 就是用来构建Docker镜像的构建文件、命令脚本

通过脚本命令可以生成镜像，docker的镜像是一层一层的，每一行命令对应docker 的一层

<font color=blue>**官方的centos脚本命令**</font>

> **DockerHub上百分之99的镜像的都是以<font color=red>FROM scratch</font>为基础的**

```shell
FROM scratch
ADD centos-8-x86_64.tar.xz /
LABEL 
org.label-schema.schema-version="1.0"     
org.label-schema.name="CentOS Base Image"    
org.label-schema.vendor="CentOS"     
org.label-schema.license="GPLv2"     
org.label-schema.build-date="20201204"
CMD ["/bin/bash"]
```

**构建步骤:**

1. 写脚本命令
2. 使用docker命令  构建自己镜像
3. 运行自己构建的镜像

#### DockerFile的构建

**构建知识点**

1. 每一个关键字(即命令)  都是大写的
2. 实行顺序是一行一行执行
3. 文件中 # 表示注释
4. 每一行命令都对应docker镜像的一层
5. 步骤: 编写自己的dockerFile----->生成自己构建环境的镜像-------->运行镜像生成容器

**构建命令的解释**

|    命令    |                             解释                             |
| :--------: | :----------------------------------------------------------: |
|    FROM    |         表示以什么为基础,基础镜像,一切从这里开始构建         |
| MAINTAINER |                    镜像是谁写的,姓名+邮箱                    |
|    RUN     |                 镜像构建的时候需要运行的命令                 |
|    ADD     |   除了基础外,还要添加那些,如tomcat、jdk等，**会自动解压**    |
|  WORKDIR   |     镜像的工作目录,就是你进入镜像希望他在哪个指定目录下      |
|   VOLUME   |                       数据卷挂载的目录                       |
|   EXPOSE   |               指定制作好的镜像要爆露出去的端口               |
|    CMD     | 指定这个**容器启动**的时候要运行的命令，会被覆盖，只有最后一个会生效(比如你制作镜像时指定了某个命令，然后你在docker run的时候又指定了一次，那么运行时的命令**会将镜像里写好的覆盖**) |
| ENTRYPOINT | 指定这个**容器启动**的时候要运行的命令，会追加(比如你制作镜像时指定了某个命令，然后你在docker run的时候又指定了一次，那么运行时的命令会**追加至镜像里写好后面**) |
|  ONBUILD   | ONBUILD 是一个特殊的指令，它后面跟的是其它指令，比如 RUN, COPY 等，而这些指令，在当前镜像构建时并不会被执行。只有当以当前镜像为基础镜像，去构建下一级镜像的时候才会被执行(Dockerfile 中的其它指令都是为了定制当前镜像而准备的，唯有 ONBUILD **是为了帮助别人定制自己而准备的**) |
|    COPY    |          将文件拷贝到制作的镜像中，**不会自动解压**          |
|    ENV     |                    构建的时候设置环境变量                    |

![image-20210808115501686](typora-user-images\image-20210808115501686.png)

#### 实战一制作Centos

```shell
1、编写脚本文件
    [root@bank dockerfile]# vim dockerfile-centos
    FROM centos   #以什么为基础

    MAINTAINER bank<2323937303@qq.com>  #作者

    ENV MYPATH /usr/local   #设置环境变量

    WORKDIR $MYPATH    #设置工作环境

    RUN yum -y install vim     #运行安装命令
    RUN yum -y install net-tools   #运行安装命令

    EXPOSE 80    #暴露出去的端口

    CMD echo $MYPATH    #执行命令
    CMD echo "----end------"
    CMD /bin/bash   
    
2、构建镜像
	docker build -f dockerfile-centos -t mycentos:1.0 .    #点表示当前目录，不要忘了
	
3、将镜像运行成容器进行测试
	docker run -it --name mycentos01 mycentos:1.0    #运行容器
	[root@b7980cee5917 local]# ifconfig     #测试命令
	eth0: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 172.17.0.2  netmask 255.255.0.0  broadcast 172.17.255.255
        ether 02:42:ac:11:00:02  txqueuelen 0  (Ethernet)
        RX packets 8  bytes 648 (648.0 B)
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
	[root@b7980cee5917 local]# vim test.txt    #测试命令

```

- <font color=red>**使用docker history 镜像id  可以查看整个镜像的制作过程**</font>

<font color=red>**注意事项:**</font>

1. 编写命令脚本格式为     命令 参数       其中命令要全大写
2. 生成镜像命令docker build -f 脚本文件名  -t  自定义镜像名:版本 **<font color=red>.</font>**
   - docker build -f dockerfile1 -t bankcentos:1.0 .   
     - -f 表示文件名
     - -t 表示tag     镜像名
     - 点表示当前目录
     - <font color=blue>**如果你使用的命令脚本的文件命名问Dockerfile，那么可以省略 -f 脚本文件名**</font>
3. 自己做的镜像运行的时候要么用镜像id去运行，要么用自己做的镜像名:版本 去运行，如果不指定版本会去pull最新的运行

#### 实战二制作Tomcat

1. 编写脚本

```shell
[root@bank dockerfile]# cat Dockerfile 
FROM centos    #以什么为基础

MAINTAINER bank<2323937303@qq.com>   #作者

ADD jdk-8u161-linux-x64.tar.gz /usr/local/     #添加当前目录下的压缩包，会自动解压，不在同一目录下使用绝对
ADD apache-tomcat-8.5.57.tar.gz /usr/local/    # 路径好像也不可以，只能在和Dockerfile同一目录下

RUN yum -y install vim   #运行安装命令

ENV MYPATH /usr/local   #添加环境变量
WORKDIR $MYPATH        #指定docker的工作目录
  
ENV JAVA_HOME /usr/local/jdk1.8.0_161   #添加环境变量
ENV CLASSPATH $JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar   #添加环境变量
ENV CATALINA_HOME /usr/local/apache-tomcat-8.5.57   #添加环境变量
ENV CATALINA_BASE /usr/local/apache-tomcat-8.5.57   #添加环境变量
ENV PATH $PATH:$JAVA_HOME/bin:$CATALINA_HOME/lib:$CATALINA_HOME/bin   #添加环境变量
 
EXPOSE 8080   #暴露端口

CMD /usr/local/apache-tomcat-8.5.57/bin/startup.sh && tail -f /usr/local/apache-tomcat-8.5.57/logs/catalina.out     #容器启动执行，启动tomcat，并打印日志
```

2. 运行容器

```shell
docker run -d --name diyimage01 -p 9090:8080 -v /home/tomcat/webapps/test:/usr/local/apache-tomcat-8.5.57/webapps/test -v /home/tomcat/logs:/usr/local/apache-tomcat-8.5.57/logs diyimage:1.0
```

3. 在宿主机的**webapps**目录下创建**WEB-INF文件夹和index.html文件**，并在**WEB-INF中创建web.xml文件**，在浏览器**访问IP：端口/test**目录展现效果 , 还可以去映射到宿主机的logs文件夹下查看tomcat日志

```xml
<?xml version="1.0" encoding="UTF-8"?>
<web-app xmlns="http://xmlns.jcp.org/xml/ns/javaee"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://xmlns.jcp.org/xml/ns/javaee
http://xmlns.jcp.org/xml/ns/javaee/web-app_4_0.xsd"
         version="4.0">
  
  
</web-app>
```

```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>这是一个标题</title>
</head>
<body bgcolor="#fff7">
hello
<button onclick="alert('这是一个提示信息')">按钮</button>
<!--标题标签  1到6-->
<h1>标题1</h1>
<h2>标题2</h2>
<h3>标题3</h3>
<h4>标题4</h4>
<h5>标题5</h5>
<h6>标题6</h6>
</body>
</html>
```

#### 发布自己的镜像

##### 发布到DockerHub

- 登录自己的DockerHub账号
  - docker login -u  DockerHub用户名
  - 根据提示输入密码
- docker push  镜像名 : 版本 
  - 一般我们会将镜像更名为   用户名/镜像名:版本
    - 修改为规范的镜像：docker  tag  现存的镜像名:版本       用户名/镜像名:版本

##### 发布到阿里云镜像

- 阿里云官网有教程https://cr.console.aliyun.com/repository/cn-hangzhou/honey-bank/honey-test/details

### Docker 网络(进阶三)

#### 理解Docker0

<font color=red>**前沿知识补充：**</font>

1. <font color=blue> ip a    查看linux的网络 ，当启动docker时会有一个docker0的地址</font>
2. <font color=blue>我们每启动一个docker容器,docker就会给docker容器分配一个ip，我们只要安装了docker，就会有一个网卡docker0桥接模式,使用的技术是evth-pair技术</font>
3. <font color=blue>evth-pair：evth-pair就是一对的虚拟设备接口，他们都是成对出现的，一段连着协约，一段彼此相连。正应为有这个特性，evth-pair充当一个桥梁，连接各种虚拟网络设备，Docker容器之间的连接都是使用这种技术</font>
4. <font color=blue>宿主机之间能和容器之间相互访问</font>
5. <font color=blue>容器和容器之间也能够相互访问</font>

<font color=red>**宿主机**</font>

![image-20210808221100855](typora-user-images\image-20210808221100855.png)

<font color=red>**容器**</font>

![image-20210808221304589](typora-user-images\image-20210808221304589.png)

- docker exec -it 容器名 ip a   #进入容器查看ip
- docker exec -it 容器名 ping ip  进入容器ping某个ip

<font color=red>**原理图解**</font>

![image-20210808225828068](typora-user-images\image-20210808225828068.png)

<font color=blue>**结论:**</font> 容器之间能够访问并不是直连的，而是将Docker0当作路由器，通过路由访问的

- 所有的容器在不指定网络的情况下都是由Docker0路由的，Docker会给我们的容器分配一个默认的可用IP
- Docker使用的是Linux的桥接，宿主机是一个Docker容器的网桥，也就是docker0
- 最多能给65535个容器分配IP
- Docker所有的网络接口都是虚拟的，因为虚拟的转发效率高
- 只要删除容器，那么分配的一对evth-pair也会对应的消失

#### 容器互联 --link(了解，现在已不推荐使用)

> <font color=red>**-- link就是将 两个容器之间使用容器名进行绑定**</font>



**为什么使用--link：**

因为IP是随机分配的，那么每次启动IP都会变化，如果我们在配置文件中写了某个IP那么以后需要频繁的去修改，--link可以解决这个问题，他会在容器的hosts文件中将容器名和ip做映射，实现通过服务名去访问

```shell
[root@bank ~]# docker exec -it 9b003a9a23d3 cat /etc/hosts
127.0.0.1	localhost
::1	localhost ip6-localhost ip6-loopback
fe00::0	ip6-localnet
ff00::0	ip6-mcastprefix
ff02::1	ip6-allnodes
ff02::2	ip6-allrouters
172.17.0.3	tomcat02 b7f71f8478d3    #做的映射
172.17.0.4	9b003a9a23d3

```

**使用语法：**

- 在容器启动的时候使用  --link  后面跟上你要连接的指定容器名
  - docker run -it -P --name tomcat03 --link tomcat02 tomcat    #相当于在tomcat03这个容器的hosts文件中增加了一个IP 和容器名的映射
  - 例：127.0.0.1    www.baidu.com   访问www.baid.com会访问本机地址
  - 注意：这种方式不是双向的，是单向的，因为是在hosts中添加的

```shell
[root@bank ~]# docker run -it -P --name tomcat03 --link tomcat02 tomcat

[root@bank ~]# docker exec -it 9b003a9a23d3 ping tomcat02
PING tomcat02 (172.17.0.3) 56(84) bytes of data.
64 bytes from tomcat02 (172.17.0.3): icmp_seq=1 ttl=64 time=0.343 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=2 ttl=64 time=0.123 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=3 ttl=64 time=0.126 ms
64 bytes from tomcat02 (172.17.0.3): icmp_seq=4 ttl=64 time=0.125 ms
```

**Docker查看网络命令**

- docker network ls  #查看当前网络
- docker network  inspect  上方查出来的网络ID     #查看当前网卡的所有信息
- 使用docker  inspect  容器ID   #查看容器详情中的Links也能看到绑定状态

```shell
 "Links": [
     "/tomcat02:/tomcat03/tomcat02"
	 ],
```

#### 自定义网络

> **自定义网络不适用于docker0**
>
> **自定义网络的好处，可以不用通过 --link的方式 也能通过容器名相互访问**
>
> **不同集群可以创建不同的网络，保证集群的网络是安全和健康的**

**网络模式：**

```shell
查看所有网络：docker network ls
[root@bank ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
b1f1ebd4b770   bridge    bridge    local
aa7aca772b70   host      host      local
1282dbf9d448   none      null      local
[root@bank ~]# 
```

- bridge：他指的是默认的docker0，使用桥接的方式
- none：不配置网络
- host：主机模式，表示和宿主机共享网络

**创建网络：**

> <font color=red>**我们以前默认启动容器的时候都隐式的有一个  --net bridge  命令,也就是默认走docker0**</font>
>
> <font color=red>**docker run -it -P --name tomcat01  tomcat**</font>
>
> <font color=red>**docker run -it -P --name tomcat01 --net bridge tomcat**</font>

- docker network create --help   #查看创建网络的用法

```shell
[root@bank ~]# docker network create --driver bridge --subnet 192.168.0.0/16 --gateway 192.168.0.1 mynet
47cd641e38b744a5d627481689435160c3693ffc2af375a6d5b2ff00f0e089df
[root@bank ~]# docker network ls
NETWORK ID     NAME      DRIVER    SCOPE
b1f1ebd4b770   bridge    bridge    local
aa7aca772b70   host      host      local
47cd641e38b7   mynet     bridge    local
1282dbf9d448   none      null      local

```

- 语法：docker network create --driver bridge   --subnet 192.168.0.0/16    --gateway 192.168.0.1  自定义网卡名
  - --driver      选择驱动方式模式是bridge桥接模式(可以选择不写)
  - --subnet    子网192.168.0.0/16表示能分配65535个IP给容器，192.168.0.0/24表示能分配255个IP
  - --gateway  设置网关

**测试启动使用自己的网络：**

```shell
[root@bank ~]# docker run -d -P --name tomcat01-net --net mynet tomcat
47cd641e38b744a5d627481689435160c3693ffc2af375a6d5b2ff00f0e089df
[root@bank ~]# docker run -d -P --name tomcat02-net --net mynet tomcat
2902f42c6c20e2b4795e2f4a1b639fa30b9823704846d6d75d4a978eb1d69e61
[root@bank ~]# docker exec -it 69c0ab813801 ping tomcat02-net
PING tomcat02-net (192.168.0.2) 56(84) bytes of data.
64 bytes from tomcat02-net.mynet (192.168.0.2): icmp_seq=1 ttl=64 time=0.118 ms
64 bytes from tomcat02-net.mynet (192.168.0.2): icmp_seq=2 ttl=64 time=0.125 ms
64 bytes from tomcat02-net.mynet (192.168.0.2): icmp_seq=3 ttl=64 time=0.129 ms
```

#### 网络互通

![image-20210809223501230](typora-user-images\image-20210809223501230.png)

<font color=blue>**现有两个不同网段的网络，每个网络都有自己的容器，正常情况下我们不能使用docker0的容器去访问自定义网络的容器，但是我们可以使用网咯互通的方式，让两个不同网段的容器进行互相访问,网络互通指的是容器与网卡之间的大同，而不是网卡与网卡之间的打通**</font>

<font color=blue>**网络互通的本质就是，一个容器两个IP，类似于阿里云的外网和内网IP一样**</font>

**实现方式：**

- docker network connect --help    #查看互通命令
-  docker network connect  网卡名   容器名     #将某个网卡与某个容器连通
  - 例：docker network connect mynet tomcat01

```shell
[root@bank ~]# docker network connect mynet tomcat01
[root@bank ~]# docker exec -it 7391f0aca32d4 ping 192.168.0.2
PING 192.168.0.2 (192.168.0.2) 56(84) bytes of data.
64 bytes from 192.168.0.2: icmp_seq=1 ttl=64 time=0.275 ms
64 bytes from 192.168.0.2: icmp_seq=2 ttl=64 time=0.074 ms
64 bytes from 192.168.0.2: icmp_seq=3 ttl=64 time=0.128 ms
64 bytes from 192.168.0.2: icmp_seq=4 ttl=64 time=0.129 ms
[root@bank ~]# docker exec -it 7391f0aca32d4 ping tomcat01-net
PING tomcat01-net (192.168.0.3) 56(84) bytes of data.
64 bytes from tomcat01-net.mynet (192.168.0.3): icmp_seq=1 ttl=64 time=0.161 ms
64 bytes from tomcat01-net.mynet (192.168.0.3): icmp_seq=2 ttl=64 time=0.127 ms
64 bytes from tomcat01-net.mynet (192.168.0.3): icmp_seq=3 ttl=64 time=0.119 ms
64 bytes from tomcat01-net.mynet (192.168.0.3): icmp_seq=4 ttl=64 time=0.123 ms
64 bytes from tomcat01-net.mynet (192.168.0.3): icmp_seq=5 ttl=64 time=0.123 ms
```

**通过docker network inspect mynet查看mynet网卡信息可以发现如下图所示：**

![image-20210809225113226](typora-user-images\image-20210809225113226.png)

#### 实战Redis集群

> 命令连接：https://blog.csdn.net/weixin_41896265/article/details/108245264

1. 创建网络

```shell
[root@bank home]# docker network create --subnet 172.38.0.0/16 --gateway 172.38.0.1 redisnet
```

2. 执行redis配置脚本

```shell
for port in $(seq 1 6);
do
mkdir -p /mydata/redis/node-${port}/conf
touch /mydata/redis/node-${port}/conf/redis.conf
cat << EOF >/mydata/redis/node-${port}/conf/redis.conf
port 6379
bind 0.0.0.0
cluster-enabled yes
cluster-config-file nodes.conf
cluster-node-timeout 5000
cluster-announce-ip 172.38.0.1${port}
cluster-announce-port 6379
cluster-announce-bus-port 16379
appendonly yes
EOF
done 
```

3. 拉取并启动容器

```shell
//节点1
docker run -p 6371:6379 -p 16371:16379 --name redis-1 \
 -v /mydata/redis/node-1/data:/data \
 -v /mydata/redis/node-1/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.11 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
 
//节点2
docker run -p 6372:6379 -p 16372:16379 --name redis-2 \
 -v /mydata/redis/node-2/data:/data \
 -v /mydata/redis/node-2/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.12 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

//节点3
docker run -p 6373:6379 -p 16373:16379 --name redis-3 \
 -v /mydata/redis/node-3/data:/data \
 -v /mydata/redis/node-3/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.13 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

//节点4
docker run -p 6374:6379 -p 16374:16379 --name redis-4 \
 -v /mydata/redis/node-4data:/data \
 -v /mydata/redis/node-4/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.14 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

//节点5
docker run -p 6375:6379 -p 16375:16379 --name redis-5 \
 -v /mydata/redis/node-5/data:/data \
 -v /mydata/redis/node-5/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.15 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf

//节点6
docker run -p 6376:6379 -p 16376:16379 --name redis-6 \
 -v /mydata/redis/node-6/data:/data \
 -v /mydata/redis/node-6/conf/redis.conf:/etc/redis/redis.conf \
 -d --net redis --ip 172.38.0.16 redis:5.0.9-alpine3.11 redis-server /etc/redis/redis.conf
```

4. 随便进入一个redis容器

```shell
docker exec -it redis-1 /bin/sh
```

5. 执行命令集群部署

```shel
redis-cli --cluster create 172.38.0.11:6379 172.38.0.12:6379 172.38.0.13:6379 172.38.0.14:6379 172.38.0.15:6379 172.38.0.16:6379 --cluster-replicas 
```

6. 集群部署成功！

### 镜像制作(进阶四)

#### 方式一通过容器

1. 生成镜像
   - 语法：docker commit  -m='new tomcat'   -a='bank'   要生成镜像的容器id/容器名    生成后的镜像名:生成后的版本
   
   - > <font color=red>docker commit -m='new tomcat' -a='bank' tomcat01 tomcat02:1.0</font>
     >
     > -m ：提交的描述
     >
     > -a ： 作者
2. 压缩镜像
   
   - 语法：docker save -o 压缩文件名称  镜像名称:版本号
3. 还原镜像(自己测试用)
   
   - docker load -i 压缩文件名称

#### 方式二通过Dockerfile（推荐）

1. 编写Dockerfile 文件
2. 构建镜像
   - docker build -t 镜像名:版本号. #Dockerfile 文件在当前目录
   - docker build -f Dockerfile 文件-t 镜像名:版本号. #Dockerfile 文件在指定目录
3. 创建容器
4. 制作一个镜像

### 服务编排（了解）
