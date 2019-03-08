---
layout: post
title:  "PHP源码配置文件解读"
date:   2018-09-10 11:33:59
author: Ren Xin Qiang
categories: Recursion
tags:	function 
cover:  "/assets/instacode.png"
---

    这篇文章主要讲述php源码文件结构及配置文件的相关语言或技术

首先我们先从官网下载一个源码包
[http://www.php.net/downloads.php](http://www.php.net/downloads.php)    
官网的地址,随便下载个7.1.1的版本玩  
下载完tar解压下或者鼠标双击下  
先看下目录结构
```
.
├── TSRM        // zend和php相关线程安全管理目录
├── Zend        // 引擎相关内容,宏定义等
├── build       // 编译相关配置文件
├── ext         // 扩展库,例如curl,mysql等. /ext/standor包含标准扩展,例:array
├── main        // 主要声明或者头文件,宏定义
├── pear        // 扩展应用目录,安装pear的核心文件等
├── sapi        // 接口服务,例cli,fpm,cli,cgi,apache的so模块等
├── scripts     // linux的工具配置文件,例phpize,php-config
├── tests       // 测试集
└── win32       // windows编译时有关的脚本
```

简单介绍完目录结构我们需要编译php文件  
configure是编译文件最主要的配置文件  
下面介绍下最基本的参数  
`--prefix=/usr/local/php` 指定安装目录,就像windows安装软件到哪  
`--enable` 用于安装指定功能,就像图形界面,需要勾选指定功能是一个道理,例`--enable-fpm`  
`--with-config` 用于使用指定配置文件,例:自己编辑扩展时可用  

### 扩展配置文件

进到ext/文件  
我们会看到许多熟悉的PHP函数哈~  
其实这些都是php扩展,在编译完php时就会将这些函数静态编译  
我们可以通过php -m查看扩展model  
怎样编写自己的扩展函数?以后会有文章介绍    

随便进入一个函数```cd curl```
咱们tree一下:
```$xslt
.
├── CREDITS         // 作者,贡献者 
├── config.m4       // Unix构建配置文件
├── config.w32      // windows构建配置文件
├── curl_file.c     // curl文件操作函数
├── interface.c     // curl服务接口
├── multi.c         // curl多进程实现
├── package.xml     // 支持服务的说明和支持文件的介绍
├── php_curl.h      // curl定义和声明的宏
├── share.c         // share相关函数
└── tests           // 测试集
```

config.m4 文件是宏语言编写,GNU Autoconf语法  
详细请看[https://www.gnu.org/software/autoconf/manual/](https://www.gnu.org/software/autoconf/manual/)  

