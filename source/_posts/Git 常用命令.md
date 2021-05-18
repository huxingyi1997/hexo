---
title:  Git 常用命令
date:  2021-04-04 10:30:00
categories: 
- 计算机基础知识
tags:
- git
- 常用命令
---
Git是目前世界上最先进的分布式版本控制系统，方便我们管理代码的版本，详细的介绍可以参考[廖雪峰的Git教程](https://www.liaoxuefeng.com/wiki/896043488029600)，本文总结常见的Git命令，想要熟悉这些命令，可以通过 [Learning Git Branching](https://learngitbranching.js.org/)这个网站，这是一个有趣的教程，在沙盒里执行相应的Git命令，还能看到每个Git命令的执行情况，像玩有些一样通过一系列的关卡，逐步学习 Git 的强大功能。最后增加了一个大大整理的教程，让人觉得git如此有趣。

<!-- more -->

### 配置操作

#### 全局配置

```
git config --global user.name '你的名字'
git config --global user.email '你的邮箱'
```

#### 当前仓库配置

```
git config --local user.name '你的名字'
git config --local user.email '你的邮箱'
```

#### 查看 global 配置

```
git config --global --list
```

#### 查看当前仓库配置

```
git config --local --list
```

#### 删除 global 配置

```
git config -unset --global 要删除的配置项
```

#### 删除当前仓库配置

```
git config --unset --local 要删除的配置项
```



### 本地操作

#### 查看变更情况

```
git status
```

#### 将当前目录及其子目录下所有变更都加入到暂存区

```
git add .
```

#### 将仓库内所有变更都加入到暂存区

```
git add -A
```

#### 将指定文件添加到暂存区

```
git add 文件1 文件2 文件3
```

#### 比较工作区和暂存区的所有差异

```
git diff
```

#### 比较某文件工作区和暂存区的差异

```
git diff 文件
```

#### 比较暂存区和 HEAD 的所有差异

```
git diff --cached
```

#### 比较某文件暂存区和 HEAD 的差异

```
git diff -cached 文件
```

#### 比较某文件工作区和 HEAD 的差异

```
git diff HEAD 文件
```

#### 创建 commit

```
git commit
```

#### 将工作区指定文件恢复成和暂存区一致

```
git checkout 文件1 文件2 文件3
```

#### 将暂存区指定文件恢复成和 HEAD 一致

```
git reset 文件1 文件2 文件3
```

#### 将暂存区和工作区所有文件恢复成和 HEAD 一样

```
git reset --hard
```

#### 用 difftool 比较任意两个 commit 的差异

```
git difftool 提交1 提交2
```

#### 查看哪些文件没被 Git 管控

```
git ls-files --others
```

#### 将未处理完的变更先保存到 stash 中

```
git stash
```

#### 临时任务处理完后继续之前的工作

- pop 不保留 stash
- apply 保留 stash

```
git stash pop
```

```
git stash apply
```

#### 查看所有 stash

```
git stash list
```

#### 取回某次 stash 的变更

```
git stash pop stash@{数字n}
```

#### 优雅修改最后一次 commit

```
git add.
git commit --amend
```



### 分支操作

#### 查看当前工作分支及本地分支

```
git branch -v
```

#### 查看本地和远端分支

```
git branch -av
```

#### 查看远端分支

```
git branch -rv
```

#### 切换到指定分支

```
git checkout 指定分支
```

#### 基于当前分支创建新分支

```
git branch 新分支
```

#### 基于指定分支创建新分支

```
git branch 新分支 指定分支
```

#### 基于某个 commit 创建分支

```
git branch 新分支 某个 commit 的 id
```

#### 创建并切换到该分支

```
git checkout -b 新分支
```

#### 安全删除本地某分支

```
git branch -d 要删除的分支
```

#### 强行删除本地某分支

```
git branch -D 要删除的分支
```

#### 删除已合并到 master 分支的所有本地分支

```
git branch --merged master | grep -v '^\*\| master' | xargs -n 1 git branch -d
```

#### 删除远端 origin 已不存在的所有本地分支

```
git remote prune orign
```

#### 将 A 分支合入到当前分支中且为 merge 创建 commit

```
git merge A分支
```

#### 将 A 分支合入到 B 分支中且为 merge 创建 commit

```
git merge A分支 B分支
```

#### 将当前分支基于 B 分支做 rebase，以便将B分支合入到当前分支

```
git rebase B分支
```

#### 将 A 分支基于 B 分支做 rebase，以便将 B 分支合入到 A 分支

```
git rebase B分支 A分支
```



### 变更历史

#### 当前分支各个 commit 用一行显示

```
git log --oneline
```

#### 显示就近的 n 个 commit

```
git log -n
```

#### 用图示显示所有分支的历史

```
git log --oneline --graph --all
```

#### 查看涉及到某文件变更的所有 commit

```
git log 文件
```

#### 某文件各行最后修改对应的 commit 以及作者

```
git blame 文件
```



### 标签操作

#### 查看已有标签

```
git tag
```

#### 新建标签

```
git tag v1.0
```

#### 新建带备注标签

```
git tag -a v1.0 -m '前端食堂'
```

#### 给指定的 commit 打标签

```
git tag v1.0 commitid
```

#### 推送一个本地标签

```
git push origin v1.0
```

#### 推送全部未推送过的本地标签

```
git push origin --tags
```

#### 删除一个本地标签

```
git tag -d v1.0
```

#### 删除一个远端标签

```
git push origin :refs/tags/v1.0
```

### 远端交互

#### 查看所有远端仓库

```
git remote -v
```

#### 添加远端仓库

```
git remote add url
```

#### 删除远端仓库

```
git remote remove remote的名称
```

#### 重命名远端仓库

```
git remote rename 旧名称 新名称
```

#### 将远端所有分支和标签的变更都拉到本地

```
git fetch remote
```

#### 把远端分支的变更拉到本地，且 merge 到本地分支

```
git pull origin 分支名
```

#### 将本地分支 push 到远端

```
git push origin 分支名
```

#### 删除远端分支

```
git push remote --delete 远端分支名
```

```
git push remote :远端分支名
```



## [git干货，花整整两天吐血整理](https://www.nowcoder.com/discuss/433766)

### 0.精髓总结 

 **让本地开发分支跟远程实现关联，是很棒的一件事！可以省掉很多参数，利用关联性实现简写。** 

```
git pull 等于 git fetch + git merge FETCH_HEAD
git push  //啥都不加相当于把HEAD所在分支push到远程关联分支。 git pull //啥都不加也相当于把HEAD所在分支对应的远程分支拉到HEAD所在分支并merge git pull --rebase //啥都不加也同上
git push <远程主机名> <本地分支名>：<远程分支名> 如果省略远程分支名，则表示将本地分支推送至与之存在“追踪关系”的远程分支（通常两者同名），如果该远程分支不存在，则会被新建：git push origin master
```

### 1.Git中的‘HEAD’是什么？ 

 **又名检出（checkout）位置** 

 **问题来源** 

 git 恢复文件到初始状态的命令： 

```
$ git reset HEAD <file>
```

 git 展示提交日志命令： 

```
$ git log commit c4f9d71863ab78cfca754c78e9f0f2bf66a2bd77 (HEAD -> master)
```

 在这些命令中常常会看到HEAD这个名词，它指的是什么呢？ 

 **回答** 

  这要从git的分支说起， **git 中的分支，** 本质上仅仅 **是个指向 commit 对象的可变指针** 。git 是如何知道你当前在哪个分支上工作的呢？   其实答案也很简单，它保存着一个 **名为 HEAD 的特别指针** 。在 git 中， **指向你正在工作中的本地分支** ，可以将 HEAD 想象为当前分支的别名。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420170433.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420171338.png)

