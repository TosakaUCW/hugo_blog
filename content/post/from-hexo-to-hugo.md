---
title: "从 Hexo 到 Hugo"
date: 2020-02-06T23:07:17+08:00
draft: false
categories: ['技术']
tags: ['博客']
weight: 1
---

该博客的前身是自 2018 年 12 月搭建的 hexo 博客，于 2020 年 2 月搬迁至 hugo。

<!--more-->

## Motivation

这次搬迁最直接的原因，是我把 hexo 博客从 Windows 搬到 Linux 失败了..

之前魔改太多，主题里各种文件都改过，甚至 node_modules 里面都被我改过很多地方，之前因此都放弃升级 hexo 和 NexT 主题，而最近开始主用 Manjaro，于是博客必须得搬到 Linux 上，但无论是简单复制过来还是重新使用 npm 安装包都未能如愿。

其实说不定还有很多解决方法，但我之前就听说过 hugo，于是就决定干脆搬过来好了。

但为什么是搬到 hugo，而不是升级 hexo 呢？

从 hugo 本身来说，它最显著的优点，就是快：

1. 安装快。这在本地安装时倒影响不大，而且如果你要修改主题还得用 yarn 安装包，得装上几分钟。最主要的是，安装快使得 CI 快，使用 CI 发布 hugo 博客就会非常容易且快捷。
2. 构建快。hugo 比起 hexo 的构建速度不是快了一点点，hexo 需要十几秒才能构建完的博客，hugo 只需不到一秒。这点更加重要，它不仅加速了 CI，而且本地构建几乎没有延时，加上 server 预览使用 websocket，直接分屏当预览写博客体验良好。

然后是不知道主题 even 还是 hugo 的特性，非常多，某些方面配置比 NexT 还完善（当然也有 NexT 有而 even 没有的）。

只不过我一直用的是 2018 年 12 月那时的 hexo 和 NexT，这一年来 hexo 可能也有很多改进，所以我的比较不一定准确。

## Process

hugo 的配置步骤就不说了，网上很多教程，而且这个博客是 [开源](https://github.com/ouuan/hugo-blog) 的，可以自己看。稍微提醒一下，CI 配置中的 `$GITHUB_TOKEN` 需要在 travis.ci 上进行设置。

从 hexo 搬到 hugo 可以参考这篇博客：[Hugo 与 Hexo 的异同 | reuixiy](https://io-oi.me/tech/hugo-vs-hexo/) 。

我自己使用的脚本（fork 然后稍微改了一点）：[ouuan/hexo2hugo](https://github.com/ouuan/hexo2hugo) 。

由于我之前博客的地址是不带 `/post` 的，我在 404 页面上用 JS 进行了跳转。只不过这并不能挽救 SEO...

## Issues

因为是用脚本批量改的，改完之后也没有一篇篇去检查，难免会有改错的地方以及一些死链，可以直接在评论区指出，多谢大家了。

如果有原博客链接没有 redirect 而是 404 了，也请务必告诉我。

如果需要原 hexo 博客的，可以看 [ouuan.github.io/hexo-archive](https://github.com/ouuan/ouuan.github.io/tree/hexo-archive) 。