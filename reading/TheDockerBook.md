Docker Learing

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
