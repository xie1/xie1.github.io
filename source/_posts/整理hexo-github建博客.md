---
title: 整理hexo+github建博客
date: 2018-11-17 18:30:48
tags: hexo
categories: 工具
---
####0、安装使用过程需要提前了解点
		1、GitHub在去对GitHub page项目进行建立分支及主干，以供不同地方使用
		2、关注GitHub的ssh设置
		3、安装过程有个思路（环境安装+本地调试+部署测试+其他文件的配置（主题，域名，优化的东西））

####1、配置基本环境（Hexo需用通过npm安装，而npm需要node，现在只要安装node 就自带 npm了）
#####1.1、[安装NodeJs](http://nodejs.cn/)
		1、下载并安装
		2、在cmd 进行输入命令 查看版本 node -v
		3、npm install -g cnpm --registry=https://registry.npm.taobao.org  --》以后可以用cnpm 安装
	
#####1.2、[安装git](https://gitforwindows.org/)
		1、一路安装下去，鼠标右键会出现git bash
#####1.3、安装hexo
		1、先创建一个文件夹（用来存放所有blog的东西），进入文件夹下。
		2、按shift + 鼠标右击 //点击在此处打开命令窗口
		3、安装hexo命令：cnpm i -g hexo
		4、安装完成后，查看版本 hexo -v
		5、初始化命令：hexo init ，初始化完成之后打开所在的文件夹可以看到以下文件	
![初始化文件](https://i.imgur.com/M8vhxQK.png)
![版本查看](https://i.imgur.com/7pi7K6m.png)
		
####2、在GitHub上建立分支主干
#####2.1、在GitHub设置GitHub Page页
		步骤如下：
			1、创建并初始化一个仓库
![创建一个仓库](https://i.imgur.com/15mnQMx.png)
			

2、在项目的setting找到GitHub Pages如下，选模板进行生成GitHub Pages项目

![](https://i.imgur.com/wxNUzoN.png)
![](https://i.imgur.com/Oa4Rt8d.png)

#####2.2、在GitHub设置分支及主干
		1、考虑到如果在不同地方进行博客的编写，可以进行分支和主干的设置
		2、分支主要用于博客的源码存放，主干主要用于博客项目的部署使用（静态文件）
		操作步骤如下：
			1、创建分支
![](https://i.imgur.com/XSAqmqm.png)
![](https://i.imgur.com/YWnM8m7.png)
			2、设置分支为默认分支
![](https://i.imgur.com/eNYlMhp.png)	
#####2.3、创建SSH
		1、打开之前hexo 初始的文件夹，选择右键git bash here
		2、依次输入以下命令
			git config --global user.name "xxxx"
			git config --global user.email "xxxx@163.com"
			ssh-keygen -t rsa -C "xxxx@163.com"
		3、拷贝里面id_rsa.pub在github设置添加ssh key
![](https://i.imgur.com/UpZLO9D.png)
![](https://i.imgur.com/fwooR9H.png)
		4、ssh -T git@github.com ，如果如下图所示，出现你的用户名，那就成功。
![](https://i.imgur.com/cfs9DOl.jpg)
####3、配置的hexo配置文件
#####3.1、打开博客的根目录_config.yml文件，这是博客的配置文件
![](https://i.imgur.com/CThcoom.png)
#####3.2、本地部署测试
			1、hexo clean
			2、hexo g
			3、hexo s
			4、http://localhost:4000/  查看博客情况
#####3.3、GitHub部署测试
			1、停掉本地测试
			2、在DOS系统下：npm install hexo-deployer-git --save
			3、hexo clean
			4、hexo g
			5、hexo d
			6、输入你的GitHub pages的地址进行访问
![](https://i.imgur.com/uzc6C8j.png)
#####3.4、hexo的配置（全局站点设置和模板设置）
			1、待完善
####4、相关参考
1. [https://segmentfault.com/a/1190000014093946](https://segmentfault.com/a/1190000014093946)
2. [https://godweiyang.com/2018/04/13/hexo-blog/](https://godweiyang.com/2018/04/13/hexo-blog/)
3. [https://www.bilibili.com/video/av17433920/](https://www.bilibili.com/video/av17433920/)
4. [https://hexo.io/zh-cn/docs/index.html](https://hexo.io/zh-cn/docs/index.html "hexo文档")
5. [https://leroyli.github.io/2016/11/07/hexo-more-PC/](https://leroyli.github.io/2016/11/07/hexo-more-PC/ "不同电脑使用")
6. [https://theme-next.iissnan.com/](https://theme-next.iissnan.com/ "next主题")
7. [http://blog.codesfile.com/2017/12/16/HEXO%E6%90%AD%E9%85%8DNext%E4%B8%BB%E9%A2%98%E4%BF%AE%E6%94%B9%E5%8D%9A%E5%AE%A2/](http://blog.codesfile.com/2017/12/16/HEXO%E6%90%AD%E9%85%8DNext%E4%B8%BB%E9%A2%98%E4%BF%AE%E6%94%B9%E5%8D%9A%E5%AE%A2/ "next主题相关配置")





