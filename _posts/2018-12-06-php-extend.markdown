---
layout: post
title:  "PHP扩展浅谈"
date:   2018-12-06 15:33:59
author: Ren Xin Qiang
categories: Recursion
tags:	function 
cover:  "/assets/instacode.png"
---

    这篇文章主要讲述php扩展开发,给想要尝试php扩展开发的同胞一个入口.需要一定的C语言及ZEND引擎了解

# 环境
1. Mac OS 10.13.2 
2. 源码php7.1.1
3. php环境php7.1.18
4. gcc 4.9
5. g++ 4.9
6. ./configure --with=php-config = /usr/local/Cellar/php@7.1/7.1.18_1/bin/php-config 

### 为什么要写PHP扩展?

1. 第一个理由是：PHP需要支持一项她还未支持的技术。这通常包括包裹一些现成的C函数库，以便提供PHP接口。例如，如果一个叫FooBase的数据库已推出市场，你需要建立一个PHP扩展帮助你从PHP里调用FooBase的C函数库。这个工作可能仅由一个人完成，然后被整个PHP社区共享（如果你愿意的话）。
2. 第二个不是很普遍的理由是：你需要从性能或功能的原因考虑来编写一些商业逻辑。

### 怎么开发PHP扩展?

   这个问题我在开始的时候一直摸不到头脑,边学习C语言边看PHP源码,虽然看不太懂,也是去网上查资料,硬着头皮看,在看zend引擎的时候很迷茫,因为网上没有很好的zend API函数文档可看,只能猜.更不要说扩展了
   
   现在好了,鸟哥给出了如何用C/C++开发php扩展[http://www.laruence.com/2009/04/28/719.html](http://www.laruence.com/2009/04/28/719.html) 
   原来官方直接给出了扩展搭建骨架供我们使用
   
   查看[https://renxinqiang.github.io/recursion/2018/09/10/php-config.html](https://renxinqiang.github.io/recursion/2018/09/10/php-config.html) 了解源码及扩展源码
  
### 使用官方提供的扩展骨架开发

先上原理
![扩展图](/assets/extend.jpg)

这个是鸟哥博客的扩展原理图,使用文件来创建扩展骨架.
下面是`--extname`创建扩展骨架

1. 下载php源码(和自己php环境一致版本)
2. 进到源码 ext/文件里
3. `./ext_skel --extname=lovephp`
4. `cd lovephp`
5. 修改config.m4文件,将PHP_ARG_WITH往下两行前面的dnl去掉
6. `phpize` 检测环境
7. `./configure --with-php-config=自己php环境的php-config`  配置处理
8. `make && make install` 编译这个扩展
9. 找到当前php环境ini添加.so文件,lovephp.so文件在module中   extension=lovephp.so
10. 测试 `php -d enable_dl=On myfile.php` 或者 `php -m | grep lovephp`
11. 修改lovephp.c文件 添加
```
PHP_FUNCTION(helloWorld)
{
       php_printf("Hello World!\n");
       RETURN_TRUE;
}
``` 
12. ```
找到函数zend_module_entry
在里面添加 PHP_FE(helloWorld, NULL)
```
13. 重新编译 `./configure && make && make install`

14. 输出 `php -r "helloWorld();"`

### 扩展骨架介绍
1. PHP_MINIT_FUNCTION  php加载时加载一次,初始化资源或常量,可常住内存,随时调用
2. PHP_RINIT_FUNCTION  请求时加载,初始化php变量等!(可多个)
3. PHP_RSHUTDOWN_FUNCTION  进行GC操作,各种释放内存,注销变量等等操作
4. PHP_MSHUTDOWN_FUNCTION  进行模块销毁,此次进程内该模块销毁,再做一次进程内申请的内存回收,此次不回收则会导致内存泄漏
5. PHP_MINFO_FUNCTION  提供给phpinfo查看扩展信息使用

再上个扩展骨架运行流程图

多线程
![多线程](/assets/moreThred.jpeg)
多进程
![多进程](/assets/moreProcess.jpeg)

### 总结
到此我们的扩展已经很简单很简单的完成了,只限制于理解动态PHP扩展的运行原理,我们需要继续学习C语言来加强Zend引擎的理解.ZEND线程安全,内存回收,都是以后
开发商业逻辑业务必备基础.需恶补C语言和ZEND引擎

鸟哥心得[http://www.laruence.com/2009/04/28/719.html](http://www.laruence.com/2009/04/28/719.html)

彩蛋:php扩展还是有使用C++框架开发,PHP-CPP框架可以直接编译扩展到环境中,可以了解下 官网[http://www.php-cpp.com/](http://www.php-cpp.com/)

 
