---
layout: post
title:  "文档样式"
date:   2020-01-20 14:41:31 +0800
categories: notes jekyll base
tags: Jekyll 基础 文档 样式
excerpt: "博客posts文档样式"
--- 

将文档结构完成了，我感觉我的博客的框架就大致搭建起来了，其他的一些问题就是小事了。在这个时候我以经完成了包括主页、emotion页面、notes页面、share页面的构建，接下来只用将文档按格式写好放到_posts文件夹中就行了。

不过你将文档中放进去就会发现你的文档基本都是灰白的，这是为什么呢？是因为我们还没有为这些文档设置样式。  

我们使用vscode编写markdown文件时会发现显示的比较好看，但是实际上它并不是实际上的原本的markdown文档的样子，而是被vscode美化过后的样子。  

我们现在需要做的就是美化markdown样式，因为jekyll已经将将markdown文档转换为html元素了，所以我们只用在对应的post模板中设置css样式就可以了。  

首先可以将\{\{page.title}}等变量放在页面中。然后是对整个页面的布局进行设计，一般是使用flex布局。可以使用行排序，也可以使用列排序，这个倒是不用特别讲究，都是可以按照默认的来使用。  

一般的对应关系是：  

| markdown | html |
| :------: | :--: |
|    \#    | \<h1>|
|   \##    | \<h2>|
|   \###   | \<h3>|
|  \####   | \<h4>|
|  \#####  | \<h5>|
| \######  | \<h6>|
|   word   | \<p> |
|\[name](url "title")|\<a>|
|  \<url>  | \<a> |
|\`\`\`code\`\`\`|\<div class="language-语言 highlighter-rouge">\<div class="highlight">\<pre class="highlight">\<code></code></pre></div></div>|
| \+ | \<ul>\<li> |
| \- | \<ul>\<li> |
|   \*   |\<ul>\<li>|
|\1. ...|\<ol>\<li>|
|\!\[alt](url "title")|\<img>|
|  \-\-\-  | \<hr> |
|  \***   | \<hr> |
| \<font> |\<font>|
|\**word**|\<strong>word</strong>|
| \*word* |\<em>word</em>|
|\***word***|\<strong>\<em>word</strong></em>`|
|\~~word~~|\<del>word</del>`|
|\>|\<blockquote>|
|\|thead\||\<table>\<thead>\<tr>\<th>|
|\|tbody\||\<table>\<tbody>\<tr>\<td>|

因为流程图vscode和jekyll都不支持，所以也不用多解释。  

还有一点值得注意的是我不是很喜欢jekyll所给的高亮语法，所以我基本都不会使用这种语法，取而代之是，我看到code元素中jekyll会渲染不同的代码块为不同的class类型，所以我在css样式中为不同的code类型设置了不同的颜色，我觉得比jekyll提供的方法更自由。

我的博客大部分功能已经实现，我觉得运用以上一些功能就可以解决大部分问题。
