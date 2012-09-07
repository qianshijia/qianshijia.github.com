---
layout: post
title: "给博客添加新浪微博侧边栏"
date: 2012-09-07 13:26
comments: true
categories: Blog 
---
今天闲得无聊，就想把博客的内容弄得再丰富一点。[Octopress](http://octopress.org)已经提供了丰富的侧边栏widget，比如Twitter，Google Plus，GitHub Repo等等，只需要在_config.yml里配置一下用户名就可以了。但是考虑到天朝强大的防火墙的存在，所以我准备把新浪微博的侧边栏给添加进来。  
新浪微博已经提供了可以定制的微博侧边栏叫[微博秀](http://http://weibo.com/tool/weiboshow)，这个工具就能够帮我们直接生成一个微博的侧边栏，非常方便。系统会自动给我们生成一个iframe，接下来我们就要把这个iframe添加到我们的博客侧边栏中去。  
首先，在博客所处的本地文件夹中找到source/_includes/asides，在这个文件夹下建立weibo.html。用编辑器打开这个文件，然后输入：  
<script src="https://gist.github.com/3662995.js"> </script>  

其中iframe那块代码就是刚才微博秀给我们生成的代码，不过为了能更方便地通过_config.yml来配置，我们用一些变量来替代了原有的值。然后到_config.yml中添加如下配置：

	# Sina Weibo
	# 微博uid和verifier，可以在iframe中的src参数里找到
	weibo_uid: 2277930531
	weibo_verifier: c773826d
	# 显示粉丝的行数：1-7行
	weibo_fansrow: 1
	# 是否显示缩略图
	weibo_picshow: true
	# 配色方案：新浪提供了1-10种不同的配色方案
	weibo_skin: 2
	# 是否显示微博内容
	weibo_showcontent: true
	# 是否在底部显示粉丝栏
	weibo_showfans: false
好了接下来只要generate然后deploy就可以了。