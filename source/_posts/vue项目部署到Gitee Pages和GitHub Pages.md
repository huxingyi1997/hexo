---
title:  Vue项目部署到Gitee Pages和GitHub Pages
date:  2021-03-11 12:02:39
categories: 
- web前端
tags:
- Vue
- Gitee Pages
- GitHub Pages
- 实战
---
使用Gitee Pages实现静态页面（[悦听播放器(Gitee Pages)](https://hxy1997.gitee.io/music_player/%E6%A8%A1%E6%9D%BF) 和[悦听播放器(GitHub Pages)](https://hxy1997.xyz/music_player/%E6%A8%A1%E6%9D%BF)）部署后 ，我尝试着将自己学习搭建的Vue电商项目托管至Gitee Pages，实现项目的正式上线 [Vue电商项目(Gitee Pages)](https://hxy1997.gitee.io/vue_shops/)和[Vue电商项目(GitHub Pages)](https://hxy1997.xyz/vue_shops/)
<!-- more -->

# 1. 简单的音乐播放器静态页面

## 1.1 将项目提交至gitee

到Gitee上新建仓库，写仓库名称 选择是否开源 

使用git remote add origin https://gitee.com/码云用户名/项目名 / 添加远程仓库。

git pull origin master 命令，将码云上的仓库pull到先前创建的文件夹中，期间需要输入gitee上面的账号和密码，输入完成密码之后点击 OK。
可能会出现以下提示问题：

这是因为没有配置提交时的用户名和邮箱的原因，你可以直接执行：

```
git config --global user.name "Your Name"
git config --global user.email "you@example.com"
```

完毕之后再把将要上传的文件，全部添加到之前在桌面创建的文件夹中并使用git add .（. 表示所有的）或者 git add 文件名 // 将文件保存到缓存区
然后使用git commit -m '这里写新推送的文件描述' //添加文件描述
使用git push origin master ，将本地仓库推送到远程仓库，之后去刷新gitee就能看到推送上去的项目了。

## 1.2 配置Gitee Page

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418145848.png)

点击项目导航栏中的`服务 --> Gitee Pages`

## 1.3 访问网站

打开网站 https://码云用户名.gitee.io/项目名/入口文件 即可访问，注意入口文件为index.html可省略，我的项目入口文件不是，所以需要，打开[悦听播放器](https://hxy1997.gitee.io/music_player/%E6%A8%A1%E6%9D%BF) 可以享用音乐了

## 1.4 将码云库同步到Github库

#### 步骤一：在github中新建一个项目

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150113.webp)

#### 步骤二：在码云中复制要导入github中的git地址

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150049.webp)

#### 步骤三：在github平台进入步骤一创建的项目，点击“Import code”进入导入页面

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150132.webp)

#### 步骤四：在导入页面，将步骤二复制出来的码云中项目的git地址，粘贴到“url”中，并点击“Begin import”

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150154.webp)

> 注意：有时候步骤四在导入时，会出现一个500异常界面。这时，我们不需要管它，只需要重新进入我们新建的项目，再次从步骤三开始操作即可， 这时候便会看见项目正在导入，稍等一段时间，便会提示你导入成功，最后我们再次访问该项目，便会发现码云中的项目已经导入到github中来了。（导入成功GitHub会发邮件提醒）

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150211.webp)

500异常界面

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150226.webp)

再次进入时的导入界面

#### 步骤五：开启GitHub Pages

点击右上角的 Settings

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150239.jpeg)

找到 GitHub Pages 选项，设置好后，https://Github用户名.github.io/项目名/入口文件

# 2. 使用vue-cli脚手架创建的页面提交至Gitee Pages

## 2.1 调整本地项目

### 在根目录下增加.spa文件

首先，根据[官方指示](https://gitee.com/help/articles/4237)

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150250.png)

在项目路径中添加`.spa`文件

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150259.png)

原理，我不是很清楚，应该是给Nginx做一个`标识`作用吧。

### 配置production版本路径名称

根目录下的vue.config.js文件，如下图箭头那个要改为项目文件名称

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150309.png)

在本地运行`npm run build`，得到/dist

![在这里插入图片描述](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150320.png)

### 配置.gitignore文件

