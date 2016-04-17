Docker Learing

2016.04.11
# Docker简介
Docker基于LXC（LinuX Containers), 轻量级虚拟化技术，”构建一次，在各平台上运行“（build once, run anywhere）。
容器和管理程序虚拟化（hypervisor virtualization, HV)不同，管理程序虚拟化通过将中间层将一台或多台独立的机器
虚拟机运行于物理硬件之上，而容器则是直接运行在操作系统内核之上的用户空间。
在超大规模的多租户服务部署、轻量级沙盒以及对安全性要求不太高的隔离环境中，容器技术非常流行。
容器本身比较复杂，不易安装，管理和自动化都很困难，而docker就是为改变这些而存在的。

Docker是一个能够把开发的应用程序自动部署到容器的开源引擎。
Docker在虚拟化的容器执行环境中增加一个应用程序部署引擎。
Docker可以类比为集装箱，集装箱运输货物，Docker运输软件。
持续集成：continuous integration CI
Docker包含一套镜像构建和镜像管理的解决方案。

Docker目前不支持32位CPU。
运行Linux3.8或更高版本内核。

docker程序是Docker守护进程的客户端程序。

2016.04.13
# Docker入门
查看docker程序是否存在
```
docker info
```


运行docker容器
```
docker run -i -t ubuntu /bin/bash
```
-i  标志保证容器中STDIN开启，持久的标准输入是交互式shell的“半边天”
-t  告诉Docker为要创建的容器分配一个伪tty终端，是另“半边天”
这样以后，新创建的容器才能提供一个交互式shell。
ubuntu  是我们基于的，用来创建容器的镜像，ubuntu是一个基础镜像。
/bin/bash   我们告诉Docker在新的容器中要运行什么命令，这里我们运行一个Bash shell。


查看当前系统中容器的列表
```
# 查看运行中的容器
docker ps

# 查看所有容器
docker ps -a

# 查看最后一次运行的容器
docker ps -l
```


三种方式可以唯一指代容器：
短UUID（一般为前5个字符即可）
长UUID（完整的UUID，一般不建议使用）
名称（可以自定义名称）


容器命名方式
```
docker run --name your_container -it ubuntu /bin/bash
```
容器名称有助于分辨容器
容器名称必须唯一
可以使用docker rm命令删除容器


重新启动一个已经停止的容器
```
docker start -i your_container
```
注意：如果你对一个正在运行的容器使用这个命令，那么正在运行的容器会被停止，然后重启


附着到容器上
```
docker attach your_container
```
重新附着到一个已经运行的容器上
书中讲的比较模糊，无法理解功能。下面是网上找到的，说的很清楚：
docker run
- -d, --detach=false Run container in background and print container ID
如果要attach交互使用，考虑
-i, --interactive=false Keep STDIN open even if not attached
-t, --tty=false Allocate a pseudo-TTY
docker run -it ubuntu:14.04 /bin/bash 可以启动一个ubuntu容器，里面可以进行各种操作，比如安装ssh server，然后连续ctrl+p, ctrl+q可以de-attach容器
docker attach <container_id>可以再attach进去


创建守护式容器
```
docker run --name daemon_dave -d ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"
```
使用-d参数，Docker会将容器放到后台运行。


查看容器中在干什么
```
docker logs your_container
```
使用-f参数来监控Docker日志
```
docker logs -f your_container
```
使用-t参数为每条日志加上时间戳
```
docker logs -ft your_container
```


查看容器内的进程
```
docker top your_container
```


在容器内部运行程序
我们可以通过docker exec命令在容器内部额外启动新进程。可以在容器内运行的进程有两种:后台任务和交互式任务
```
docker exec -d your_container touch /etc/new_config_file
```
通过docker exec后台命令，我们可以在正在运行的容器中进行维护、监控及管理任务。
```
docker exec -it your_container /bin/bash
```


停止守护式容器
```
docker stop your_container
```
快速停止可以使用
```
docker kill your_container
```


自动重启容器
```
docker run --restart=always --name daemon_dave -d ubuntu /bin/bash -c "while true; do echo hello world; sleep 1; done"
```
使用on-failure设定一个重启次数的参数：--restart=on-failure:5


获取更多的容器信息
```
docker inspect
```


删除容器
```
docker rm 804145adsf

# 删除所有容器
docker -rm `docker ps -a -q`
```
-a  表示列出所有容器
-q  表示只需要列出容器的id而不会返回容器的其他信息


2016.4.14
# 使用Docker镜像和仓库
Docker镜像：用来启动容器的构建基石。
Docker镜像是由文件系统叠加而成。