### 2.合并分支的两种方法：git merge & git rebase 

 **git merge** 

  git merge 在 Git 中合并两个分支时会产生一个特殊的提交记录，它有两个父节点。翻译成自然语言相当于：“我要把这 **两个父节点本身及它们所有的祖先** 都包含进来。” 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420171930.png)  

**git rebase** 

 Rebase 实际上就是取出**一系列(整条分支)**的提交记录，**“复制”它们，然后在另外一个地方逐个的放下去**。 

  Rebase 的优势就是 **可以创造更线性的提交历史** ，这听上去有些难以理解。如果只允许使用 Rebase 的话，代码库的 **提交历史将会变得异常清晰** 。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420172100.png)  

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420172527.png) 

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420172559.png) 

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420173100.png) 

 在开发社区里，有许多关于 merge 与 rebase 的讨论。以下是关于 **rebase 的优缺点**： 

 优点: 

-  Rebase 使你的**提交树变得很干净, 所有的提交都在一条线上** 

 缺点 

-  Rebase **修改了提交树的历史** 

 比如, 提交 C1 可以被 rebase 到 C3 之后。这看起来 C1 中的工作是在 C3 之后进行的，但实际上是在 C3 之前。 

 **一些开发人员喜欢保留提交历史，因此更偏爱 merge。**而其他人（比如我自己）可能更喜欢干净的提交树，于是偏爱 rebase。仁者见仁，智者见智。

