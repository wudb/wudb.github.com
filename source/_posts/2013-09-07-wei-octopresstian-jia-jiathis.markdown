---
layout: post
title: "为octopress添加JiaThis"
date: 2013-09-07 14:39
comments: true
categories: Octopress
---
为文章增加分享功能, 我们可以找些插件来添加到博客里面, 我就选择了[JiaThis](http://www.jiathis.com/), 大家也可以选择其他认为好的。     
1.在_config.yml中添加如下配置:   

	\#JiaThis
	jiathis: true

2.在source/_include/post/sharing.html添加从JiaThis获取的分享代码即可, 注意一定加在\</div>之前, jiathis的分享代码根据你在jiathis的配置不同而不同。