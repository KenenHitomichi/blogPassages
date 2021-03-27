---
title: MathJax无法动态加载的问题
date: 2021-03-27 23:55:21
categories:
	- front-end
tags:
	- javascript
	- LaTex
---

## 前言

这几天在编辑博客的时候，发现了一个比较严重的问题。主题自带的MathJax似乎无法检查到DOM加载完成后通过ajax新增的元素。这就导致了通过ajax进行页面切换的时候，文章里的公式不会显示的问题。考察了部分官方文档后，才终于解决了这个问题。

## 解决方案

其实解决方式也很简单，配置完MathJax的config的基础上（一般module都会默认配置完），在ajax动态加载完成的回调函数里加上这么两句：

```javascript
var element = document.getElementById(DOM_ID)
MathJax.Hub.Queue(["Typeset", MathJax.Hub, element])
```

这样MathJax就会自动检查新增的元素了。