### 3.什么是fast-forward 和no-fast-forward？ 

 从一个分支获取变更到另一个分支的方式之一是执行git merge命令。Git 有两类合并操作：**fast-forward** 和**no-fast-forward**。 

 这么说你可能没什么概念，我们来看看区别吧。 

####  **fast-forward (--ff)** 

  如果当前分支与即将合并过来的分支相比，没有额外的提交，这种就是 fast-forward 合并。Git 很会偷懒，它会首先尝试最简单的方案，即 fast-forward 。这种合并方式不会创建新的提交，只是把另一个分支的提交记录直接合并到当前分支。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420173351.gif)

  fast-forward merge 

#### **no-fast-foward (--no-ff)** 

 跟即将合并过来的分支比较，当前分支如果没有额外的提交，这固然很好，但实际情况往往不是这样！如果我们在当前分支上也提交了一些改动，那么 Git 就会执行no-fast-forward合并。 

  对于  no-fast-forward  合并，Git 会在当前分支上创建一个新的 **合并提交** 。该提交的父提交同时指向当前分支和合并过来的分支。 

  ![img](https://uploadfiles.nowcoder.com/images/20200529/933639781_1590755612864_38630241A8024EA006101538D9D640D5) 

  no-fast-forward 

 也没毛病！现在master分支上有了我们在dev分支上做的所有变更。

### 4.分离HEAD的方法: git checkout  

```
git checkout  提交记录的哈希值(前缀)/HEAD的相对引用  HEAD^代表HEAD的上一次提交 HEAD^^代表HEAD的上上次提交 HEAD~6代表HEAD的第前六次提交 HEAD~不加数字也代表HEAD的上一次提交  //敲黑板 HEAD^2的意思不一样，是第二个父亲，而HEAD~2的意思是父亲的父亲（爷爷）
```

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420173613.png)

作者：心有林夕yummy
链接：https://www.nowcoder.com/discuss/433766?source_id=profile_create_nctrack&channel=-1
来源：牛客网



### 5.相对引用 

 我使用相对引用最多的就是移动分支。可以直接使用 -f 选项**让分支指向另一个提交**。例如: 

```
git branch -f master HEAD~3
```

  上面的命令会将 master 分支强制指向 HEAD 的第 3 级父提交。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420174612.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420174634.png)

### 6.撤销提交的两种方法：git reset & git revert 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420214815.png)


 虽然在你的本地分支中使用 git reset 很方便，但是这种“改写历史”的方法对大家一起使用的远程分支是无效的哦！**想要让远程分支改写历史要怎么办呢？使用 git revert <commit> 可以撤销指定的提交**， 要撤销一串提交可以用 <commit1>..<commit2> 语法。 注意这是一个前开后闭区间，即不包括 commit1，但包括 commit2。

