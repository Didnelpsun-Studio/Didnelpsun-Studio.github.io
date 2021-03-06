---
layout: post
title:  "路由"
date:   2020-02-04 12:36:12 +0800
categories: notes jekyll base
tags: Jekyll 基础 路由 博文 主页面 自定义
excerpt: "路由引用与定义"
--- 

## 博文路由

如果要利用利用本站的其他博文，除了根据分类和日期来指定相对路径，还可以按照jekyll所给出的[博文链接](http://jekyllcn.com/docs/templates/)方法来转到对应的博文。

{% raw %}

{% post_url 文章名字 %}

如：{% post_url 2010-07-21-name-of-post %}

如果你使用了子文件夹来组织你的博文，你需要在路径中加入子文件夹：

{% post_url 相对于整个项目的_posts文件夹的url/文章名字 %}

<span style="color:red">警告：</span>当使用post_url标签时，不要写文件后缀名。

还可以用 Markdown 这样为你的文章生成超链接：

\[文字]({% post_url 链接 %})

{% endraw %}

&emsp;

## 主页面路由

如果你想使用某个文件夹中的资源，发现使用相对路径是无法实现的，因为经过编译后路径有些会有巨大的改变，原来的路径会出错，而我也不建议你使用网页上地址栏的绝对路径来获取，因为会不稳定。

Jekyll提供了\{\{ site_url }}来得到基站地址，如本站[基地址]({{ site_url }})，但是你点击会发现还是这个页面，因为这时候它的形成的a标签的href属性值为空，这时候浏览器不会认为你想到主页面而是认为这是个空值，所以它就只会刷新一遍，而如果你想到主页面去，就一定要使用\{\{ site_url \}\}，这时候你才会发现自己成果了。

&emsp;

## 自定义路由

这个属性之前就已经提供了，就是Jekyll的YUML变量，permalink，如果想自定义路由就需要在每个文档的开头定义好。

如果要使用分类而不想每个文档都定义，自动生成，就要在_config.yaml中配成的permalink: /:categories/:title.html，按分类/文章生成链接。
