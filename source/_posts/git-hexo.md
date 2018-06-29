---
title: 新环境 拉取源文件 新增博客
date: 2018-06-29 17:24:01
tags:
---

#  安装 nodejs 环境

#  使用nodejs 安装 hexo
```git
npm install -g hexo

hexo init  zhangyong(此处的zhangyong 是hexo创建的文件夹)

npm install hexo-deployer-git --save 安装 hexo 上传到博客的独有 git
```

# 在任意一个文件夹拉取github 源文件 其实只需要 .git 文件 将.git文件 复制到 刚 创建的 zhangyong 文件夹中

##下面就是git操作了

```git
git clean -df

git reset --hard  将文件返回GitHub版本
```

# 啦啦啦 好了  下面就是 hexo 自己的操作了