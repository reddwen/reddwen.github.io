---
title: 关于GIT使用SSH
date: 2018-05-08 13:49:02
tags:
---
# 初次安装git配置用户名和邮箱
初次安装git需要配置用户名和邮箱，否则git会提示：please tell me who you are.  
So!你需要运行命令来配置你的用户名和邮箱
```
$ git config --global user.name "你在GIT注册的名字"
$ git config --global user.email  "你在GIT注册的邮箱"
```
<!-- more -->

# git使用ssh密钥
##  一、生成密钥对（windows 平台）
```
$ ssh-keygen -t rsa -C "your_email@youremail.com"
```
直接一路 Enter就行. 

然后，会提示你输入密码，如下(建议输一个，安全一点，当然不输也行，应该不会有人闲的无聊冒充你去修改你的代码)

>完了之后，大概是这样：
```
Your public key has been saved in /home/you/.ssh/id_rsa.pub.
The key fingerprint is: # 01:0f:f4:3b:ca:85:d6:17:a1:7d:f0:68:9d:f0:a2:db your_email@youremail.com
```
To here 你的本地的密钥对就生成好了！撒花~

## 二、添加公钥到你的远程仓库（github）

### 1、查看你生成的公钥（两种方式）：
* 第一种(直接运行)：
```
$ cat ~/.ssh/id_rsa.pub

ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAAABAQC0X6L1zLL4VHuvGb8aJH3ippTozmReSUzgntvk434aJ/v7kOdJ/MTyBlWXFCR+HAo3FXRitBqxiX1nKhXpHAZsMciLq8vR3c8E7CjZN733f5AL8uEYJA+YZevY5UCvEg+umT7PHghKYaJwaCxV7sjYP7Z6V79OMCEAGDNXC26IBMdMgOluQjp6o6j2KAdtRBdCDS/QIU5THQDxJ9lBXjk1fiq9tITo/aXBvjZeD+gH/Apkh/0GbO8VQLiYYmNfqqAHHeXdltORn8N7C9lOa/UW3KM7QdXo6J0GFlBVQeTE/IGqhMS5PMln3 youemail@youemailnet
```
* 第二种：通过生成时，生成工具提供的生成目录找到 id_rsa.pub。用编辑器打开，复制。

### 2、登陆你的github帐户。
点击你的头像，然后 **Settings -> 左栏点击 SSH and GPG keys -> 点击 New SSH key**  

复制上面的公钥内容，粘贴进“Key”文本域内。 title域，自己随便起个名字  

点击 Add key

### 完成以后，验证下这个key是不是正常工作：
```
$ ssh -T git@github.com
```
如果你看到:
```
Hi xxx! You've successfully authenticated, but GitHub does not # provide shell access.
```
恭喜你，完成撒花~