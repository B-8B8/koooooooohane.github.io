---
title: 记录2018-10-10Jekyll运行时出现的问题
key: 2018-10-10
tags: jekyll 问题 主题 .MD
---

## 问题一：github-pages x64-mingw32
```
jekyll serve
C:/Ruby23-x64/lib/ruby/gems/2.3.0/gems/bundler-1.13.6/lib/bundler/resolver.rb:366:in `block in verify_gemfile_
dependencies_are_found!': Could not find gem 'github-pages x64-mingw32' in any of the gem sources listed in yo
ur Gemfile or available on this machine. (Bundler::GemNotFound)
  from C:/Ruby23-x64/lib/ruby/gems/2.3.0/gems/bundler-1.13.6/lib/bundler/resolver.rb:341:in `each'
  from C:/Ruby23-x64/lib/ruby/gems/2.3.0/gems/bundler-1.13.6/lib/bundler/resolver.rb:341:in `verify_gemf
ile_dependencies_are_found!'

```
### 解决方法：

> 命令行执行下面两句(安装非常慢)   
参考：<a href="https://blog.csdn.net/wudalang_gd/article/details/74619791" target="\_blank">在 Windows 上搭建本地 Jekyll 编译环境时问题汇总 - wblearn</a>

```
gem install github-pages -r --source http://rubygems.org/
gem install wdm -r --source http://rubygems.org/

```

## 问题二：(无法启动)
```
/Library/Ruby/Gems/2.3.0/gems/bundler-1.16.1/lib/bundler/runtime.rb:313:in check_for_activated_spec!’: You have already activated public_suffix 3.0.2, but your Gemfile requires public_suffix 3.0.0. Prepending bundle exec to your command may solve this. (Gem::LoadError)

```
### 解决方法：

> 命令行执行下面两句(之前是 `/>jekyll server`)   
参考：<a href="https://blog.csdn.net/haoaiqian/article/details/80194668" target="\_blank">解决运行jekyll serve时报错 - 化风</a>

```
bundle exec jekyll serve

```

## 主题
* <a href="https://tianqi.name/jekyll-TeXt-theme/docs/zh/quick-start" target="\_blank">TeXt Theme - 快速开始页面</a> (推荐)
* <a href="https://simpleyyt.com/jekyll-theme-next/" target="\_blank">next</a> (没有设置成功，但是好看、简约)
* <a href="http://taylantatli.github.io/Halve/" target="\_blank">Halve</a> (好看，但是缺少search功能)
* <a href="http://xseven.me/" target="\_blank">xseven</a> (好看，同上)
* <a href="https://github.com/kaeyleo/jekyll-theme-H2O" target="\_blank">H2O</a> (好看，好用就是代码高亮的框框太难看)
* <a href="https://webjeda.com/vyaasa/" target="\_blank">vyaasa</a> (字超大，有点Low)
---
* <a href="http://jekyllthemes.org/" target="\_blank">jekyllthemes.org</a> (主题站)
* <a href="http://himsel.me/material-theme/github/open-source/project/jekyll/2017/06/15/theme-users.html" target="\_blank">一些杂乱的地址</a>

## 书写规范
> 直接给出参考地址：   
<a href="https://www.jianshu.com/p/1e402922ee32/" target="\_blank">Markdown——入门指南 - Te_Lee</a>   
<a href="https://www.cnblogs.com/liugang-vip/p/6337580.html" target="\_blank">.md即markdown文件的基本常用编写语法（图文并茂） - 凌云之翼</a>
