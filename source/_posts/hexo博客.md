---
title:  hexo搭建博客
date:  2020-10-25 00:02:39
categories: 
- 实际应用
tags:
- Github
- 博客
- hexo
---
搭建一个博客不难，但是网上资料好多坑。我也是一个刚入门小白，可能有些会写错，请在评论指出。但是有经过实战测试的。 我用的博客框架是 hexo，采用 NexT 主题 ，使用 Github 托管项目，使用Github Pages实现页面搭建。
<!-- more -->

# 一、基础配置

## 1.1.本地安装hexo

首先保证你电脑上有node环境，这个不懂的可以自定百度。

控制台输入node -v出现版本号说明安装成功。

有了node就可以安装hexo了，控制台输入如下命令

```text
npm i -g hexo
```

同样控制台输入hexo -v出现版本号说明安装成功。

然后开始初始化项目，控制台输入：

```text
hexo init
```

生成的目录：

```text
node_modules：是依赖包
public：存放的是生成的页面
scaffolds：命令生成文章等的模板
source：用命令创建的各种文章
themes：主题
_config.yml：整个博客的配置
db.json：source解析所得到的
package.json：项目所需模块项目的配置信息
```

## 1.2.本地运行

首先在本地跑起来你的代码

控制台安装hexo-server

```text
npm i hexo-server
```

然后运行 hexo-server

```
hexo server
```

在浏览器中访问：http://localhost:4000  就可以看到你本定运行的页面了

这时你可以在本地调试一下你的blog。

## 1.3.github配置

首先要创建一个github账号

并配置好ssh

这些不懂的可以自行百度。

创建一个repo，名称为yourname.github.io, 其中 yourname 是你的github名称，按照这个规则创建github page才会生效。

修改_config.yml中的git配置

```js
deploy:
  type: git
  repo:  https://github.com/yourname/yourname.github.io.git
  branch: master
```

## 1.4.部署上传

在本地安装上传工具

```js
npm install hexo-deployer-git --save
```

依次执行如下命令

```text
hexo clean     //删除上次打包
hexo generate   //打包
hexo deploy    /上传
```

这里我单独写了sh执行这三段脚本，这样每次只需要执行这个sh即可。

在浏览器中输入 https://yourname.github.io/ 就可以看到你的个人博客了！

## 1.5 README.md配置



1.首先在source文件夹下建立一个README.md

2.修改_config.yml

```text
skip_render: README.md
```



# 二、绑定二人域名

## 2.1购买一个域名

这里我选择的是阿里云，挑选一个自己喜欢的域名购买。

## 2.2域名解析

进入控制台添加域名解析。

按照如下规则添加两条记录。

解析好的域名404，说明域名解析没有问题，接下来进入github进行配置

## 2.3 hexo配置

在本地的博客目录中找到source文件夹。

新建一个没有后缀名的文件CNAME

在文件中添加你的域名，如：

```text
lisq.xyz
```

保存后重新生成，并提交你的博客。

## 2.4 github配置

在github中找到你的博客仓库。

点击`Setting`

找到`Custom domain`

输入你的域名点击save

然后你就可以在浏览器用你的域名愉快的访问啦！