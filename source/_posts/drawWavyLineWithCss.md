---
layout: post
title: drawWavyLineWithCss
subtitle: 如何用css画出波浪线
date: 2018-06-21 15:26:29
author: codinglobster
tags:

  - css
---

## 前言

atom编辑器是我的主力编辑器，日常的美化和配置是必然的操作，一日，出于对原生目录树样式不满的我决定通过内置的开发者工具对其修整一下，突然发现部分文件名下居然有**红色的下划线**,本来以为是贴图实现的效果，没想到用工具看了下居然是用`background-img`配合`linear-gradient`来实现的，借这个机会可以顺便复习下css3了。

## 效果

![redline](./redline.png)

## 实现代码

```css
.block {
    height: 200px;
    background-image: linear-gradient(45deg, transparent 65%, #EF5350 80%, transparent 90%), linear-gradient(135deg, transparent 5%, #EF5350 15%, transparent 25%), linear-gradient(135deg, transparent 45%, #EF5350 55%, transparent 65%), linear-gradient(45deg, transparent 25%, #EF5350 35%, transparent 50%);
    background-size: 8px 2px;
    background-repeat: repeat-x;
    background-position: bottom;
  }
```
## liner-gradient

这是我第一次知道background-image可以使用多个线性渐变，所以抽丝剥茧，看看线性渐变的实现细节吧。

`background-image: linear-gradient(45deg, transparent 65%, #EF5350 80%, transparent 90%)`的效果：

![redline](./1.png)