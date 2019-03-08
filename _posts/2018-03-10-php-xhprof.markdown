---
layout: post
title:  "php56 xhprof性能测试扩展"
date:   2018-03-10 10:43:59
author: Ren Xin Qiang
categories: Recursion
tags:	function 
cover:  "/assets/instacode.png"
---

### 环境信息
* OS 10.13
* PHP 5.6.*
* php56-xhprof扩展
* graphviz 2.40.1服务
* homebrew工具
* nginx
* nginx 解析目录/www

#### 安装homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

基于ruby开发的包管理工具
类似于linux的rpm,yum软件包管理工具

#### 安装php56
安装php56环境是使用xhprof前提条件
请查看[https://renxinqiang.github.io/recursion/2018/03/10/php-evn.html](https://renxinqiang.github.io/recursion/2018/03/10/php-evn.html)


#### 安装php56-xhprof扩展
* `brew install php56-xhprof`
* `brew info php56-xhprof` 查看安装目录,后面有用

#### 配置php.ini 
* php配置文件brew安装一般在/usr/local/etc/php/5.6/php.ini
* 加入xhprof扩展输入目录
  {% highlight javascript %}
  [xhprof]
  xhprof.output_dir=/www/xhprof
  {% endhighlight %}
* 重启php `brew services restart php56`
* 查看是否生效 `php -m | grep xhprof`

#### 安装graphviz服务
这是保证在xhprof分析完程序后生成图片的服务,可视化查看程序慢的地方,后面会有例子
* `brew install graphviz`


#### 使用前准备
* 添加软连接到nginx解析的目录`ln -s /usr/local/Cellar/php56-xhprof/254eb24/xhprof_html /www/xhprof_html`
* 建立xhprof输出目录 `cd /www` `mkdir xhprof` 为了性能结果输出存放文件用的

准备工作做完之后就可以写个程序试一下了

#### 测试程序
/www/test/test.php
{% highlight javascript %}
function foo($data){
    if($data%50==0){
        ;
    }
}


function test(){
    for($i=1;$i<10000;$i++){
        foo($i);
    }
}

//启动xhprof
xhprof_enable(XHPROF_FLAGS_CPU + XHPROF_FLAGS_MEMORY);

//调用foo函数，也是我们要分析的函数
test();

//停止xhprof
$xhprof_data = xhprof_disable();

//取得统计数据
print_r($xhprof_data);

$XHPROF_ROOT = "/usr/local/Cellar/php56-xhprof/254eb24"; // 此路径为xhprof安装目录
include_once $XHPROF_ROOT . "/xhprof_lib/utils/xhprof_lib.php";
include_once $XHPROF_ROOT . "/xhprof_lib/utils/xhprof_runs.php";

//保存统计数据，生成统计ID和source名称
$xhprof_runs = new XHProfRuns_Default();
$run_id = $xhprof_runs->save_run($xhprof_data, "xhprof_foo"); //source名称是xhprof_foo

//弹出一个统计窗口，查看统计信息
echo "<script language='javascript'>window.open('../xhprof_html/index.php?run=" . $run_id . "&source=xhprof_foo');</script>";
{% endhighlight %}

#### 见证奇迹的时刻
程序准备好了
1. 打开页面[http://localhost/test/test.php](http://localhost/test/test.php)(会跳转一个页面)
![打印的数据](/assets/xhprof1.jpeg)
2. 跳转到[http://localhost/xhprof_html/index.php?run=5aa35f4525a3d&source=xhprof_foo](http://localhost/xhprof_html/index.php?run=5aa35f4525a3d&source=xhprof_foo)
![数据页面](/assets/xhprof3.jpeg)
3. 图形界面[http://localhost/xhprof_html/callgraph.php?run=5aa35792583ac&source=xhprof_foo](http://localhost/xhprof_html/callgraph.php?run=5aa35792583ac&source=xhprof_foo)
![图形效果](/assets/xhprof2.jpeg)
