---
title: github多账户切换(GitHub Disktop)
key: 2018-10-17
tags: GitHub 问题
---

## 一、问题描述
```
*** Please tell me who you are.
Run
git config --global user.email "you@example.com"
git config --global user.name "Your Name"
to set your account's default identity.
Omit --global to set the identity only in this repository.

```
> 上面是使用前端IDE，commit时报出的异常

## 二、解决办法
### 1.单独为项目配置的方法(全局和单独配置都存在的时候会默认使用项目单独配置的)
1. 打开项目所在目录，找到隐藏的.git文件夹。注意这个文件夹是隐藏的，显示隐藏出来就行。
2. 打开文件夹里的config文件，推荐用nodepad++打开。
3. 添加下面三行到文件：

```
[user]
    name = XXX(用户名称)
    email = XXXX(邮箱)

```
> 或者在.git文件夹执行

```
git  config  user.name  "Your Name"
git  config  user.email  "you@example.com"

```
### 2.全局
```
git config --global user.email "you@example.com"  
git config --global user.name "Your Name"

```
## 三、实操
![img](http://wx2.sinaimg.cn/mw690/c08724f2ly1fwazco3523j20y60pdacx.jpg)
> 选择 `Open in Command Prompt` 选项
然后输入

```
git  config  user.name  "Your Name"
git  config  user.email  "you@example.com"

```
![img](http://wx2.sinaimg.cn/mw690/c08724f2ly1fwazcog46mj20xz0hrjsa.jpg)
