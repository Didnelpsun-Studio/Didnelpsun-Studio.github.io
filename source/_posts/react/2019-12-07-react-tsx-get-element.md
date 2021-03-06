---
layout: post
title:  "获取元素"
date:   2019-12-07 11:41:14 +0800
categories: notes react typescript
tags: React typescript Ethernal
excerpt: "获取DOM与自定义元素"
---

如果我们要获取元素，如果是使用js，那么会有如下几种方式：

原生js方法

1. document.getElementById

2. document.getElementsByName

3. document.getElementsByTagName

4. document.getElementsByClassName

5. document.documentElement

6. document.body

7. document.querySelector

8. document.querySelectorAll

而对于react的jsx来说又有三大类获取dom元素的方法：

1. 以上的js原生方法

2. react原生函数findDOMNode

3. 通过ref来定位一个组件

但是对于tsx中这写方式会因为类型而发生错误：不能将类型`HTMLElement | null`分配给类型"HTMLDivElement"，而我们只需要将获取到的元素进行类型转换就行了。

<span style="color:aqua">格式：</span>`const 变量名 = document.getElementBy...("值") as HTMLElement;`/`(ReactDom.findDOMNode(变量名)as HTMLElement`
