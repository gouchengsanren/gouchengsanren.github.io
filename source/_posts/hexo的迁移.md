---
title: hexo的迁移
date: 2020-04-06 04:45:55
tags:
---
由于hexo deploy的是编译生成的网页静态文件（hexo init下的.git_deploy）。
当更换电脑，由于没有源文件，我们没法更新博文。
所以有必要同时对hexo源文件进行备份（其他电脑可随时获取），并能快速恢复hexo环境。
本文参考大多数人的做法，将hexo源文件备份至github。

## 在github.io仓中新建hexo分支管理源码
为了便于管理、维护（hexo d和git push源码在一个目录下操作），我们复用github.io仓
，新建一个分支`hexo`用于源码的管理。
```
gouchengsanren.github.io - master（静态网页文件）
                         \
                           hexo（hexo源码）
```

