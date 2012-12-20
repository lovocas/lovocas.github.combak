---
layout: post
title: Git的远程分支和远程tag管理
tags:
- branch
- git
- tag
- 系统管理
- 编程资料
---
建立分支
  >git branch branchname # 建立分支branchname
  >git push                             \# 将最新版本及分支信息推送到origin

获取分支
  >git branch -r                        \# 显示远程分支
  >git checkount -b localbranch remotebranch 
  >\# 从远程分支remotebranch建立本地分支localbranch
  >\# 一般remotebranch格式为origin/branchname

删除分支
  >git push origin :branchname

建立TAG
  >git tag tagname -m 'comment'          \# 最简单的建立本地TAG
  >git push origin refs/tags/tagname     \# 把本地TAG tagname推送到origin

获取TAG
  >git pull                              \# 执行此命令获得最新版本，TAG信息会同时加载

删除TAG
  >git push origin :refs/tags/tagname

