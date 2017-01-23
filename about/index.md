---
layout: page
title: About this Blog
tags: [about, Jekyll]
date: 2017-01-22
comments: false
---
    

## 我是如何搭建这个博客的

* **创建GitHub Pages**    
首先你要有一个github账户，再使用GitHub Pages建立博客。   
详细步骤请参考[GitHub Pages](https://pages.github.com) 

* **选择Jekyll模板**  
搜索Jekyll Theme，选择一个喜欢的模板，下载或者Fork都可以，然后部署到自己的电脑里。  

* **修改配置文件**  
找到_config.yml文件，按照自己的需求修改相应的参数，以此作为自己的博客。 

* **编辑博文**    
在_posts文件夹里创建博文，文件的命名格式应为：  
`yyyy-mm-dd-artical-title.md`或`yyyy-mm-dd-artical-title.html`这样的形式。

* **提交Git仓库**  
在终端输入如下命令提交： 
	`git add .`  
	`git commit -m "××××"`  
	`git push -u origin master`  

* **访问博客**  
访问`https://username.github.io`，就可以看到自己搭建的博客了。

* **安装Jekyll本地编译环境**  
在本地安装Jekyll编译环境可以在编辑博客后预览效果，以此避免频繁提交到Git的繁琐步骤。  
安装后在库的根目录使用命令`jekyll serve`，访问`http://localhost:4000`可以看到自己编辑的博客网站。  
详细步骤请参考[Pages 中使用 Jekyll](http://wiki.jikexueyuan.com/project/github-pages-basics/jekyll-page.html)

* **编写shell脚本**  
如果你觉得在终端里时常敲重复命令很麻烦，可以编写几个shell脚本，直接拖入terminal中运行即可。

## 博客预览

{% capture images %}
    https://cloud.githubusercontent.com/assets/754514/14509720/61c61058-01d6-11e6-93ab-0918515ecd56.png
    https://cloud.githubusercontent.com/assets/754514/14509716/61ac6c8e-01d6-11e6-879f-8308883de790.png
{% endcapture %}
{% include gallery images=images caption="Screenshots of Moon Theme" cols=2 %}


## 模板参考

想要学习如何安装和使用这个模板，请前往[Setup Guide](http://taylantatli.me/Moon/moon-theme/) 获得更多信息。
      
[Install Moon](https://github.com/TaylanTatli/Moon){: .btn}
