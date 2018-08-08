---
title: Github安装及配置
date: 2018-06-03 00:00:00
tag: [github,note]
---
# 什么是Github
- github是一个基于git的代码托管平台，付费用户可以建私人仓库，我们一般的免费用户只能使用公共仓库，也就是代码要公开。

- [注册](https://github.com/join?source=header-home)Github账户 (已有账号可以跳过此步骤)

## 安装git
- [下载](https://git-scm.com/downloads/)并安装git

## 配置ssh
- 右键git bash
- 设置user.name和user.email
```bash
git config --global user.name "你的GitHub用户名"
git config --global user.email "你的GitHub注册邮箱"
```
- 在本地生成ssh密匙
```bash
ssh-keygen -t rsa -C "你的GitHub注册邮箱"
```
- 之后会要求确认路径和输入密码，我们这使用默认的一路回车就行。成功的话会在~/下生成.ssh文件夹，进去，打开id_rsa.pub，复制里面的key
- 回到github上，进入 Account Settings（账户配置），左边选择SSH Keys，Add SSH Key,title随便填，粘贴在你电脑上生成的key。
![](/img/18/06/github安装及配置.png)
为了验证是否成功，在git bash下输入：
```bash
$ ssh -T git@github.com
```
- 如果是第一次的会提示是否continue，输入yes就会看到：You've successfully authenticated, but GitHub does not provide shell access 。这就表示已成功连上github。

## 还是不懂？那看看别人家的
- [入门级：GitHub和Git超超超详细使用教程！](https://blog.csdn.net/javaandroid730/article/details/53522872)
- [利用 SSH 完成 Git 与 GitHub 的绑定](https://blog.csdn.net/qq_35246620/article/details/69061355)
- 还是不懂？还有[绝招](https://www.baidu.com/)!

# 参考
- [CSDN](https://blog.csdn.net/javaandroid730/article/details/53522872)
- [菜鸟教程](http://www.runoob.com/w3cnote/git-guide.html)