--- 
date: 2010-06-29 22:39:56 +08:00
layout: post
title: 在svn中使用git

---
如果正在使用svn，打算换到git，又暂时不想放弃已有的svn代码库，可以选择git- svn。说一说我自己从svn到git的经验吧。

开始

安装最新版本的git，从git 1.5.3以后支持git-svn，git和svn的配合就要借助这个功能。

安装完毕后要做一些简单的配置。最直接的做法就是创建修改~/.gitconfig。下面是我的.gitconfig
{% highlight text %}
[user]
        name = Robin Lu
        email = ---@gmail.com
[color]
        diff = auto
        status = auto
        branch = auto
[alias]
  st = status
  rb = svn rebase
  ci = commit -a
  co = checkout
{% endhighlight %}
[user]部分标示出使用者的身份，你提交的代码会自动引用这一身份信息。[color]设置命令输出的颜色。[alias]部分可以简化一些常用命令，比如在这里将git status简化为git st。
初始化代码库

首先用git-svn来初始化本地的代码库(repository)
{% highlight bash %}
git svn clone -s svn-repository-url
{% endhighlight %}
svn-repository-url部分使用svn代码库的url。如果要从trunk目录或者某个branch目录里check out，要把-s换成-T、-b等选项。具体参看man git-svn。这个命令时间比较长，因为需要同步所有的提交历史，还好只此一次，以后不会这么慢了。做完这一步，在本地就有了一个完整的代码库，包括所有commit的历史和log，已经可以开始用它来进行开发工作了。

不过，在开始开发之前，最好先做一次垃圾搜集：
{% highlight bash %}
git gc
{% endhighlight %}
它对代码库的信息进行垃圾搜集和压缩，最明显的作用就是减小磁盘占用空间。第一次做效果尤其明显。

你可以检查一下代码库的状态：
{% highlight bash %}
git status
{% endhighlight %}
现在应该在一个叫”master”的分支(branch)上。

用这个命令来显示出所有的分支(branch):
{% highlight bash %}
git branch -a
{% endhighlight %}
master前有一个*号，代表你现在所处的分支，另外还有一个分支叫trunk，它是一个远程分支(remote branch)，对应的是远程svn代码库。master实际上是trunk的一个本地分支。

接下来，需要配置忽略文件，让git忽略一些目录中不希望加入代码库的文件，类似svn propset svn:ignore。全局有效的忽略文件列表可以添加在./.git/info/exclude文件中。比如我需要忽略所有vi产生的swp文件：

`.*.swp`

对于和目录有关的忽略文件设置可以在该目录下创建.gitignore，然后加入需要忽略的内容，比如我希望忽略根目录下的log,tmp等目录，可以直接在根目录下的.gitignore中加入:

    log
    tmp

开发流程

可以开始工作了。用git后开始养成一个新习惯，就是工作前先创建新分支:
{% highlight bash %}
git checkout -b new_branch
{% endhighlight %}
-b后是分支名，创建的同时，你要转到了新分支上。尽量保持master上没有未提交到svn的commit，这样随时都可以很容易的产生一个干净的分支。

接下来你可以写代码，修改文件或者添加文件。如果想看看修改了什么，可以用：
{% highlight bash %}
git diff
{% endhighlight %}
如果对某个修改不满意，希望恢复原状，可以使用:
{% highlight bash %}
git checkout path/filename
{% endhighlight %}
相当于svn revert

git引入一个索引(index)的概念，提交前，需要把要提交的文件加入到git索引(index)中：
{% highlight bash %}
git add path/filename1
git add path/filename2
...
{% endhighlight %}
然后提交
{% highlight bash %}
git commit -m "提交感言"
{% endhighlight %}
每次commit都是提交索引(index)中的内容。

如果要一次提交所有修改过的文件，可以一次性添加，然后提交
{% highlight bash %}
git add .
git commit -m "提交感言"
{% endhighlight %}
如果只是修改，并没有添加新文件，可以直接用下面的命令:
{% highlight bash %}
git commit -a -m "提交感言"
{% endhighlight %}
将被修改文件加入索引并提交，一次完成全过程。

在修改加入所索引后，如果想看看索引内容中都所了什么修改，可以用：

    git diff --cached

适合在提交前做最后的code review。

查看最近一次提交的内容，可以使用

git show

修改中随时查看当前代码库的状态：

    git status

相当于svn status

删除和移动某个文件：
{% highlight bash %}
git rm file
git mv file newfile
{% endhighlight %}
提交到svn

在完成了几轮工作后，要将本地内容提交到远程svn中，可以先让当前分支和远程svn同步：

    git svn rebase

然后将所有已经合并到master分支的本地修改提交到svn
{% highlight bash %}
git svn dcommit
{% endhighlight %}
如果在git svn rebase时发生代码冲突，需要先手动解决冲突，然后用git add将修改加入索引，然后继续rebase
{% highlight bash %}
git svn rebase --continue
{% endhighlight %}
缺点

最后说说这种工作方式的缺点。这个话题稍微复杂一点。

svn和git的工作原理毕竟不同，git对代码提交的非线性特性在svn中难以再现，如果使用了git-merge或者git-pull，再提交到svn，相关分支上的提交历史有可能无法体现在svn上。从svn的使用者的角度，无法辨别这是一个提交还是一次合并，所以在和svn协作过程中，尽量不要使用merge，或者说，尽量让代码库保持线性。

我的经验是，如果不在乎svn中是否反映出提交历史，使用merge也无妨。比如完成工作后，可以将工作分支合并到主分支中去：
{% highlight bash %}
git checkout master
git merge new_branch
{% endhighlight %}
先用checkout命令切换回master分支，然后将新分支中内容合并进来。然后在master分支上做git svn rebase和dcommit。从svn来看，这就是一个commit，new_branch上的提交历史在svn上体现不出来。(有例外情况，以后再讨论)。

还有一个解决办法是尽量保持git代码库的线性特征。比如在new_branch分支中，先和master做rebase，再合并到master分支中：
{% highlight bash %}
git rebase master
git checkout master
git merge new_branch
{% endhighlight %}
然后在master上做dcommit，就可以在svn代码库中看到完整的提交历史。

如果看到这已经有点头晕了，可以干脆不管它，就按照前面的做法，直接在你的工作分支里dcommit，等对非线性开发有一定了解再来看各种情况。

好了，基本上知道这些就可以干活了。
