# 作者：me@fengyao.me
# 时间：2016/03/30
# 记录：2016/03/31     将博客的上线分成几个阶段，分阶段实现，可行性更强
        2016/04/04     增加设计过程

关于个人博客，分成几个阶段：
阶段1：
    使用hexo创建博客并托管到github上，绑定域名，用markdown写文章。
    阶段1代号Monkey

阶段2: 
    在daocloud上建立自己的博客，使用flask框架进行编写，前端使用jinja。
    阶段2代号Horse

阶段3：
    前端用AngularJS重写，后端还是Flask，继续托管在daocloud上。
    阶段3代号Lion

阶段4：
    后端部署在阿里云服务器，重构代码。
    阶段4代号Ape


##################################################
阶段1：Monkey 像猴子一样灵巧，快速上线。
    之前有自己部署过hexo，但是没有深入，网站的界面就是最原始的，博文也只有一篇。
    现在，准备将hexo玩起来，旨在制作出一个由个性，比较绚丽的hexo博客。
    计划是：先博采众长，观察现存hexo模板框架的样式，源代码以及博文介绍。
    4.4 添加
    1. 先熟悉hexo的运行原理，完善网站的功能；
    2. 制作自己的主题，取名待定lightMonkey；
    3. 研究如何使用AngulaJS改写前端；
    4.7 添加
    使用flask+jinja2编写注册系统，参考项目build-a-saas-app-with-flask/catwatch/blueprints/user
    先用于公司的系统，公司所做项目系统还差一个注册功能；
    4.8 添加
    计划将域名更改为fengyao.me，更加个人化，me表示个人，其他域名弃用。
    使用自己域名带的邮箱，比如me@fengyao.me，一个字：帅！
    4.14 添加
    昨天去看了阿里云ECS服务器的价格，每个月最低都要一百多，也申请了亚马逊的免费aws，还没进一步使用。
    在build Dockerfile来创建镜像时，本地速度太慢了，准备在daocloud上充20元/月，开通会员，使用daocloud
    编译升级个人博客镜像；
    4.15 添加
    成功登陆aws上免费的ubuntu服务器，使用puttygen生成私钥，putty登陆aws云，账户使用ubuthu，密码使用
    公共dns，指定私钥文件目录。
    4.17 添加
    突然发现.me域名没有快照，经过分析，需要自己向谷歌和百度添加自己的网站地址和网页快照，谷歌预计一天后可以有效果！
    明天check一下！
    4.20 
    google上收录了fengyao.me网站的网页缓存
    4.21
    增加‘不蒜子’统计访客数量
    4.22
    计划将aws上的es2服务器连接到daocloud
	4.23
	为MacBookPro部署环境，准备用于工作
    将hexo博客生成的环境从win10移植到os x
    4.24
    本周的博文《flask的配置》无法推出了，下周项目上线，加了两天班。空闲时间在搭建MacBookPro的开发环境。
    4.25
    配置MacBookPro上使用ssh连接aws服务器
    重点在于：1.下载fengyaokey.pem秘钥文件；2.chmod 400 fengyaokey.pem; 3.ssh -i "fengyaokey.pem" ubuntu@[公有DNS]
    MacBookPro的Mysql数据库的字符设置还有问题，根据网上的教程将database的两个latin-1字符设置改为
    utf8后还是不行,。。。
    4.26
    公司项目上线
    4.27
    准备解决MacBookPro上Mysql数据库字符编码的问题
	4.28
	需要安装windows虚拟机
    4.29
    现场调试中
    4.30
    现场，晚上升级了mac系统到10.11.4
    5.1
    在macbookpro上安装了win7-32bit虚拟机，刚打开时页面显示的很小，将分辨率调高即可。
    mac上excel文件修改问题，解决方案是：通过qq转发给win7虚拟机，修改后发回来。
    lodop打印问题，通过win7虚拟机进行操作；
    需要在macbookpro上手动安装office2016
    5.2
    在MacBookPro上安装了office2016并进行了破解
    5.3
    MacBookPro上，MySQL的问题所在是：flask代码创建数据库表时，表的charset被设为Latin1
    所以无法导入中文
    5.8
    正在学习Flask-Cache的用法
    5.9
    将flask-cache应用于商品信息导入部分，使用缓存，使得5000各商品信息导入时间从108s减少到49s。
    增加了数据库索引，并没有太大变化。
    5.10
    增加索引，在mysql上运行，5000条的导入时间为38s。
    mysql数据库表charset不对的原因是：创建数据库时，需要设置所创建的数据库的charset，默认情况
    下是Latin1
    为导入部分增加测试
    5.11
    使用celery异步来处理大量数据导入的问题，将导入的数据存入某个目录文件中，将目录地址发送给celery task，
    task通过目录地址找到文件，读取后进行处理。
    接下去还要做一个前端数据导入进度条的功能。
    5.12
    websocket用来做类似网页客服的功能
    gulp-karma用来做angularjs测试
    ionicframework用来打包angular程序用于移动端程序打包
    5.13
    安装ionic环境，并跑起来，解决跨域问题；
    5.14
    研究angualr的karma单元测试，运行力鹏的代码，总是出错；
    明天准备自己写一个测试跑一下，还有celery多进程执行导入数据前端的框架编写；
    
    
    