```
git revert HEAD 意思是撤销HEAD指向的这个提交
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420215516.png)

### 7.整理提交记录:git cherry-pick & git rebase -i

开发人员有时会说“我想要把这个提交放到这里, 那个提交放到刚才那个提交的后面”, 而接下来就讲的就是它的实现方式，非常清晰、灵活，还很生动。看起来挺复杂, 其实是个很简单的概念。 

####  第一个命令是 git cherry-pick 

-  git cherry-pick <提交号>... 

  如果你想将一些提交复制到当前所在的位置（ HEAD ）下面的话， Cherry-pick 是最直接的方式了。我个人非常喜欢  cherry-pick ，因为它特别简单。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420215552.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420215639.png)

当你知道你所需要的提交记录（**并且还知道这些提交记录的哈希值**）时, 用 cherry-pick 再好不过了 —— 没有比这更简单的方式了。 

 *多个commit 只需要git cherry-pickcommitid1..commitid100* 

 注意，不包含第一个commitid ， 即 **git cherry-pick (commitid1..commitid100]** 

 如果想搞成[]区间，使用 git cherry-pick A^..B 相当于[A B]包含A 

 但是如果你不清楚你想要的提交记录的哈希值呢? 幸好 Git 帮你想到了这一点, 我们可以利用交互式的 rebase —— 如果你想从一系列的提交记录中找到想要的记录, 这就是最好的方法了 

####  第二个命令是 交互式的rebase 

 交互式 rebase 指的是使用带参数 --interactive 的 rebase 命令, 简写为 -i 

```
git rebase -i HEAD~4
```

 如果你在命令后增加了这个选项, Git 会打开一个 UI 界面并列出将要被复制到目标分支的备选提交记录，它还会显示每个提交记录的哈希值和提交说明，提交说明有助于你理解这个提交进行了哪些更改。 

 在实际使用时，所谓的 UI 窗口一般会在文本编辑器 —— 如 Vim —— 中打开一个文件。 

 **当 rebase UI界面打开时**, 你能做3件事: 

-  调整提交记录的顺序（通过鼠标拖放来完成） 
-  删除你不想要的提交（通过切换 pick 的状态来完成，关闭就意味着你不想要这个提交记录） 
- ​    合并提交。 它允许你把多个提交记录合并成一个。


![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221200.png)

【应用： **本地栈式提交（注意提交只能是本地的！！！）** 】

来看一个在开发中经常会遇到的情况：我正在解决某个特别棘手的 Bug，为了便于调试而在代码中添加了一些调试命令并向控制台打印了一些信息。

这些调试和打印语句都在它们各自的提交记录里。最后我终于找到了造成这个 Bug 的根本原因，解决掉以后觉得沾沾自喜！ 

 最后就差把 bugFix 分支里的工作合并回 master 分支了。你可以选择通过 fast-forward 快速合并到 master 分支上，但这样的话 master 分支就会包含我这些调试语句了。你肯定不想这样，应该还有更好的方式…… 

 实际我们只要让 Git 复制解决问题的那一个提交记录就可以了。跟之前我们在“整理提交记录”中学到的一样，我们可以使用 

-  git rebase -i 
-  git cherry-pick 

  来达到目的。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221736.png)
  

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221755.png)

### 8.添加锚点：git tag 

```
git tag v1 c1 //打v1标签到提交c1上
```

 有没有什么可以*永远*指向某个提交记录的标识呢，比如软件发布新的大版本，或者是修正一些重要的 Bug 或是增加了某些新特性，有没有比分支更好的可以永远指向这些提交的方法呢？ 

 Git 的 tag 就是干这个用的啊，它们可以（在某种程度上 —— 因为标签可以被删除后重新在另外一个位置创建同名的标签）永久地将某个特定的提交命名为里程碑，然后就可以像分支一样引用了。 

  **更难得的是，它们并不会随着新的提交而移动。** 你也不能检出到某个标签上面进行修改提交，它就像是提交树上的一个锚点，标识了某个特定的位置。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221817.png)

### 9.**描述**离你最近的锚点（也就是标签）:git describe

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221836.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221847.png)

### 10.拉取远程仓库：git clone 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420221917.png)
   既然你已经看过 git clone 命令了，咱们深入地看一下发生了什么。 

 你可能注意到的第一个事就是在我们的**本地仓库多了一个名为 o/master 的分支, 这种类型的分支就叫远程分支**。由于远程分支的特性导致其拥有一些特殊属性。 

 远程分支反映了远程仓库(在你上次和它通信时)的状态。这会有助于你理解本地的工作与公共工作的差别 —— 这是你与别人分享工作成果前至关重要的一步. 

 远程分支有一个特别的属性，在你检出时自动进入分离 HEAD 状态。Git 这么做是出于不能直接在这些分支上进行操作的原因, **你必须在别的地方完成你的工作, （更新了远程分支之后）再用远程分享你的工作成果。** 

 远程分支有一个命名规范 —— 它们的格式是: 

-  <remote name>/<branch name> 

 因此，如果你看到一个名为 o/master 的分***么这个分支就叫 master，远程仓库的名称就是 o。 

 大多数的开发人员会将它们主要的远程仓库命名为 origin，并不是 o。这是因为当你用 git clone 某个仓库时，**Git 已经帮你把远程仓库的名称设置为 origin 了**

### 11.git fetch 

 git fetch 完成了仅有的但是很重要的两步: 

-  从远程仓库下载本地仓库中缺失的提交记录 
-  更新远程分支指针(如 o/master) 

 git fetch 实际上**将本地仓库中的远程分支**更新成了**远程仓库相应分支最新的状态**。 

  如果你还记得上一节课程中我们说过的，远程分支反映了远程仓库在你 **最后一次与它通信时** 的状态， git fetch  就是你与远程仓库通信的方式了！ git fetch  通常通过互联网（使用  http://  或  git://  协议) 与远程仓库通信。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420222547.png)
   **git fetch 不会做的事** 

 **git fetch 并不会改变你本地仓库的状态。它不会更新你的 master 分支，也不会修改你磁盘上的文件。** 

 理解这一点很重要，因为许多开发人员误以为执行了 git fetch 以后，他们本地仓库就与远程仓库同步了。它可能已经将进行这一操作所需的所有数据都下载了下来，**但是并没有修改你本地的文件**。我们在后面的课程中将会讲解能完成该操作的命令 . 

 所以, 你可以将 git fetch 的理解为单纯的下载操作。

### 12.git pull 

 既然我们已经知道了如何用 git fetch 获取远程的数据, 现在我们学习如何将这些变化更新到我们的工作当中。 

 其实有很多方法的 —— 当远程分支中有新的提交时，你可以像合并本地分***样来合并远程分支。也就是说就是你可以执行以下命令: 

-  git cherry-pick o/master 
-  git rebase o/master 
-  git merge o/master 
-  等等 

  实际上，由于先抓取更新再合并到本地分支这个流程很常用，因此 Git 提供了一个专门的命令来完成这两个操作。它就是我们要讲的  git pull 。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420222633.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420222647.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420222654.png)

```
git pull --rebase //我个人更喜欢这个
```

 其实也可以像下面这样： 

 **暂存，拉取，恢复暂存，合并(如果有冲突)，提交，推送** 

```
git stash :暂存本地代码 git pull origin develop : 获取远程分支代码 git stash pop:恢复之前暂存的文件
```

###  13.git push 

  it push  负责将 **你的** 变更上传到指定的远程仓库，并在远程仓库上合并你的新提交记录。一旦  git push  完成, 你的朋友们就可以从这个远程仓库下载你分享的成果了！ 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223050.png)

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223106.png) 

 【敲黑板：一直以来的误解！！**原来git push的时候交上去的不是一个提交，而是以检出位置HEAD为出发点，把所有的子子孙孙全部push上去，即这个提交距离origin/master之间的所有提交及新建的分支**，所以说提交需谨慎啊！】 

  【git merge, git pull ,看的是 **检出(checkout)位置HEAD** 在哪，就merge到哪】

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223123.png)

执行git push之后，c3 c4都会一起过去远程分支。可以理解成三大同步！！！分别是origin,origin/master以及本地master!

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223635.png)


### 14.偏离的历史提交

假设你周一克隆了一个仓库，然后开始研发某个新功能。到周五时，你新功能开发测试完毕，可以发布了。但是 —— 天啊！你的同事这周写了一堆代码，还改了许多你的功能中使用的 API，这些变动会导致你新开发的功能变得不可用。但是他们已经将那些提交推送到远程仓库了，因此你的工作就变成了基于[项目]()**旧版**的代码，与远程仓库最新的代码不匹配了。 

 这种情况下, git push 就不知道该如何操作了。如果你执行 git push，Git 应该让远程仓库回到星期一那天的状态吗？还是直接在新代码的基础上添加你的代码，亦或由于你的提交已经过时而直接忽略你的提交？ 

  因为这情况（历史偏离）有许多的不确定性，Git 是不会允许你  push  变更的。实际上它会强制你先合并远程最新的代码，然后才能分享你的工作。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223823.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223837.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223846.png)

很好！但是要敲那么多命令，有没有更简单一点的？ 

  当然 —— 前面已经介绍过  **git pull 就是 fetch 和 merge 的简写，类似的 git pull --rebase 就是 fetch 和 rebase 的简写！** 

  **![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420223914.png)**  

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224028.png)

### 15.远程服务器拒绝!(Remote Rejected) 

 如果你是在一个大的合作团队中工作, 很可能是master被锁定了, 需要一些Pull Request流程来合并修改。如果你直接提交(commit)到本地master, 然后试图推送(push)修改, 你将会收到这样类似的信息: 

```
 ! [远程服务器拒绝] master -> master (TF402455: 不允许推送(push)这个分支; 你必须使用pull request来更新这个分支.)
