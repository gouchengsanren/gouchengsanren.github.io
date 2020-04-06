---
title: hexo的迁移
date: 2020-04-06 04:45:55
tags:
---
由于hexo deploy的是编译生成的网页静态文件（hexo init下的.git_deploy）。
当更换电脑，由于没有源文件，我们没法更新博文。
所以有必要同时对hexo源文件进行备份（其他电脑可随时获取），并能快速恢复hexo环境。
本文参考大多数人的做法，将hexo源文件备份至github。
<!--more-->

## 在github.io仓中新建hexo分支管理源码
- - -
为了便于管理、维护（hexo d和git push源码在一个目录下操作），我们复用github.io仓
，新建一个分支 `hexo` 用于源码的管理。
```
gouchengsanren.github.io - master（静态网页文件）
                         \
                           hexo（hexo源码）
```

### 创建hexo分支
{% img https://github.com/gouchengsanren/images/blob/master/blog/hexo%E7%9A%84%E8%BF%81%E7%A7%BB-%E5%88%9B%E5%BB%BAhexo%E5%88%86%E6%94%AF.png?raw=true %}


### 添加hexo源文件
拉下github.io的代码：
`git clone git@github.com:gouchengsanren/gouchengsanren.github.io.git`

删除 `.git` 以外的其他文件：
`rm -rf !(.git)`

拷贝hexo源文件到本目录：
```
cp -rf <hexo源文件根目录>/* .
cp <hexo源文件根目录>/.gitignore .
```

删除其他的 `.git` 目录（因为git不允许仓库嵌套）：
`find . -name .git`
通常theme下的主题是git clone下来的，需要删除。

添加并上传：
```
git add .
git commit -m "[ADD] 添加hexo源文件"
git push
```
<br>

## 本地快速恢复hexo环境
- - -
在新环境下：
```
git clone git@github.com:gouchengsanren/gouchengsanren.github.io.git
cd gouchengsanren.github.io.git
npm install
npm install hexo-deployer-git --save
```
至此，环境就恢复了。
<br>

## 需要提交的内容
- - -
除了 `hexo d` 提交网页静态文件到 `master` 分支。
还需要 `git add` 和 `git push` 源文件到 `hexo` 分支。



