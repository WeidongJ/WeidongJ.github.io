---
title: hexo + github搭建过程
date: 2019-05-01 10:21:04
tags:
    - hexo
    - github
    - 博客
categories:
    - 学习
    - git pages
---
# hexo + github 个人博客搭建
## 步骤
* GitHub配置
* hexo安装
* hexo配置及命令
* hexo

### git 配置
安装git，配置账户及ssh密钥，[Git教程][git_url]

github上创建git Pages项目，使用git托管项目名称为：*`用户名.github.io`*,项目权限必须是*public*;

### hexo 安装
安装[node.js][]
安装hexo，进入cmd执行安装命令：

    npm install -g hexo-cli
初始化hexo项目blog：创建一个Blog文件夹，cmd在该目录下执行

    hexo init blog
启动hexo 

    hexo g
    hexo s

部署hexo

    hexo d
tips：部署后打开站点修改可能不会立即生效，需要等待

### hexo 配置及命令
#### 站点配置，配置文件 `blog/_config.yml`

基本信息

    # Site
    title: PrueMat # 站点名称
    subtitle:
    description: 
    keywords:
    author: Weidong Ji
    language: zh-Hans # 中文
    timezone: Asia/Shanghai # 
    
主题：

    theme: next

部署：

    # Deployment
    ## Docs: https://hexo.io/docs/deployment.html
    deploy: # 部署配置
    type: git #部署方式
    repo: https://github.com/WeidongJ/WeidongJ.github.io.git # git
    branch: master

Tips:修改站点配置后需要执行clean命令才能生效：

    hexo clean
    hexo g
    hexo s

#### 主题配置（next）
使用命令clone next主题文件夹

    git clone https://github.com/iissnan/hexo-theme-next themes/next
修改样式，配置文件 `blog/themes/next/_config.yml` 

    # Schemes
    #scheme: Muse
    #scheme: Mist
    scheme: Pisces # 指定样式
    #scheme: Gemini

tips：非站点配置，一般不用执行clean命令

    hexo g
    hexo s

## 其他
### 迁移本地hexo文件及配置至git
cmd至Blog目录，把文件夹推送至项目 dev 分支

    git push git@github.com:WeidongJ/WeidongJ.github.io.git -b dev
问题：推送出去的next文件夹不可用

原因：next文件夹是git项目

解决方法：
1. 设置windows查看，显示隐藏项目，删除`.git`
2. 删除git上next文件夹
3. 重新 push

### 相关
hexo 使用介绍：[hexo文档][hexo_doc]
next 使用介绍：[next文档][next_doc]
markdown 使用介绍：[markdown文档][markdown_doc]
### 待学习
hexo 分享及页面定制，嵌入等

[git_url]: 'https://www.liaoxuefeng.com/wiki/0013739516305929606dd18361248578c67b8067c8c017b000' "廖雪峰 Git教程"
[node.js]: 'https://nodejs.org/en/download/' "Node.js"
[hexo_doc]: 'https://hexo.io/zh-cn/docs/' "hexo doc"
[next_doc]: 'https://theme-next.org/docs/' "next doc"
[markdown_doc]: 'http://www.markdown.cn/' "markdown doc"