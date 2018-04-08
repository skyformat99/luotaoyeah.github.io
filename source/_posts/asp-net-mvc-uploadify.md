---
title: ASP.NET MVC 4 使用 Uploadify 文件上传插件
date: 2013-09-14 12:47:02
categories:
  - ASP.NET
tags:
  - asp.net
  - uploadify
---

## 1. 下载

官网下载免费的 Flash 版本的开发包：[http://www.uploadify.com/download/](http://www.uploadify.com/download/)；

![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-01.png)

## 2. 解压

解压后，需要用到下面几个文件：

![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-02.png)

## 3. 前端配置

#### 3.1. 页面添加 CSS 和 JS 引用

<script src="https://gist.github.com/luotaoyeah/6cf3d7b79f2a683c4adbd7263998cc05.js"></script>

#### 3.2. 添加上传按钮标签

<script src="https://gist.github.com/luotaoyeah/d4250e7c2af06c65c096ca11f3a6a5e1.js"></script>

#### 3.3. 初始化插件

<script src="https://gist.github.com/luotaoyeah/8aa5ceaeeca73d58c52151385a8842b2.js"></script>

## 4. 后端代码

<script src="https://gist.github.com/luotaoyeah/e0098b3e0e93ac3aca5ba7a1dd6791c0.js"></script>

## 5. 参数说明

* `uploader: '/article/upload'`  请求地址，对应后端进行处理的 Action；
* `formData:{ "imgType": "normal" }`  动态设置表单数据，一般在 `onUploadStart` 事件中进行处理：
<script src="https://gist.github.com/luotaoyeah/933a1f8c398bf668811b336aa14d489f.js"></script>

    如果参数名与初始化的 formData 中的参数名一样，参数值将被覆盖，否则添加；在开始上传之前执行动态设置都是可以的，例如在两个 checkbox（使用 bootstrap-switch 插件）的状态切换时进行设置：
<script src="https://gist.github.com/luotaoyeah/6d815bb02ea2202442719dcae7cddd11.js"></script>


* `onUploadSuccess` 事件处理函数的 3 个参数：
    * `file` 包含原始文件的信息；
    * `response`  后台返回（true 或 false）；
    * `data`  后台返回数据，试例中为 JSON 对象；

* 其他详细参数，请参考官方文档：[http://www.uploadify.com/documentation/](http://www.uploadify.com/documentation/)

## 6. 效果预览

![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-03.png)
![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-04.png)
