---
title: 2Anki 帮助文档
date: '2025-08-22 15:25:39'
updated: '2025-09-16 09:12:52'
permalink: /post/2anki-help-documentation-zxwq3o.html
enableToc: true
enableBackLinks: true
---



up::

‍

d@eck::卡组名称；可在文档内，输入你的卡组名称；如果没有：d@eck::卡组名称，则默认按文档树结构在anki建立卡组。

# 前言

脚本构思和模板参考自[logseq-anki-sync](https://github.com/debanjandhar12/logseq-anki-sync)，，所以本文以它的帮助文档的结构进行说明。

# 多行卡片

大纲卡片的制卡逻辑，底层逻辑按[logseq-anki-sync](https://github.com/debanjandhar12/logseq-anki-sync)，但是又思源的闪卡特点。三种形式

**单个问题**

- 问题1

  - 1

‍

 ![image](/assets/images/image-20250822153414-kdv4bi5.png)

**单个多级问题**

- 问题3

  - 3

    - 33
  - 3-1

    - 33-11

![image](/assets/images/image-20250822153401-an25j1n.png)

**并列问题**

- 问题2

  - 2
- 问题2-1

  - 2-1

![image](/assets/images/image-20250822153338-pug8agg.png)

# 挖空卡片

**高亮挖空**

- 111 **啊啊** 2222

大 **萨** 达大 

**序号挖空**

啊啊啊啊啊啊啊啊啊啊{{c1 22222}}大萨达撒大{{c2 阿达啊啊}}

# 图像遮挡

大叔大婶大萨  
​![image](/assets/images/image-20250822160614-psk8bft.png)

特性1：支持行级元素同步；

- 大萨**达a**da<span data-type="text" style="color: var(--b3-font-color2);">dASD </span>

  - 大<span data-type="text" style="color: var(--b3-font-color2);">萨达</span>的

- 大大ada<span data-type="text" style="background-color: var(--b3-font-background7);">dad阿打算 啊</span>

  - 大萨达

特性2：支持表格同步；

|大萨达|大萨达|大萨||
| --------| ----------------------------------------------| ----------------------------------------------| --------|
||大 **萨达** ​**a**d<br />|牛逼|的|
|22|||大萨达|
||大师|||
||大萨达|||

- 更复杂的嵌套大纲表格

  - ||||
    | --| -----| ---------------------------------|
    ||dsd|sdsd|
    ||||
  - dasdasd
  - dasdad

![image](/assets/images/image-20250828141836-fi8grlm.png)

![image](/assets/images/image-20250828141850-eptmjza.png)

‍