```

  **为什么会被拒绝?** 

 远程服务器拒绝直接推送(push)提交到master, 因为策略配置要求 pull requests 来提交更新. 

 你**应该按照流程,新建一个分支, 推送(push)这个分支**并申请pull request. 

 但是你忘记并直接提交给了master.现在你卡住并且无法推送你的更新. 

 **解决办法（不是很懂？）** 

 **新建一个分支feature, 推送到远程服务器(实质上就是间接地给远程服务器新建了一个feature分支，然后变成以后推东西到这个feature分支了，这样同事之间开发互不干扰，也可以避免提交的时候跟他人发生冲突).** 然后reset你的master分支和远程服务器保持一致, 否则下次你pull并且他人的提交和你冲突的时候就会有问题.

### 16.远程追踪 

 在前几节课程中有件事儿挺神奇的，Git 好像知道 master 与 o/master 是相关的。当然这些分支的名字是相似的，可能会让你觉得是依此将远程分支 master 和本地的 master 分支进行了关联。这种关联在以下两种情况下可以清楚地得到展示： 

-  pull 操作时, 提交记录会被先下载到 o/master 上，之后再合并到本地的 master 分支。隐含的合并目标由这个关联确定的。 
-  push 操作时, 我们把工作从 master 推到远程仓库中的 master 分支(同时会更新远程分支 o/master) 。这[个推]()送的目的地也是由这种关联确定的！ 

 直接了当地讲，master 和 o/master 的关联关系就是由分支的“remote tracking”属性决定的。master 被设定为跟踪 o/master —— 这意味着为 master 分支指定了推送的目的地以及拉取后合并的目标。 

 你可能想知道 master 分支上这个属性是怎么被设定的，你并没有用任何命令指定过这个属性呀！好吧, 当你克隆仓库的时候, Git 就自动帮你把这个属性设置好了。 

 当你克隆时, Git 会为远程仓库中的每个分支在本地仓库中创建一个远程分支（比如 o/master）。然后再创建一个跟踪远程仓库中活动分支的本地分支，默认情况下这个本地分支会被命名为 master。 

 克隆完成后，你会得到一个本地分支（如果没有这个本地分支的话，你的目录就是“空白”的），但是可以查看远程仓库中所有的分支（如果你好奇心很强的话）。这样做对于本地仓库和远程仓库来说，都是最佳选择。 

 这也解释了为什么会在克隆的时候会看到下面的输出： 

```
local branch "master" set to track remote branch "o/master"
```

 我能自己指定这个属性吗？ 

 当然可以啦！你可以让任意分支跟踪 o/master, 然后该分支会像 master 分支一样得到隐含的 push 目的地以及 merge 的目标。 这意味着你可以在分支 totallyNotMaster 上执行 git push，将工作推送到远程仓库的 master 分支上。 

 有两种方法设置这个属性，第一种就是通过远程分支检出一个新的分支，执行: 

```
git checkout -b totallyNotMaster o/master
```

  就可以创建一个名为  totallyNotMaster  的分支，它跟踪远程分支  o/master 。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224055.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224112.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224123.png) 

 第二种方法 

 另一种设置远程追踪分支的方法就是使用：git branch -u 命令，执行： 

```
git branch -u o/master foo
```

 这样 foo 就会跟踪 o/master 了。如果当前就在 foo 分支上, 还可以省略 foo： 

```
git branch -u o/master
```

### 17.Git Push 的参数 

 我们可以为 push 指定参数，语法是： 

```
git push <remote> <place>
```

 <place> 参数是什么意思呢？我们稍后会深入其中的细节, 先看看例子, 这个命令是: 

```
git push origin master
```

 把这个命令翻译过来就是： 

 切到本**地仓库中的“master”分支，获取所有的提交**，**再到远程仓库“origin”中找到“master”分支**，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。 

 【注意】 

```
git push origin side
```

 把这个命令翻译过来就是： 

 切到本**地仓库中的“side”分支，获取所有的提交**，**再到远程仓库“origin”中找到“side”分支**，将远程仓库中没有的提交记录都添加上去，搞定之后告诉我。 

  我们通过“place”参数来告诉 Git 提交记录来自于 master, 要推送到远程仓库中的 master。它实际就是要同步的两个仓库的位置。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224148.png)

可以看到，即使HEAD指针挪动了位置，但丝毫不会影响我们的push操作~~

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224203.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224219.png)

要同时为源和目的地指定 <place> 的话，只需要用冒号 : 将二者连起来就可以了：

```
git push origin <source>:<destination>
```

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224235.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224244.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224252.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224306.png)

【**不存在的分支，git会帮你新建，基于谁新建呢？本地的o/master，远程的master**】

### 18.Git fetch 的参数 

###  <place> 参数 

 如果你像如下命令这样为 git fetch 设置 的话： 

```
git fetch origin foo
```

  Git 会到远程仓库的  foo  分支上，然后获取所有本地不存在的提交，放到本地的  o/foo  上。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224318.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224346.png) 

 “如果我们指定 <source>:<destination> 会发生什么呢？” 

 如果你觉得直接更新本地分支很爽，那你就用冒号分隔的 refspec 吧。不过，你不能在当前检出的分支上干这个事，但是其它分支是可以的。 

 这里有一点是需要注意的 —— **source 现在指的是远程仓库中的位置，而 <destination> 才是要放置提交的本地仓库的位置。**它与 git push 刚好相反，这是可以讲的通的，因为我们在往相反的方向传送数据。 

  理论上虽然行的通，但开发人员很少这么做。我在这里介绍它主要是为了从概念上说明  fetch  和  push  的相似性，只是方向相反罢了。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224401.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224409.png)

### 19.古怪的 <source> 

 Git 有两种关于 <source> 的用法是比较诡异的，即你可以在 git push 或 git fetch 时不指定任何 source，方法就是仅保留冒号和 destination 部分，**source 部分留空**。 

-  git push origin :side 
- ​    git fetch origin :bugFix    

 ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224421.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224441.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224448.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224455.png)

### 20.Git pull 参数 

 既然你已经掌握关于 git fetch 和 git push 参数的方方面面了，关于 git pull 几乎没有什么可以讲的了 :) 

 因为 git pull 到头来就是 fetch 后跟 merge 的缩写。你可以理解为用同样的参数执行 git fetch，然后再 merge 你所抓取到的提交记录。 

 还可以和其它更复杂的参数一起使用。 

 以下命令在Git中是等效的： 

 git pull origin foo 相当于： 

```
git fetch origin foo; git merge o/foo
```

 还有... 

 git pull origin bar~1:bugFix 相当于： 

```
git fetch origin bar~1:bugFix; git merge bugFix
```

  看到了吗？git pull首先就是fetch + merge的缩写，git pull唯一关注的是提交最终合并到哪里（也就是为git fetch所提供的目的地参数） 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224509.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224521.png)

**检出位置即HEAD指针**

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224602.png)

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224532.png)

### 21.git 本地修改被reset后怎么恢复？ 

 可以使用git reflog 命令查看本地的操作记录 b7057a9 HEAD@{0}: reset: moving to b7057a9 98abc5a HEAD@{1}: commit: more stuff added to foo b7057a9 HEAD@{2}: commit (initial): initial commit 然后使用$ git reset --hard 98abc5a回到98abc5a对应的那次commit 

 【拓展】 

 reset命令有3种方式： 1：git reset –-mixed：此为默认方式，不带任何参数的git reset，即时这种方式，它回退到某个版本，**只保留[源码]()**，回退commit和index信息 2：git reset –-soft：回退到某个版本，**只回退了commit的信息，不会恢复到index一级。如果还要提交，直接commit即可** 3：git reset –-hard：彻底回退到某个版本，本地的[源码]()也会变为上一个版本的内容 这就是**--soft** 和 **--hard** 的区别：**--hard** 会清空工作目录和暂存区的改动,而 **--soft****则会保留工作目录的内容，并把因为保留工作目录内容所带来的新的文件差异放进暂存区(index)**。

### 22.reflog 

 每个人都会犯错误，这完全没有关系！有时候你可能觉得自己把仓库搞得一团糟，只想把它删了完事。 

  git reflog 是个非常有用的命令，可以显示所有操作的日志。包括 merge ， reset ， revert  等，基本上包括了对分支的任何更改。 

  ![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224748.gif)
  

 如果出错了，你可以根据reflog提供的信息通过重置HEAD 来撤销改动。 

  比如，我们实际上并不想合并分支。当我们执行  git reflog 命令时，我们看到在合并前仓库位于  HEAD@{1} 。我们执行下 git reset 命令，让 HEAD 重新指回原来的 HEAD@{1} 位置。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210420224801.gif)

我们可以看到，最新的操作也记录到reflog里了。

### 23.其它实用命令 

 ①git status 查看工作区、本地仓库、缓存（stash）的文件修改 状态 

 ②git branch 查看当前版本（分支） 

 ③git branch -a 查看所有版本（分支） 

 ④git checkout filename 放弃某文件的修改。 

 ⑤git stash 储存修改 

 ⑥git stash pop 弹出储存文件，此时新文件可能会与你的文件产生冲突，解决冲突。 

 ⑦git add filename 添加某个修改文件 

 ⑧git add . 提交所有加点 

 ⑨git branch -d <branch_name> 刪除 local branch 

 ①执行git commit --amend --no-edit之后，message内容并没有发生变化，并且最重要的是只有一条commit记录。如果要修改上一条的message，那么去掉--no-edit选项即可，git commit --amend -m "xxxx"。