+++
title = "Git Bash 中文乱码"
date = 2020-11-02T08:32:26+08:00
categories = ['技术']
tags = ['Git']
+++

在使用 Git 对中文文件进行操作时，经常出现乱码

```
$ git status
Refresh index: 100% (545/545), done.
ddOn branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Codeforces/[Codeforces 1119B] Alyona and a Narrow Fridge.cpp
        modified:   "Luogu/[Luogu P1972] [SDOI2009]HH\347\232\204\351\241\271\351\223\276.cpp"
```

## 原因

1. Windows 默认使用 GB2312 来处理文件名和内容，但 Git 默认使用 UTF-8
2. Git 命令在输出 log 的时候会使用 less 这个工具，默认和 Windows 的编码格式不兼容
3. 不同的命令行工具（终端）对环境变量的处理方式不同

<!--more-->

## 解决方案

解决方案是统一使用 UTF-8 编码

### 设置 Git 支持 UTF-8 编码

```
git config --global core.quotepath false # 不对0x80以上的字符进行quote，解决git status/commit时中文文件名乱码
git config --global i18n.commitencoding utf-8 # 提交信息编码
git config --global i18n.logoutputencoding utf-8 # 输出log编码
git config --global svn.pathnameencoding GB2312 # 支持中文路径
export LESSCHARSET=utf-8 # git log 默认使用less分页，所以需要bash对less命令进行utf-8编码
```

### 设置 GUI 显示中文

```
git config --global gui.encoding utf-8 # 图形界面编码
```

### 针对其他命令行工具

在系统环境变量中添加 `LESSCHARSET` 变量，并赋值为 `utf-8`

可解决 Windows 其他命令行工具如 Powershell , cmd 和 cmder 的中文乱码问题


## 最终效果

```
$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git restore <file>..." to discard changes in working directory)
        modified:   Codeforces/[Codeforces 1119B] Alyona and a Narrow Fridge.cpp
        modified:   Luogu/[Luogu P1972] [SDOI2009]HH的项链.cpp
```

## 参考资料

[nightire/解决 Git 在 windows 下中文乱码的问题.md](https://gist.github.com/nightire/5069597)

[g4e常见问题#4 解决Git在Windows上的中文乱码问题](https://devopshub.cn/2018/01/07/g4e-faq-4-git-encoding-error/)

[Git bash中文显示问题](https://knightwupz.github.io/post/pipelines/git-bash%E4%B8%AD%E6%96%87%E6%98%BE%E7%A4%BA%E9%97%AE%E9%A2%98/)