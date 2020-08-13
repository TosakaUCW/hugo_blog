+++
title = 'Fira Code'
date = 2020-08-13T13:33:18+08:00
categories = ['技术']
tags = ['字体']
+++

consolas 曾是我最喜欢的英文字体 和 编程代码字体

在接触 Fira Code 后：芜湖！这也太爽了，so beautiful！

![Fira Code Logo](https://raw.githubusercontent.com/tonsky/FiraCode/e2ae5061fdb835829b26fa2312fc9ba74b7d42c4/extras/logo.svg)

<!--more-->

## 简介

Fira Code 是一款免费的等宽字体，支持连字特性（ligatures)

Fira Code = Fira Mono + ligatures

## 示例

{{% fold "官方示例" %}}
![Fira Code Ligatures](https://raw.githubusercontent.com/tonsky/FiraCode/master/extras/ligatures.png)
{{% /fold %}}

来看一看我在日常使用中的实际效果

editor - vscode - Insiders +  theme - Monokai + font - Fira Code

{{% fold "tarjan 代码示例" %}}
![tarjan](/images/Fira_Code_example.png)
{{% /fold %}}

## 安装

### Windows 系统安装 Fira Code 字体

Fira Code 是一款开源字体，代码在 github 上开源 [仓库地址](https://github.com/tonsky/FiraCode)

[下载字体](https://github.com/tonsky/FiraCode/releases)，选择最新版 Fira_Code_vx.x.zip 下载解压后右键安装即可

### 在 vscode 中使用 Fira Code 并开启连字特性（ligatures）

```json
    "editor.fontFamily": "Fira Code", // 在 fontFamily 中添加 Fira Code
    "editor.fontLigatures": true // 开启 ligatures
```