最底端是一个引导文件系统，即bootfs，这很像典型的Linux/Unix的引导文件系统。
    当一个容器启动后，它将会被移到内存中，而引导文件系统则会被卸载，留出更多的内存供initrd磁盘镜像使用。

Docker镜像的第二层是root文件系统rootfs，它位于引导文件系统之上。
    rootfs可以是一种或多种操作系统（如Debian或者Ubutnu)
    在Docker里，root文件系统永远只能是只读状态，并且Docker利用联合加载技术又会在root文件系统层上加载更多
    的只读文件系统。联合加载指的是一次同时加载多个文件系统，但是在外面看起来只能看到一个文件系统。
    联合加载会将各层文件系统叠加到一起，这样最终的文件系统会包含所有底层的文件和目录。

Docker将这样的文件系统称为镜像。
    一个镜像可以放到另一个镜像的顶部。位于下面的镜像称为父镜像，可以依次类推，直到镜像栈的最底部，
    最底部的镜像称为基础镜像。

最后，当从一个镜像启动时，Docker会在该镜像的最顶部加载一个读写文件系统。
    我们想要在Docker中运行的程序就是在这个读写层中执行的。

当Docker第一次启动一个容器时，初始的读写层是空的。当文件系统发生变化时，这些变化都会应用到这一层。

通常这种机制被称为“写时复制”（copy on write),这也是使Docker如此强大的技术之一。
    每个只读镜像层都是只读的，并且以后永远不会变化。当创建一个容器时，Docker会构建出一个镜像栈，
    并在栈的最顶端添加一个读写层。这个读写层再加上其上面的镜像层以及一些配置数据，就构成了一个容器。
    容器是可以修改的，并且可以启动和停止。容器的这种特点加上镜像分层框架，使我们可以快速构建镜像并
    运行包含我们自己应用程序和服务的容器。


列出镜像    
```
docker images
```

拉取镜像
```
docker pull [镜像名]
```

查找镜像，查找Docker Hub上公共的可用镜像
```
docker search
```


构建镜像
两种方式：
1.使用docker commit命令
2.使用docker build命令和Dockerfile文件
不推荐使用第一种方法，推荐使用第二种方法

Dockerfile使用基本的基于DSL语法的指令来构建一个Docker镜像，然后使用docker build命令基于
Dockerfile中的指令构建一个新的镜像。

第一个Dockerfile文件
```
# Version: 0.0.1
FROM ubuntu:14.04
MAINTAINER Feng Yao "me@fengyao.me"
RUN apt-get update
RUN apt-get install -y nginx
RUN echo 'Hi, I am in your container' \
    > /usr/share/nginx/html/index.html
EXPOSE 80
```
Dockerfile由一系列指令和参数组成。
Docker大体上按照如下流程执行Dockerfile：
1.Docker从基础镜像运行一个容器
2.执行一条指令，对容器进行修改
3.执行类似docker commit的操作，提交一个新的镜像层
4.Docker再基于刚提交的镜像运行一个新容器
5.执行Dockerfile中的下一条指令，直到所有指令都执行完毕

每条RUN指令都会创建一个新的镜像层

EXPOSE指令告诉Docker该容器内的应用程序会使用容器的指定端口。
这并不意味着可以自动访问任意容器运行中服务的端口。处于安全考虑，
Docker并不会自动打开该端口，而是需要你在使用docker run运行容器时来指定需要打开哪些端口。


基于Dockerfile构建新镜像
```
docker build -t="fengyao/static_web" .
```
命令中最后的.告诉Docker到本地目录中找Dockerfile文件。

可以指定一个Git仓库的源地址来指定Dockerfile的位置
```
docker build -t="fengyao/static_web:v1" git@github.com:fengyao/docker-static_web
```

构建缓存的取消
增加--no-cache标志
```
docker build --no-cache -t="fengyao/static_web" .
```

可以使用ENV指令来在镜像中设置环境变量


```
docker images [镜像名]
docker history [镜像ID]
```


从新镜像启动容器
```
docker run -d -p 80 --name static_web fengyao/static_web nginx -g "daemon off;"
```
-d  告诉docker以分离（detached）方式在后台运行
-p  用来控制Docker在运行时应该公开哪些网络端口给外部（宿主机）


2016.4.17
运行一个容器时，Docker可以通过两种方法来在宿主机上分配端口。
1.Docker可以自宿主机上随机选择一个位于49000~49900的一个比较大的端口号来映射到容器的80端口上。
2.可以在Docker宿主机中指定一个端口，这个端口会连接到容器中的80端口上。

