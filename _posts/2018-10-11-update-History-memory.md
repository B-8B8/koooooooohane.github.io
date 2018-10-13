---
title: 更新GitHub仓库的所有历史记录
key: 20181011
tags: GitHub 学习
---
> 把旧项目提交到git上，但是会有一些历史记录，这些历史记录中可能会有项目密码等敏感信息。如何删除这些历史记录，形成一个全新的仓库，并且保持代码不变呢？   
可能只是治标不治本，因为有一个网友说：

<img src="http://wx2.sinaimg.cn/mw690/c08724f2gy1fw4ligbdwnj20mg02x0sx.jpg" width="100%" />

## 具体步骤
1. 进入cmd
2. 切到项目的本地目录
3. 执行下面的代码

```
1.Checkout

  git checkout --orphan koooooooohane.github.io #项目名

2. Add all the files

  git add -A

3. Commit the changes

  git commit -am "commit message" #更新的话

4. Delete the branch

  git branch -D master

5.Rename the current branch to master

  git branch -m master

6.Finally, force update your repository

  git push -f origin master

```
> 参考：<a href="https://blog.csdn.net/yc1022/article/details/56487680" terget="\_blank">git仓库删除所有提交历史记录，成为一个干净的新仓库 - yanchengyc</a>
