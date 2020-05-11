---
title: github搭建自己的博客
date: 2018-03-09 14:29:44
categories: 技术周边
tags:
     - github
description: 身为程序员干嘛还要用人家的博客平台呢，用我们程序员自己打造的那不是极好
---

之前自己在CSDN和163上写过博客，后来出现了简书于是就转移到了简书。但是简书发生了一起侮辱程序猿的事件，想想觉得既然自己身为程序员干嘛还要用人家的博客平台呢，用我们程序员自己打造的那不是极好，于是使用了github+hexo自己去生成博客。现在自己购买了虚拟服务器和域名，这样以后可以更加自由的玩耍了！

## github仓库创建
github是开发人员的一个无价之宝，不仅提供了各种开源项目，其实github还具有博客搭建的功能。github搭建博客简单方便，它像维护项目一样维护自己的博客。

1. 首先自己去[github的官网](https://www.github.com)注册帐号

2. 创建自己的博客仓库
这个仓库名称对格式有要求，必须是[用户名].github.io的形式 (比如huscarter.github.io)，这样建好之后的博客地址就是 https://[用户名].github.io （比如 https://huscarter.github.io ）。

3. 接下来就是靠自己去创建静态页面(html)展示自己博客的内容
创建一个index.html，将作为我们博客的首页。这种方式全部使用自己手写html，需要我们有一定的前端开发能力，而且做出来的页面可能不是很美观。下一个部分将介绍如何使用hexo创建自己美观的博客。

## hexo博客搭建
[Hexo](https://hexo.io/)是一个快速、简洁且高效的博客框架。它使用 Markdown（或其他渲染引擎）解析文章，在几秒内，即可利用靓丽的主题生成静态网页。很多开发设计者提供了许多hexo的主题，我们可以很方便的将自己喜欢主题应用到我们的博客上，妈妈再也不用担心我写的博客样式很难看了。下面我们来看下如何使用hexo搭建博客。

1、 hexo安装

- 安装[git](https://git-scm.com/)
直接去官网下载安装即可，不需要其他配置。
如果已经安装则跳过此步骤；
如果是mac用户需要先安装xcode，并通过xcode（Preferences -> Download -> Command Line Tools -> Install）安装命令行工具。

- 安装[node.js](https://nodejs.org/en/)

- 安装hexo
所有必备的应用程序安装完成后，即可使用 npm 安装 Hexo
```
npm install -g hexo-cli
```

2、 hexo 的使用

- 建站
运行如下命令
```
hexo init <folder> // 初始化目录
cd <folder>
npm install // 创建hexo站点文件
```
运行成功之后会在目录下生成如下文件
```
├── _config.yml // 重要：全站的配置文件，比如全站的标题、作者、语言和分页等c 
├── package.json // 应用程序的信息,一般不用管
├── scaffolds // 模版文件夹
├── source // 重要：资源文件夹是存放用户资源的地方
|   ├── _about // 可在此目录下建立index.md用来写博客中的关于内容
|   └── _posts // 我们存放博客文件的目录
└── themes // 重要：存放博客主题的目录
```

- 创建第一篇博客
在_post书目录下建立文件the first blog.md，博客内容参照
```
title: 第一篇博客 // 本篇博客的标题
date: 2013-03-09 10:29:44 // 本篇博客创建的时间
categories: 
            - ANDROID // 类目
tags: 
      - BLOG // 标签
description: 博客模版 // 博客简介，用于博客列表的展示

正文内容 // 正文
```
[博客内容模版传送门](../template)

- 访问自己的博客
使用命令行工具进入到hexo的目录运行：
```
hexo s // hexo server 开启服务
```
启动服务成功在浏览器里输入 "http://localhost:4000/" 访问即可

- hexo 基础命令介绍 [官方文档](https://hexo.io/zh-cn/docs/commands.html)
```
hexo g // hexo generate 生成静态访问文件
hexo s // hexo server 开启服务，关闭服务使用快捷键 "control+c"
hexo clean // 清除缓存文件和已生成的静态文件，若您对站点的更改不生效，您可能需要运行此命令
hexo d // hexo deploy 部署网站
```

## hexo 主题应用
hexo 提供了很多漂亮的主题供我们使用，加入的方法也很简单。

1. 进入[hexo 主题网址](https://hexo.io/themes/)，点击自己喜欢的"主题名称"即可进入主题对应的github网址。

2. 安装主题的命令一般会在主题项目的README文件中有介绍。
参照命令
```
cd hexo // 进入你的hexo目录
git clone https://github.com/tufu9441/maupassant-hexo.git themes/maupassant // 通过git拉取主题到hexo/themes目录
npm install hexo-renderer-pug --save
npm install hexo-renderer-sass --save
_config.yml-->theme: maupassant // 将站点_config.yml中的themes配置成你拉取的主题名称(主题的目录名称)
```

3. 每个主题也有自己_config.yml文件用于控制主题的样式和内容

## hexo 关联 github
hexo 关联 github 的前提是你已经创建好了github博客仓库和安装好了hexo。

1. 安装hexo扩展插件
```
npm install hexo-deployer-git --save
```

2. 在_config.yml里配置github信息
```
## Docs: https://hexo.io/docs/deployment.html
deploy:
  type: git
  repo: https://github.com/xxx/xxx.github.io.git // 你的github博客仓库地址
  branch: master // 主分支
```

3. 将博客发布到github仓库
```
hexo g // 生成
hexo d // 部署
hexo d -g // 生成+部署(hexo g+hexo d)
```

4. 一些注意事项
- 因为source目录是我们的资源目录，部署发布到github也是将source的内容生成静态文件发布，所以如果你有单独想上传的文件请将它放到source目录下。
- 如果你需要配置自己的CNAME文件（购买了域名的小伙伴用得到），也请将CNAME文件放到source目录下。
- 建议最好在github上建立另外一个仓库用来上传这个hexo，当作hexo站点的版本控制；这样当更换电脑写博客时，只需要从github拉取文件覆盖即可。