使用docker port来查看容器的端口映射情况：
```
sudo docker port [容器ID]  [容器的端口号]
```

使用-p选项，我们可以灵活的管理容器和宿主机之间的端口映射关系。
比如，将容器的端口映射到Docker宿主机的某一特定端口上：
```
# 将容器内的80端口绑定到本地宿主机的80端口
sudo docker run -d -p 80:80 --name static_web fengyao/static_web nginx -g "daemon off;"
```

将端口绑定限制在特定的网络接口上（IP地址）：
```
sudo docker run -d -p 127.0.0.1:80:80 --name static_web fengyao/static_web nginx -g "daemon off;"
```

Docker还提供-P参数，该参数可以用来对外公开在Dockerfile中的EXPOSE指令中设置的所有端口
```
sudo docker run -d -P --name static_web fengyao/static_web nginx -g "daemon off;"
```

使用curl在查看web服务器
```
curl localhost:80
```


#
Dockerfile指令
CMD
CMD指令用于指定一个容器启动时要运行的命令。
跟使用docker run命令启动容器时指定要运行的命令非常类似
例子：
CMD ["/bin/bash"]
CMD ["/bin/bash", "-l"]
使用docker run命令可以覆盖CMD命令
在Dockerfile中只能指定一条CMD指令。如果指定了多条，只有最后一条有效。
如果想在启动容器时运行多个进程或多条命令，可以考虑使用Supervisor这样的服务管理工具。

ENTRYPOINT
与CMD类似，但是ENTRYPOINT不会被docker run命令中最后的参数所覆盖，实际上，
最后的参数都会被当做参数传递给ENTRYPOINT命令中指定的命令。
例子：
ENTRYPOINT ["/usr/sbin/nginx"]
ENTRYPOINT ["/usr/sbin/nginx", "-g", "daemon off;"]
如果确实需要覆盖，可以在docker run时使用--entrypoint标志覆盖ENTRYPOINT命令

WORKDIR
在容器内部设置一个工作目录，CMD和ENTRYPOINT指定的程序会在这个目录下执行
docker run可以通过-w标志在运行时覆盖工作目录

ENV
用来在镜像构建过程中设置环境变量
这些环境变量会被持久保存在我们镜像创建的任何容器中
docker run可以通过-e标志来传递环境变量，这些变量只会在运行时有效

USER
用来指定该镜像以什么样的用户去运行
docker run使用-u选项来覆盖
默认用户为root

VOLUME
用来向基于镜像创建的容器添加卷。
一个卷可以存在一个或多个容器内的特定目录，这个目录可以绕过联合文件系统。
卷功能可以让我们可以将数据（如源代码）、数据库或者其他内容添加到镜像中而不是将这些内容提交到镜像中，
并且运行我们在多个容器间共享这些内容。
我们可以使用这些功能来测试容器和内部的应用程序代码，管理日志或者处理容器内部的数据库。
例子：
VOLUME ["/opt/project"]
VOLUME ["/opt/project", "/data"]

ADD
用来将构建环境下的文件和目录复制到镜像中（仅当前目录下的文件）
文件源也可以使用URL的格式
会直接将归档文件解开
ADD指令会使得构建缓存变得无效

COPY
类似ADD，但COPY只关心构建上下文中复制文件。
COPY [源文件目录] [目标文件目录]
源文件目录必须是一个与当前构建环境相对应的文件或目录。

ONBUILD
为镜像添加触发器
当一个镜像被用作其他镜像的基础镜像时，该镜像中的触发器将会被执行。
ONBUILD触发器会按照在父镜像中指定的顺序执行，并且只能被继承一次。


#
将进行推送到Docker Hub
```
sudo docker push fengyao/static_web
```
推送前需要登录: sudo docker login
有自动构建功能
使用github或bitbucket代码库
设置Dockerfile目录

删除镜像
```
# 删除一个镜像
sudo docker rmi fengyao/static_web
# 删除多个镜像
sudo docker rmi fengyao/static_web fengyao/static_web:v1
# 删除所有镜像
sudo docker rmi `docker images -a -q`
```


# 
运行自己的Docker register
```
# 启动一个Registry应用的容器，并绑定到本地宿主机的5000端口
sudo docker run -p 5000:5000 registry
# 给镜像打上标签
sudo docker tag [镜像id] docker.example.com:5000/fengyao/static_web
# 推到新的Registry
sudo docker push docker.example.com:5000/fengyao/static_web
```


