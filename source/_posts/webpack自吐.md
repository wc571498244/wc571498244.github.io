---
title: webpack自吐
top: false
cover: false
toc: true
mathjax: true
date: 2025-01-03 11:15:04
password:
summary:
tags: 爬虫
categories: python
---

# webpack自吐

示例网站：aHR0cHM6Ly9zcGEyLnNjcmFwZS5jZW50ZXIv

示例接口：aHR0cHM6Ly9zcGEyLnNjcmFwZS5jZW50ZXIvYXBpL21vdmllLw==

token值js动态生成![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/aa7e9062-d6a5-41f7-8a59-1d4a4fab75a1.png)

根据接口的堆栈信息可定位到token生成代码

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/abca510c-9627-44ef-a786-96c4e1b687f0.png)

进入到 Object(i\["a"\]) 中，里面代码非常简单明了；直接扣算法最简单（代码如下）。

```javascript
var crypto = require('crypto-js');
var t = '1735870406'; // 时间戳
var o = crypto.SHA1('/api/movie,20,1735870406').toString(crypto.enc.Hex) // url的path,offset,时间戳

var c = crypto.enc.Base64.stringify(crypto.enc.Utf8.parse([o, t].join(",")));
console.log(c)
```

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/d6d34917-7b8f-40a1-a666-14f0760d7d0a.png)

这里我们学习webpack自吐，因为token生成用到了n变量，而n在上面可以看到是r('3452')生成的（webpack），断点到var n = r("3452"); 刷新页面再根据堆栈信息定位到webpack加载器，从而把加载器抠出来。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/d631e730-cb65-4e78-969c-3b52fda1ae7f.png)

定位到加载器，将app.e9fbf43f.js的代码扣出来，放在本地执行，解决完window的问题运行不报错即可。

接下来在加载器中 exports 后下个断点，重新刷新页面。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/2a49c881-3ca0-4f8c-8f52-65b9edfd967b.png)

再将自吐代码放入控制台执行，代码如下：

```javascript
window.c = c; // c 是加载器。 上面可以识别出来
window.res = "";
window.flag = false;
c = function (r) {
    if (flag) {
        window.res = window.res + `"${r.toString()}"` + ":"+(e[r]+"")+",";
    }
    return window.c(r);
}

```

执行后，跳到下个断点（也就是在执行var n = r("3452"); 之前）；将window.flag改为ture（按需获取）。

继续在i函数执行完后下个断点，也就是执行i函数所需要使用到的js文件。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/7fafde9b-f560-4255-878d-99c9b365d647.png)

断点跳到i函数执行完，控制台输出window.res, 即可自动吐出3452所需要的js文件了。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/Pd6l2YRpW2oVO7Ma/img/878ed208-9dfc-45ff-bef6-3c4a57ce9871.png)