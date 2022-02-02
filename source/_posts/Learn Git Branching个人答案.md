---
title:  Learn Git Branching个人答案
date:  2022-01-30 10:30:00
categories: 
- 计算机基础知识
tags:
- git
- 常用命令
- 答案
---
Git是目前世界上最先进的分布式版本控制系统，链接：[https://learngitbranching.js.org](https://links.jianshu.com/go?to=https%3A%2F%2Flearngitbranching.js.org)是一个有趣的学习Git的教程，在沙盒里执行相应的Git命令，还能看到每个Git命令的执行情况，像玩有些一样通过一系列的关卡，还有远程操作的练习，十分实用。

<!-- more -->

# 一、主要

## （一）基础篇

循序渐进地介绍 Git 主要命令

### *1: Git Commit*

```bash
git commit
git commit
```

### *2: Git Branch*

```bash
git checkout -b bugFix
```

完全版


```bash
git branch bugFix
git checkout bugFix
```

### *3: Git Merge*

```bash
git checkout -b bugFix
git commit
git checkout main
git commit
git merge bugFix
```

### *4: Git Rebase*

```bash
git checkout -b bugFix
git commit
git checkout main
git commit
git checkout bugFix
git rebase main
```

## （二）高级篇

要开始介绍 Git 的超棒特性了，快来吧！

### *1: 分离 HEAD*

```bash
git checkout C4
```

### *2: 相对引用（^）*

```bash
git checkout bugFix^
```

完全版


```bash
git chekcout bugFix
git checkout HEAD^
```

### *3: 相对引用2（~）*

```bash
git branch -f main c6
git branch -f bugFix HEAD~2
git checkout HEAD~1
```

### *4: 撤销变更*

```bash
git reset HEAD^
git checkout pushed
git revert HEAD
```

## （三）移动提交记录

自由修改提交树

### *1: Git Cherry-pick*

```bash
git cherry-pick bugFix side~1 another
```

### *2: 交互式 rebase*

```bash
git rebase -i main~4
```

## （四）杂项

Git 技术、技巧与贴士大集合

### *1: 只取一个提交记录*

```bash
git rebase -i HEAD~3
git branch -f main bugFix
```

### *2: 提交的技巧 #1*

```bash
git rebase -i HEAD~2
git commit --amend
git rebase -i HEAD~2
git branch -f main
```

### *3: 提交的技巧 #2*

```bash
git checkout main
git cherry-pick newImage
git commit --amend
git cherry-pick caption
```

### *4: Git Tag*

```bash
git tag v0 C1
git tag v1 C2
git checkout v1
```

### *5: Git Describe*

```bash
git describe bugFix
```

## （五）高级话题

只为真正的勇士！

### *1: 多次 Rebase*

```bash
git rebase main bugFix
git rebase bugFix side
git rebase side another
git branch -f main another
```

### *2: 两个父节点*

```bash
git branch bugWork  HEAD~^2~
```

### *3: 纠缠不清的分支*

```bash
git checkout one
git cherry-pick C4 C3 C2
git checkout two
git cherry-pick C5 C4 C3 C2
git branch -f three C2
```

# 二、远程

## （一）Push & Pull —— Git 远程仓库！

是时候分享你的代码了，让编码变得社交化吧

### *1: Git Clone*

```bash
git clone
```

### *2: 远程分支*

```bash
git commit
git checkout o/main
git commit
```

### *3: Git Fetch*

```bash
git fetch
```

### *4: Git Pull*

```bash
git pull
```

### *5: 模拟团队合作*

```bash
git clone
git fakeTeamwork 2
git commit
git pull
```

### *6: Git Push*

```bash
git commit
git commit
git push
```

### *7: 偏离的提交历史*

```bash
git clone
git fakeTeamwork 1
git commit
git pull --rebase
git push
```

### *8: 锁定的Main(Locked Main)*

```bash
git reset --hard o/main
git checkout -b feature C2
git push origin feature
```

## （二）关于 origin 和它的周边 —— Git 远程仓库高级操作

做一名仁慈的独裁者一定会很有趣……

### *1: 推送主分支*

```bash
git fetch
git rebase o/main side1
git rebase side1 side2
git rebase side2 side3
git rebase side3 main
git push
```

### *2: 合并远程仓库*

```bash
git checkout main
git pull origin main
git merge side1
git merge side2
git merge side3
git push origin main
```

### *3: 远程追踪*

```bash
git checkout -b side
git commit
git pull --rebase
git push
```

或

```bash
git branch -f side main
git branch -u o/main side
git checkout side
git commit
git pull --rebase
git push
```

### *4: Git push 的参数*

```bash
git push origin main
git push origin foo
```

### *5: Git push 参数 2*

```bash
git push origin main^:foo
git push origin foo:main
```

### *6: Git fetch 的参数*

```bash
git fetch origin main^:foo
git fetch origin foo:main
git checkout foo
git merge main
```

### *7: 没有 source 的 source*

```bash
git push origin :foo
git fetch origin :bar
```

### *8: Git pull 的参数*

```bash
git pull origin bar:foo
git pull origin main:s
```

