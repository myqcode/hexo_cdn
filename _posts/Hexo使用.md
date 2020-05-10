---
title: Hexo使用
date: 2020-04-20 17:41:57
tags: Hexo

typora-root-url: ./
summary:
categories: 玩物搭建
---

## 1.首先去安装nodejs（官网nodejs.org）

![01](/Hexo使用/01.png)

**安装：**一直next到底即可

```bash
查看是否安装成功
查看node的版本
node -v

查看包管理器版本
npm -v

```



## 2.并生成博客

```bash
第二步：
安装hexo框架（用淘宝源安装）安装cnpm
npm install -g cnpm --registry=https://registry.npm.taobao.org

查看cnpm是否安装好
cnpm -v

安装hexo博客
cnpm install -g hexo-cli

查看hexo是否安装好
hexo -v

随意目录创建一个文件夹K（过程中出现错误直接干掉）
生成博客
sudo hexo init

启动
hexo s

创建一个博客
hexo new "文章名"
```



## 3.将本地资源存放在远端的服务器上（免费的）

![02](/Hexo使用/02.png)

## 4.githubssh公钥设置

- 首先下载一个git软件

​	**下载地址：https://git-scm.com/downloads**

- 一样一直next

- 配置用户名和邮箱

```bash
查看git用户配置
git config user.name

查看git邮箱配置
git config user.email

配置git用户
git config --global user.name "你的名字"

配置git邮箱
git config --global user.email "你的邮箱"

获取ssh公钥
cat ~/.ssh/id_rsa.pub
```

- 设置github公钥

![](/Hexo使用/04.png)

## 5.装一个git的插件

```bash
中途报的错误不用管
cnpm install --save hexo-deployer-git
```

**并修改文件名K下的_config.yml**

![](/Hexo使用/03.png)

最后：

```
hexo d 部署到远端

第一次部署，中途会出现让你登录github的窗口
会让你输入github账号密码，最后推送完成
```

​																				**修改主题不做赘述**

## 6.修改玩主题需要重新生成

```bash
清除克隆
hexo clean

重新生成一下
hexo g

上传到github
hexo d
```



