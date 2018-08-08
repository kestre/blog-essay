---
title: hexo博客安装教程
date: 2018-06-01 00:00:00
tag: [hexo]
---
# 安装Hexo
## 1.安装node.js
- 点击[这里](https://nodejs.org/en/)进入node.js官网，并下载安装。

## 2.设置npm淘宝镜像站
- 默认npm源的下载速度可能很慢，建议使用淘宝镜像

```bash
npm config set registry "https://registry.npm.taobao.org"
```
## 3.安装hexo
- 执行以下命令

```bash
# 安装hexo
npm install hexo-cli g
# 初始化博客文件夹（blog为指定的文件夹）
hexo init blog
# 切换到该路径
cd blog
# 安装hexo的扩展插件
npm install
# 安装其它插件
npm install hexo-server --save
npm install hexo-admin --save
npm install hexo-generator-archive --save
npm install hexo-generator-feed --save
npm install hexo-generator-search --save
npm install hexo-generator-tag --save
npm install hexo-deployer-git --save
npm install hexo-generator-sitemap --save
```
- 测试安装情况，在本地创建服务器使用。

```bash
# 生成静态页面
hexo generate
# 开启本地服务器
hexo s
```
- 在浏览器中输入：http://127.0.0.1:4000 ，如果能看到刚刚创建的博客，那就给自己打call。
- 当访问 http://127.0.0.1:4000 ，无反应，咋怎呢？
- 可能是由于端口问题引起的。使用Ctrl+C中断本地服务，使用命令hexo s -p 5000重新开启本地服务，访问http://localhost:5000 可以看到博客页面了。

## 4.准备Github账号
- 关于Github的教程及配置 [这里](https://blog.csdn.net/qazwsxpcm/article/details/68946736)
**注意：**要创建仓库和配置好ssh（已配置可跳过此步骤）

## 5.将hexo博客部署到gitHub上
- 修改配置文件blog/_config.yml，修改deploy项的内容，如下所示：

```
# Deployment 注释
## Docs: https://hexo.io/docs/deployment.html
deploy:
  # 类型
  type: git
  # 仓库
  repo: https://github.com/你的Github/仓库名称.git
  # 分支
  branch: master
```
## 6.部署hexo
- 输入下面的命令将hexo博客部署到github中：

```bash
# 清空静态页面
hexo clean
# 生成静态页面
hexo generate
# 部署 
hexo deploy
```
# hexo命令缩写
- hexo支持命令缩写，<font style="color:red">hexo g</font>相当于：<font style="color:red">hexo generate</font>

```bash
hexo g: hexo generate
hexo c: hexo clean
hexo s: hexo server
hexo d: hexo deplop
```
# hexo组合命令
```bash
# 清除、生成、启动
hexo clean && hexo g -s
# 清除、生成、部署
hexo clean && hexo g -d
```

# 参考
- [hexo官方文档](https://hexo.io/zh-cn/)
- [hexo教程系列——hexo安装教程](https://blog.csdn.net/xuezhisdc/article/details/53130328)