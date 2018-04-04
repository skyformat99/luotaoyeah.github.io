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

```html
<link href="~/Content/uploadify/uploadify.css" rel="stylesheet" />
<script src="~/Scripts/jquery-1.10.2.min.js"></script>
<script src="~/Content/uploadify/jquery.uploadify.min.js"></script> 
```

#### 3.2. 添加上传按钮标签

```html
<span id="btn_upload"></span>
```

#### 3.3. 初始化插件

```javascript
$(function () {
    $('#btn_upload').uploadify({
        uploader: '/article/upload', /* 后端接口地址 */
        swf: '/Content/uploadify/uploadify.swf',
        buttonText: "选择文件", /* 按钮文字 */
        height: 34, /* 按钮高度 */
        width: 82, /* 按钮宽度 */
        fileTypeExts: "*.jpg;*.png;", /* 允许的文件类型 */
        fileTypeDesc: "请选择图片文件", /* 文件说明 */   
        formData: { "imgType": "normal" }, /* 提交给服务器端的参数 */
        onUploadSuccess: function (file, data, response) { /* 一个文件上传成功后的响应事件处理 */
            var data = $.parseJSON(data);
            alert(data.imgpath);
        }
    });
});
```


## 4. 后端代码

```csharp
public ActionResult Upload(HttpPostedFileBase Filedata) {
    /*
     * 没有文件上传，直接返回
     */
    if (Filedata == null || string.IsNullOrEmpty(Filedata.FileName) || Filedata.ContentLength == 0) {
        return HttpNotFound();
    }

    /*
     * 获取文件完整文件名(包含绝对路径)
     * 文件存放路径格式：/files/upload/{日期}/{md5}.{后缀名}
     * 例如：/files/upload/20130913/43CA215D947F8C1F1DDFCED383C4D706.jpg
     */
    string fileMD5 = CommonFuncs.GetStreamMD5(Filedata.InputStream);
    string FileEextension = Path.GetExtension(Filedata.FileName);
    string uploadDate = DateTime.Now.ToString("yyyyMMdd");
    string imgType = Request["imgType"];
    string virtualPath = "/";

    if (imgType == "normal") {
        virtualPath = string.Format("~/files/upload/{0}/{1}{2}", uploadDate, fileMD5, FileEextension);
    } else {
        virtualPath = string.Format("~/files/upload2/{0}/{1}{2}", uploadDate, fileMD5, FileEextension);
    }
    string fullFileName = this.Server.MapPath(virtualPath);

    /*
     * 创建文件夹，保存文件
     */
    string path = Path.GetDirectoryName(fullFileName);
    Directory.CreateDirectory(path);
    if (!System.IO.File.Exists(fullFileName)) {
        Filedata.SaveAs(fullFileName);
    }

    var data = new { imgtype = imgType, imgpath = virtualPath.Remove(0, 1) };
    return Json(data, JsonRequestBehavior.AllowGet);
}
```

## 5. 参数说明

* `uploader: '/article/upload'`  请求地址，对应后端进行处理的 Action；
* `formData:{ "imgType": "normal" }`  动态设置表单数据，一般在 `onUploadStart` 事件中进行处理：
```javascript
onUploadStart: function(file) {
    $("#btn_upload").uploadify("settings", "formData", { "imgType": "other","imgMode":"big" });
}
```

    如果参数名与初始化的 formData 中的参数名一样，参数值将被覆盖，否则添加；在开始上传之前执行动态设置都是可以的，例如在两个 checkbox（使用 bootstrap-switch 插件）的状态切换时进行设置：
```javascript
$('#img_mode').on('switch-change', function (e, data) {
    $("#btn_upload").uploadify("settings", "formData", { "imgMode": (data.value ? "small" : "big") });
});

$('#img_type').on('switch-change', function (e, data) {
    $("#btn_upload").uploadify("settings", "formData", { "imgType": (data.value ? "normal" : "big") });
});
```

* `onUploadSuccess` 事件处理函数的 3 个参数：
    * `file` 包含原始文件的信息；
    * `response`  后台返回（true 或 false）；
    * `data`  后台返回数据，试例中为 JSON 对象；

* 其他详细参数，请参考官方文档：[http://www.uploadify.com/documentation/](http://www.uploadify.com/documentation/)

## 6. 效果预览

![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-03.png)
![](/images/asp-net-mvc-uploadify/asp-net-mvc-uploadify-04.png)
