---
layout: post
title:  软件研发管理
categories: [CI]
tags: quality
---

## gitlab ##

代码仓库，实现代码和文档的集中管理

gitlab的安装(Docker)
	
	https://github.com/zhulongchao/gitlab.git

git的常用命令

	https://gist.github.com/guweigang/9848271

book的制作

如何将总结的markdown文件发布成网页版的book，制作每个人的blog

	http://gitbook-zh.wanqing.me/howtouse/README.html


## code style ##

编码风格的统一

	http://files.cnblogs.com/files/zhulongchao/ar_codestyle.xml

## check style ##
开发人员提交代码前，代码本地化静态分析

	http://files.cnblogs.com/files/zhulongchao/ar_checkstyle.xml

## jenkins ##

jenkis的安装(Docker)

	http://www.jianshu.com/p/b686afe75e24

利用pipeline插件，实现代码的持续化集成(CI)

	https://jenkins.io/doc/book/pipeline/jenkinsfile/
	https://www.phodal.com/blog/use-jenkinsfile-blue-ocean-visualization-pipeline/
	http://www.cnblogs.com/itech/p/5633948.html

### 单元测试和集成集成测试###
	http://www.jianshu.com/p/e638d64b6955

### jacoo report ###
	http://www.ezlippi.com/blog/2016/06/Jacoco-coverage.html
### sonarque ###
	https://my.oschina.net/u/2366460/blog/855719

### 持续化部署 ###

--checkout code

拉取最新的代码: git clone

--mvn test

运行单元测试，maven的插件：maven-surefire-plugin

--mvn verify

运行集成测试,maven的插件：maven-failsafe-plugin

--sonar 

安装sonar服务

安装jenkins的sonar插件，配置token，实现代码静态化分析

-- mvn package

-- build docker（cal version number）

-- push docker image

-- prepare the rollback version.

-- pull and run the new image in test enviroment

-- pull and run the new image in prepublish enviroment

-- pull and run the new image in production enviroment 

## 线上引流，beta测试 ##
http层面的引流，测试

双beta测试，不修改db和cache，只验证逻辑。




