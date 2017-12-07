---
layout:     post
title:      Docker-Selenium在WebUI测试中的使用
subtitle:   使用selenium-grid docker 进行WebUI测试
date:       2017-12-06
author:     Jelly
header-img: img/docker-selenium.jpg
catalog: true
tags:
    - 测试
    - selenium
    - docker
    - Nodejs
---
# selenium简介
>Selenium是一个WEB自动化测试工具
>支持跨浏览器的自动化测试
>支持跨操作系统的自动化测试
>支持多中编程语言编写脚本
>支持分布式测试分发和管理
>拥有一个支持录制的FF插件
>同时它又是一个扩展性非常好的工具，可以通过开发测试驱动来添加其可以支持的浏览器种类。
# 为什么使用dockr
>Docker采用Container虚拟化技术，可以快速启动同时保持主机与虚拟化的隔离性，建立好Image不论是要输出到Linux，Windows还是Mac，只要主机可以执行Docker Engine就可以保证执行的一致性，要做部署或者水平扩展都极其方便。还有一个很重要的原因就是使用docker可以执行多个不同版本的浏览器。
# 正文
#### docker安装
docker的安装就不在这里做讲解了，大家Google一下就可以找到答案了。
#### 创建并运行容器
- **创建selenium hub容器**
	```
	docker run -d -p 4444:4444 --name selenium-hub selenium/hub
	```
- **创建chrome node容器**
	```
	docker run -d -p 6001:5900 --name chrome01 --link selenium-hub:hub selenium/node-chrome-debug
	```
- **创建firefox node容器**
	```
	docker run -d -p 7001:5900 --name firefox01 --link selenium-hub:hub selenium/node-firefox-debug
	```
- **查看正在运行的容器**
	```
	[root@Jelly-Test-119 ~]# docker ps
CONTAINER ID        IMAGE                                                                                                                         COMMAND                  CREATED             STATUS              PORTS                    NAMES
43c587f18fa2        selenium/node-firefox-debug                                                                                                   "/opt/bin/entry_point"   57 minutes ago      Up 57 minutes       0.0.0.0:7001->5900/tcp   firefox01
f8afcb28a865        selenium/node-chrome-debug                                                                                                    "/opt/bin/entry_point"   About an hour ago   Up About an hour    0.0.0.0:6001->5900/tcp   chrome01
8c5fa9854d38        selenium/hub                                                                                                                  "/opt/bin/entry_point"   About an hour ago   Up About an hour    0.0.0.0:4444->4444/tcp   selenium-hub

	```
#### VNC远程浏览器环境并测试
- **VNC安装和使用**
debug结尾的镜像都带有VNC服务端，本机安装VNC客户端，即可远程连接。
以chrome01的容器为例：
输入172.16.1.119:6001-->回车-->输入密码：secret-->确认-->进入容器桌面

- **编写UI测试用例**
在这里使用Nodejs编写测试用例，记得要先 `npm install selenium-webdriver -g`

```
var webdriver = require('selenium-webdriver'),
    By = webdriver.By,
    until = webdriver.until;

var driver = new webdriver.Builder()
    .forBrowser('chrome')
    .usingServer('http://172.16.1.119:4444/wd/hub')
    .build();

driver.get('http://www.google.com');
driver.findElement(By.name('q')).sendKeys('webdriver');
driver.findElement(By.name('btnG')).click();
driver.wait(until.titleIs('webdriver - Google Search'), 1000);
driver.quit();
```
基本的测试就是这个样子，大家可以打开VNC Viewer观察一下浏览器的状况。
这篇文章就到这结束啦，感谢各位的观看。


