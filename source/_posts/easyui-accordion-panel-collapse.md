---
title: EasyUI Accordion 的 Panel 初始化时全部折叠
date: 2013-09-10 17:26:00
categories:
  - JAVASCRIPT
tags:
  - easyui
---

EasyUI Accordion 下面的 Panel 有一个属性 `selected`，默认值为 `false` ；若设置为 `true`，则面板默认展开：

<script src="https://gist.github.com/luotaoyeah/a10bbbb23846fff982488b6019accab8.js"></script>

![](/images/easyui-accordion-panel-collapse/easyui-accordion-panel-collapse-001.png)

若多个 Panel 的 `selected` 都设置为 `true`，或者都设置为 `false`，则默认展开第一个 Panel；即默认情况下始终有一个面板是展开的，要想初始化时，面板全部折叠，找到的一个解决方法如下：

> 注释掉 `jquery.easyui.min.js` 第 3704 行的 `_2a0(0);`

_注：本文 easyui 版本为 `1.3.4`_
