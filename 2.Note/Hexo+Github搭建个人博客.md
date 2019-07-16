## hexo简介

hexo是一个简单、快速、强大的基于Github Pages的博客发布工具，支持Markdown格式，有众多优秀插件和主题。

官网： [http://hexo.io](http://hexo.io/)
github: https://github.com/hexojs/hexo

## 原理

由于github pages存放的都是静态文件，博客存放的不只是文章内容，还有文章列表、分类、标签、翻页等动态内容，假如每次写完一篇文章都要手动更新博文目录和相关链接信息，相信谁都会疯掉，所以hexo所做的就是将这些md文件都放在本地，每次写完文章后调用写好的命令来批量完成相关页面的生成，然后再将有改动的页面提交到github。

## 安装

```shell
npm install -g hexo	
```

## 初始化

找一个独立的文件夹，执行安装命令，hexo会自动生成一些文件到这个文件夹，包括node_modules。这个文件夹内容相当于你的blog的源码。

初始化完毕后，你可以通过hexo g 和hexo s生成并启动服务，可以再本地预览。

项目配置文件

## 配置
您可以在 _config.yml 中修改大部份的配置。

* 配置自动发布到主页；
* 配置网站icon、标题等
* 配置网站主题；

## Hexo常用命令

```shell
#常见命令
hexo new "postName" #新建文章
hexo new page "pageName" #新建页面
hexo generate #生成静态页面至public目录
hexo server #开启预览访问端口（默认端口4000，'ctrl + c'关闭server）
hexo deploy #部署到GitHub
hexo help  # 查看帮助
hexo version  #查看Hexo的版本
#缩写
hexo n == hexo new
hexo g == hexo generate
hexo s == hexo server
hexo d == hexo deploy
#组合命令
hexo s -g #生成并本地预览
hexo d -g #生成并上传
#设置文章摘要
<!--more-->
```











