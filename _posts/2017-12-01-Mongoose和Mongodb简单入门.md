---
layout:     post
title:      Mongoose和Mongodb简单入门
subtitle:   
date:       2017-12-01
author:     Jelly
header-img: img/post-bg-ios9-web.jpg
catalog: true
tags:
    - Nodejs
    - Mongodb
    - 数据库
    
---

##一、MongoDB安装启动
>简介
 MongoDB 是由C++语言编写的，是一个基于分布式文件存储的开源数据库系统。
在高负载的情况下，添加更多的节点，可以保证服务器性能。
MongoDB 旨在为WEB应用提供可扩展的高性能数据存储解决方案。
MongoDB 将数据存储为一个文档，数据结构由键值(key=>value)对组成。MongoDB 文档类似于 JSON 对象。字段值可以包含其他文档，数组及文档数组。
#####  1. 使用yum安装.
- 添加yum源
```
[root@Jelly-Test-32 ~]# vi /etc/yum.repos.d/mongodb-org-3.2.repo 
[mongodb-org-3.2]
name=MongoDB Repository
baseurl=https://repo.mongodb.org/yum/redhat/$releasever/mongodb-org/3.2/x86_64/
gpgcheck=1
enabled=1
gpgkey=https://www.mongodb.org/static/pgp/server-3.2.asc
```
- 安装MongoDB
 ```
[root@Jelly-Test-32 ~]# yum -y install mongodb-org 
  ```
- 启动
```
[root@Jelly-Test-32 ~]# service mongod start
```
#####  2. 使用docker安装(强烈推荐)
*关于docker的安装和使用可以看我的另一篇文章...*
- 拉取镜像
```
[root@Jelly-Test-32 ~]# docker pull mongo
```
- 创建目录mongo，用于数据存放
 ```
[root@Jelly-Test-32 ~]# mkdir -p ~/mongo  ~/mongo/db
```
- 运行容器，使用docker镜像
 ```
[root@Jelly-Test-32 ~]# docker run -p 27017:27017 -v /root/mongo/db:/data/db -d mongo
```
*命令解释：
-p 27017:27017 :将容器的27017 端口映射到主机的27017 端口
-v /root/mongo/db:/data/db :将主机中当前目录下的db挂载到容器的/data/db，作为mongo数据存储目录，不懂的同学可以去学习一下docker相关知识*
**到这里我们的MongoDB就安装完成了。**
##二、使用mongoose进行增删改查
*下载mongoose就非常简单啦，进入项目目录执行*
` [root@Jelly-Test-32 u]# npm install mongoose --save`
*在文件内使用mongoose*
```
var mongoose = require('mongoose'),
	Schema = mongoose.Schema
mongoose.connect('mongodb://localhost:27017/jelly')//连接MongoDB
```
>Schema —— 一种以文件形式存储的数据库模型骨架，无法直接通往数据库端，也就是说它不具备对数据库的操作能力，仅仅只是数据库模型在程序片段中的一种表现，可以说是数据属性模型(传统意义的表结构)，又或着是“集合”的模型骨架。
>Model —— 由Schema构造生成的模型，除了Schema定义的数据库骨架以外，还具有数据库操作的行为，类似于管理数据库属性、行为的类。
>Entity —— 由Model创建的实体，使用save方法保存数据，Model和Entity都有能影响数据库的操作，但Model比Entity更具操作性。
- 创建Schema，Model，Entity
```
//Schema
var StudentSchema = new Schema({
	num: {type: Number,unique: true}, //唯一不可重复
	name: {type: String},
	age: {type: Number}
})
//Model
var StudentModel = mongoose.model('student',StudentSchema)
//Entity
var StudentEntity = new StudentModel({
    num: 1, //唯一不可重复
    name: "jelly",
    age: 22
})
```
- 増
```
//命令： [Model.create(doc(s), [callback])]
//使用save 
StudentEntity.save(function(error,doc){
  if(error){
     console.log("error :" + error);
  }else{
     console.log(doc);
  }
});

//使用create
StudentModel.create(StudentEntity,function(err,docs){})
//或者
StudentModel.create({
	num: 1,
	name: "jelly" ,
	age: 22
 },function(err,docs){})
```
- 删
```
//命令: [Model.remove(conditions, [callback])]

StudentModel.remove({num:1},function(err,docs){})//查找num为1的数据删除
```
- 改
```
//命令： [Model.update(conditions, doc, [options], [callback])]

StudentModel.update({num:1},{name:"cool"},function(err,docs){
})//将num为1的名字改为cool
```
- 查
```
//命令：[Model.find(conditions, [projection], [options], [callback])]
StudentModel.find({num: 1},{_id:0},function(err,docs){
})//查询num为1的数据，{_id:0}控制返回内容不包含_id

//查询一条数据
//StudentModel.findOne({num: 1},{_id:0},function(err,docs){})  
```
**好了，Mongoose和Mongodb简单入门就到这里结束了。如果有兴趣的同学可以自己查阅资料深入研究，文中如果出现错误还望指正。感谢各位看官**
![要饭.jpg](http://upload-images.jianshu.io/upload_images/3961611-2ed043bb32029e45.jpg?imageMogr2/auto-orient/strip%7CimageView2/2/w/1240)
