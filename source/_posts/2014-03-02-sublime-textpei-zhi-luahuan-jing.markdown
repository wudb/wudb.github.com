---
layout: post
title: "Sublime text配置lua环境"
date: 2014-03-02 17:29
comments: true
categories: Lua
---
####1、Windows
配置lua的编译环境，在进行下面的步骤之前，应该先安装lua for windows.否则会编译错误.
</br>(1).点击工具栏，Tool->Build System->New Build System.
</br>(2).在新建的脚本文件中添加以下代码	
<pre>
{  
    "cmd": ["lua", "$file"],   //这里lua是路径
    "file_regex": "^(?:lua:)?[\t ](...*?):([0-9]*):?([0-9]*)",  
    "selector": "source.lua"  
}  
</pre>
(3).将文件保存为lua.sublime-build,保存到默认位置即可。</br>
(4).这个时候Tool->Build System，勾选lua为默认选项即可。

####2、Mac os x
mac os x下安装lua的环境的步骤:<br>
(1).去[lua官网](http://www.lua.org/download.html)下载lua的包。<br>
(2).终端下执行下面的命令<br>
<pre>shell
make macosx
sudo make install
lua   //测试是否成功，能进入lua模式就表明成功了
</pre>
然后剩下的步骤就跟windows的步骤一样了。
只是路径改成mac下得路径, eg:
<pre>
"cmd": ["/usr/local/bin/lua", "$file"],   //这里lua是路径
</pre>