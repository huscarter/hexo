---
title: github搭建自己的博客
date: 2018-03-09 114:29:44
categories: 技术周边
tags:
     - github
description: 身为程序员干嘛还要用人家的博客平台呢，用我们程序员自己打造的那不是极好
---

之前自己在CSDN和163上写过博客，后来出现了简书于是就转移到了简书。但是简书发生了一起侮辱程序猿的事件，想想觉得既然自己身为程序员干嘛还要用人家的博客平台呢，用我们程序员自己打造的那不是极好，于是使用了github+hexo自己去生成博客。现在自己购买了虚拟服务器和域名，这样以后可以更加自由的玩耍了！

## github仓库创建
github是开发人员的一个无价之宝，不仅提供了各种开源项目，其实github还具有博客搭建的功能。而且简单方便，它像维护项目一样去维护自己的博客。

1. 先自己去[github的官网](https://www.github.com)注册即可

2. 创建自己的博客仓库
这个仓库名称对格式有要求，必须是[用户名].github.io的形式(比如huscarter.github.io)，这样建好之后的博客地址就是https://huscarter.github.io

3. 接下来就是靠自己去创建静态页面展示自己博客的内容
创建一个index.html，将作为我们博客的首页。这种方式全部使用自己手写html，需要我们有一定的前端开发能力，而且做出来的页面可能不是很美观。下一个部分将介绍hexo（一个快速、简洁且高效的博客框架）。

## hexo博客搭建
[Hexo](https://hexo.io/)是一个快速、简洁且高效的博客框架。它使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。下面我们来看下如何使用hexo搭建博客。

1. hexo安装

- 安装[git](https://git-scm.com/)
直接去官网下载安装即可，不需要配置环境变量。
如果已经安装则跳过此步骤；如果是mac用户需要先安装xcode，并通过xcode（Preferences -> Download -> Command Line Tools -> Install）安装命令行工具。

- 安装[node.js](https://nodejs.org/en/)


## hexo关联github

