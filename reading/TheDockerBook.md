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
