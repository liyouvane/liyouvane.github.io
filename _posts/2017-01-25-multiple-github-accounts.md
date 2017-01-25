---
title: "桌面多个Github账户的管理"
layout: post
date: 2017-01-25 01:17
image: /assets/images/markdown.jpg
headerImage: false
tag:
- Github
- develop
- diary
blog: true
author: You Li
description: Github小号的实现
---

### 第一步 创建新的SSH密钥
首先我们需要为新的Github账户生成一个SSH密钥。在命令行中输入
`ssh-keygen -t rsa -C "your-email-address"` 这里的邮箱就是新的Github账户的注册邮箱。
输入这个之后会要求你输入保存密钥的地址，重点在于不能改写前一个账户的信息。原账户的密钥位置是`~/.ssh/id_rsa`所以只要写一个不同的位置就可以了，比如说`~/.ssh/id_rsa_new`。

### 第二步 关联密钥
登陆到Github账户之后，在账户信息里面添加我们生成的SSH密钥。在命令行中打开文档
`vim ~/.ssh/id_rsa_new.pub`就可以看到完整密钥。复制所有内容添加到账户之中。
此时，我们在命令行中再输入`ssh-add ~/.id_rsa_new`即可。成功之后会显示"Identity Added."

### 第三步 配置文件
我们需要分离两个不同的账户，所以需要一个配置文件来区分。在`.ssh`文件中创建一个配置文件的方法如下。
```shell
touch ~/.ssh/config
open ~/.ssh/config
```
打开文件之后，在其中添加配置如下

```
#Default GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa

Host github-NEW
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_rsa_new
```
保存即可。其中`github-NEW`是可以任意命名的。

### 第四步 测试指令
本地创建一个文件目录，cd之后测试指令
```shell
git init
git commit -m "first commit"
```
在账户里创建一个新的Repository，比如命名为“Test”，下面我们把本地创建的文件push上去
```shell
git remote add origin git@git_NEW:NAME/Test.git
git push origin master
```
其中NAME是github的账户名字。
