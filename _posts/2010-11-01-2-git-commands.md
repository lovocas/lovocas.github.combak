--- 
date: 2010-11-01 11:09:11 +08:00
layout: post
title: !binary |
    5Lik5Liq5pyJ55So55qER0lU5ZG95Luk

wordpress_id: 490
wordpress_url: !binary |
    aHR0cDovL3d3dy50b2J5Y24ub3JnL2IvP3A9NDkw

---
因为需要对现有的项目做一些新的尝试，所以我想在不确定是否保留这些新的尝试以及在必要的时候可以方便的回到当前的开发中来，我决定使用标签对开发标本进行标注，经过尝试，发现这种方法非常方便，而且高效。
在这之前先说一个远程分支的使用方法：git branch -r 查看远程分支，git checkout -b newbranch origin/master建立新分支newbranch，并同时读取远程分支到新分支newbranch。
关于标签的命令
1、git tag xxx -m 'tag log'
建立分支
2、git describe master
显示友好的自动版本，此时显示的是形如tagname-20-xxxxxx，由三部分组成，最近一次标定的版本号，加上自那次标定之后的提交次数，再加上一段SHA-1值of the commit you’re describing。
使用此命令有个前提，在做tag时，必须要有-m参数标注才可以。

tag标签可以在多个地方使用，比如git show|checkout等。
也可以直接使用标签直接进行打包，如

git archive master --prefix='project/' | gzip > `git describe master`.tar.gz

如果要打包成ZIP包，则可以

git archive master --prefix='project/' --format=zip > `git describe master`.zip
