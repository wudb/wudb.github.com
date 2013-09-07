---
layout: post
title: "记录搭建octopress+github博客的过程"
date: 2013-09-07 14:53
comments: true
categories: Octopress
---
很早以前就知道github了, 就试过github page, 但是最终没有搭建成功这个博客, 开始辗转于csdn, 博客园等等网站写博客。前一段时间看见[破船之家](http://beyondvincent.com/)的一篇文章: [利用Octopress搭建一个Github博客](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/), 遂又起意, 于是就照着这篇文章的步骤开始了。     

此次, 博客搭建成功了, 于是写下搭建过程。下面所有的过程都是在mac os x上完成的。

####1. 安装ruby     
octopress是用ruby写的, 所以ruby的环境需要建立起来。    
安装rvm(ruby version manager)     
	
	curl -L https://get.rvm.io | bash -s stable --ruby 
接下来用rvm来安装ruby, 建议ruby的版本是1.9.3以上     
	
	rvm install 1.9.3
	rvm use 1.9.3
	rvm rubygems latest  
安装完以后可以查看ruby的版本信息    
***参考: [How to Install Xcode, Homebrew, Git, RVM, Ruby & Rails on Snow Leopard, Lion, and Mountain Lion](http://www.moncefbelyamani.com/how-to-install-xcode-homebrew-git-rvm-ruby-on-mac/)***

####2. 安装octopress   
在安装octopress之前, 请确定你的电脑上安装有git, 在mac os x上是自带有git的, 所以不用安装。其他平台请参考[Set Up Git](https://help.github.com/articles/set-up-git)。

然后把octopress从github上拷贝下来, 并进入到octopress目录, 建议大家在使用github的时候还是翻墙吧, 如果不懂翻墙, 可以去***[51vpn](http://a.wy002.com/290763)***注册一个账号,这个用起来比较简单, 有免费账号亦有收费的账号, 具体看自身情况。
	
	git clone git://github.com/imathis/octopress.git octopress
	cd octopress    # If you use RVM, You'll be asked if you trust 	the .rvmrc file (say yes).
接下来安装相关依赖bundler    

	gem install bundler
	bundle install
最后安装默认主题    

	rake install
####3. 配置octopress   
_config.yml是博客系统的配置文件, 在config.yml文件中有三大配置项：Main Configs、Jekyll & Plugins和3rd Party Settings。    
	
* Main Configs主要是配置url(你博客的地址), title(博客名称), subtitle(博客子标题), author(博客作者)      
* Plugins配置边栏中的最近评论, 最近文章等等    
* 3rd Party Settings配置一些第三方插件, 比如分享, 统计, 评论等等    

***参考: [octopress config](http://octopress.org/docs/configuring/)***

####4. 部署octopress    
首先你需要到[github](www.github.com)注册账号后, 创建一个仓库来存放octopress代码。
创建好仓库后, 就可以开始部署了:
	
	rake setup_github_pages
上面的命令会创建一个_deploy目录，目录用来存放部署到master分支的内容。期间会要求你输入仓库的url, 根据提示，进行输入即可。
接下来生成博客文件, 并将博客文件拷贝到_deploy目录下, 并提交到master分支下。
	
	rake generate
	rake deploy
此时你就可以访问你的博客<http://yourname.github.io/>了。如果你没有看见, 请等待几分钟。    
最后我们把source单独提交到source分支下    

	git add .
	git commit -m 'initial source origin'
	git push origin source

####5. 开始写博客     
octopress把所有博客文章都放在source/_post目录下, 当然不需要你去这个目录下创建文章, 你只要执行下面的命令, octopress就会帮你生成一篇文章在source/_post目录下
	
	rake new_post["你的文章名称"]
octopress生成的文章的文件名称遵循这样一个命名规则: YYYY-MM-DD-post-title.markdown, 日期加上文章名称。注意生成的文件是markdown格式的, 所以在写博客之前你需要熟悉markdown, 你可以参考***[Markdown语法说明](http://wowubuntu.com/markdown/#list)***。     
文章的开始是octopress生成的一些信息    

	---
	layout: post
	title: "记录搭建octopress+github博客的过程"
	date: 2013-09-07 14:53
	comments: true
	categories: Octopress
	---	
请注意冒号后面的空格, 这个空格必须存在, 不然生成的文章会有问题, 大家可以试试去掉空格。   
接下来就是提交新文章    
	
	rake generate
	git add .
	git commit -m "add new post"
	git push origin source
	rake deploy
当然在提交前, 你可以先在本地预览下
	
	rake generate
	rake preview
然后浏览器中打开<http://127.0.0.1:4000/>或<http://localhost:4000/>就可以看见自己新提交的文章了。    
如果未写好的文章也可以提交到github, 只要在文章头部加入下面的一段, 就不用担心会把文章发布出去。
	
	published: false  //改成true就变成发布状态了

####6. 其他配置    
[为octopress添加JiaThis](http://wudb.github.io/blog/2013/09/07/wei-octopresstian-jia-jiathis/)    
[为octopress添加评论和分类](http://wudb.github.io/blog/2013/09/03/wei-octopresstian-jia-ping-lun-he-fen-lei/)

####7. 小结   
本文介绍了利用octopress和github page来搭建一个博客系统。

***参考: [利用Octopress搭建一个Github博客](http://beyondvincent.com/blog/2013/08/03/108-creating-a-github-blog-using-octopress/)***