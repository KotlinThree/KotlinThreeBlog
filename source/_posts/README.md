title: Hexo 基本使用
date: 2015-11-06 00:12:36
tags: Hexo
---
Hexo https://github.com/hexojs/hexo
theme https://github.com/litten/hexo-theme-yilia
Hexo 基本使用 http://segmentfault.com/a/1190000002592993

写文章
```
hexo server 
hexo new post <title>
hexo new page <title>
```

<!--more-->

```
hexo clean #清除缓存 网页正常情况下可以忽略此条命令
 
hexo g #生成静态网页g=generate
hexo d #开始部署d=deploy

两步骤整一块 hexo g --d
```
 
多标签注意语法格式 如下:
    tags:
        - 标签1
        - 标签2
        - 标签3
        - etc...
想在首页文章预览添加图片可以添加photo参数 这个fancybox可以省略 如下:

    photos:
        - http://xxx.com/photo.jpg
正文中可以使用<!--more-->设置文章摘要 如下:

    以上是文章摘要
    <!--more-->
    以下是余下全文
more以上内容即是文章摘要，在主页显示，more以下内容点击『> Read More』链接打开全文才显示。

