---
layout: post
title: "为octopress添加评论和分类"
date: 2013-09-03 21:15
comments: true
categories:Octopress 
---
####为octopress添加多说评论
在自己博客里面侧边栏页面里面加了最近评论，方法如下:    
1.在source/_includes/asides创建一个comment.html(文件名随便起, 自己知道就行了)   
2.在comment.html中添加下段代码
	
	<section>
	<h1>最近评论</h1>
	<ul class="ds-recent-comments" data-num-items="10">
	</ul>
	<!--多说js加载开始，一个页面只需要加载一次 -->
	<script type="text/javascript">
	var duoshuoQuery = {short_name:"your_duoshuo_name"};
	(function() {
	var ds = document.createElement('script');
	ds.type = 'text/javascript';
	ds.async = true;ds.src = 'http://static.duoshuo.com/embed.js';
	ds.charset = 'UTF-8';
	(document.getElementsByTagName('head')[0] || 	document.getElementsByTagName('body')[0]).appendChild(ds);
	})();
	</script>
	<!--多说js加载结束，一个页面只需要加载一次 -->
	</section>

3.打开_config.yml 将comment.html添加到  
	  
	  page_asides: [asides/category_list.html, asides/comment.html, asides/github.html]

####为octopress添加分类
1.保存以下代码到plugins/category_list_tag.rb：  
	
	module Jekyll
  	class CategoryListTag < Liquid::Tag
    def render(context)
      html = ""
      categories = context.registers[:site].categories.keys
      categories.sort.each do |category|
        posts_in_category = context.registers[:site].categories[category].size
        category_dir = context.registers[:site].config['category_dir']
        category_url = File.join(category_dir, category.gsub(/_|\P{Word}/, '-').gsub(/-{2,}/, '-').downcase)
        html << "<li class='category'><a href='/#{category_url}/'>#{category} (#{posts_in_category})</a></li>\n"
      end
      html
    end
  	end
	end

	Liquid::Template.register_tag('category_list', Jekyll::CategoryListTag)
	
这个插件会向liquid注册一个名为category_list的tag，该tag就是以li的形式将站点所有的category组织起来。如果要将category加入到侧边导航栏, 需要增加一个aside。

2.增加aside
复制以下代码到source/_includes/asides/category_list.html。
	
	<section>
  	<h1>分类</h1>
  	<ul id="categories">
    {% category_list %}
  	</ul>
	</section>  

3.配置侧边栏需要修改_config.yml文件, 修改其default_asides项: 
	
	default_asides: [asides/category_list.html, asides/recent_posts.html]

参考: http://yrzhll.com/blog/2012/12/12/comment/    
http://codemacro.com/2012/07/18/add-category-list-to-octopress/