首先要在.gitignore文件去掉/dist，这个文件默认是不上传的，但是执行打包bulid的时候会生成dist文件，而线上访问的是打包之后的dist文件；

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150327.png)

## 2.2 将项目提交至gitee

参考1.2节的做法，此步不再赘述

## 2.3 配置Gitee Page

用上次同样的方法点进Gitee Pages

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150337.png)

 

 这一次部署目录要填写dist

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150626.png)

## 2.4 访问网站

打开网站 https://码云用户名.gitee.io/项目名/入口文件 即可访问，注意入口文件为index.html可省略，这次vue-cli创建的项目不需要，打开线 [Vue电商项目](https://hxy1997.gitee.io/vue_shops/) 就可以体验电商项目了

# 3. 使用vue-cli脚手架创建的页面提交至GitHub Pages

## 3.1 GitHub Pages服务说明与注意事项

在你的github项目设置的GitHub Pages项，有这么一句话：

> [GitHub Pages](https://pages.github.com/) is designed to host your personal, organization, or project pages from a GitHub repository.
> 译：GitHub Pages旨在从GitHub存储库中托管您的个人、组织或项目页面。

这句话主要是介绍GitHub Pages的宗旨，[Github Pages](https://pages.github.com/) 官网上有其他的一些介绍，由于都是英文，我们看着费劲，不如直接看Gitee Pages 服务的说明，两者都差不多。

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150611.png)
注意事项大家了解一下切勿违反就行，这里重点给大家说的是红框圈出的内容，Pages服务只支持静态项目，若你的项目需要服务器支持，就不适合部署到这上面了。

## 3.2 项目配置注意事项

#### 1、vue-router 不要开启 history 模式

正常项目中我们会因为网站路径中带有“#”而将vue-router开启history模式，以去掉#号。但开启history模式需要服务器的支持，因此在github pages中不支持这一模式，所以我们不能开启history模式。

#### 2、在 vue.config.js 中设置正确的 publicPath

要将项目部署到 https://<USERNAME>.github.io/<REPO>/ 上 (即仓库地址为 https://github.com/<USERNAME>/<REPO>)，可将 publicPath 设为 "/<REPO>/"。
举个例子，我的仓库名字为“vue-admin-web”，那么 vue.config.js 的内容应如下所示：

```
// vue.config.js
module.exports = {
  publicPath: process.env.NODE_ENV === 'production'
    ? '/vue-admin-web/'
    : '/'
}
```

## 3.3 部署github项目

做好上述的配置后，就可以把项目推送到github上了，首先在Github上新建仓库

在本地上.git中的config中[remote "origin"]下增加一行

  url = git@github.com:<USERNAME>/<REPO>.git

gitpush成功把项目推送到了该仓库下。接下来把项目部署到github pages上。【参考 [vue-cli](https://cli.vuejs.org/zh/guide/deployment.html#github-pages) 官方说明】
1、在项目目录下，创建内容如下的 deploy.sh 文件



```
#!/usr/bin/env sh

# 当发生错误时中止脚本
set -e

# 构建
npm run build

# cd 到构建输出的目录下 
cd dist

git init
git add -A
git commit -m 'deploy'

# 部署到 https://<USERNAME>.github.io/<REPO>
git push -f git@github.com:<USERNAME>/<REPO>.git master:gh-pages

cd -
```

2、运行该文件
在项目目录下打开cmd命令窗口（快捷方法：在项目目录下，按住Shift键，然后鼠标右键，选择“在此处打开命令窗口”）

```
// Linux环境下
bash deploy.sh
// Windows环境下
deploy.sh
```

运行后，git就会执行该文件里的命令。大致就是，构建打包项目代码，然后将打包后的代码上传到仓库中的 gh-pages 分支。（注意：新建的仓库默认只有master分支，没有gh-pages分支，但不需要你手动新建该分支，运行该文件后，会自动帮你生成gh-pages分支）
这样，你的项目就已经部署到github pages 了。在你的github项目的 Settings - Options 下的 GitHub Pages项里，可以看到你的项目线上网站地址。
示例：https://marco-hui.github.io/vue-admin-web/

![img](https://cdn.jsdelivr.net/gh/huxingyi1997/my_img/img/20210418150530.png)