---
title: webpack逆向
top: false
cover: false
toc: true
mathjax: true
date: 2024-12-27 11:16:21
password:
summary:
tags: 爬虫
categories: python
---

# webpack扣代码

参考：[https://app.yinxiang.com/fx/970ae39c-9964-4aae-aa96-7e81fee4ef8f](https://app.yinxiang.com/fx/970ae39c-9964-4aae-aa96-7e81fee4ef8f)

### 案例

网址：aHR0cHM6Ly93d3cuY29pbmdsYXNzLmNvbS8=

接口：aHR0cHM6Ly9jYXBpLmNvaW5nbGFzcy5jb20vYXBpL2V0Zi9mbG93

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/39a24ad3-34f2-4ba9-9cb0-7a1c48106f96.png)

1.  先定位接口以及返回的数据
    

接口上面已给出，响应数据一看就有加密。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/052f6ff1-eece-4679-9d19-f09eaa2197c8.png)

2.  使用XHR断点进行debugger
    

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/71d5c3eb-2d51-42c5-a76f-97c1afd95ee6.png)

一步一步调试，找到解密的关键代码

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/6460a5f6-c7fd-4e57-8864-eb1dd47bc7ce.png)

3.  webpack打包
    

通过debugger调试，把这个解密的js拉到开头，可以看出典型的webpack打包

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/ca78e79f-e7f9-4c80-94ff-1628641ffa8f.png)

跟进到eZ里面去

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/b57d05ba-45ba-43fb-abbf-152cf8c0c88a.png)

4.  找到webpack加载器
    

这里需要跟进到加载器，将加载器扣出来。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/32c2a803-147b-453d-9f44-5bd96d36f136.png)

根据堆栈信息，可以找到加载器的入口

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/58e32640-051b-49d3-848e-37da1fa54e86.png)

将加载器复制出来，保存为本地js

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/11b01226-b678-4297-8944-0a4352e2d012.png)

5.  调试webpack，扣对应使用的js文件，补环境。
    

新建load.js, 将加载器全部复制出来

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/673b5c8e-cbd8-4b19-89a6-6052a750720e.png)

通过加载器代码可发现h为加载器函数，使用全局变量导出 加载器函数。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/ca8f4045-fce1-4d39-bd1a-2ce881131a63.png)

调试, 发现报错

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/485128a9-dc2a-4f80-b3be-f156a7e1b6f6.png)

打断点到self这里，看下是什么。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/3e3eb1f1-5305-4c71-88f1-6eb5db894952.png)

这里可发现self就是window。

直接补环境即可。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/bd2791bf-53d7-496b-ba8e-81e41af6ae2b.png)

继续执行，发行正常运行。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/89960f16-4e75-42ad-868d-86a0f93d7aa4.png)

接下来定位到解密的js，看到上面的 解密的关键代码 那块的图；找到对应的js文件和执行解密的代码块，看是使用的那个模块。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/094e0ce1-01be-4b64-b23e-690fcecd3f1b.png)

定位到解密的关键代码块，往上找加载了什么模块。

我这里将这个js全部复制到vscode里面，通过关键字搜索，定位到了解密代码块，vscode也有提示这个代码块是属于那个模块的。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/dd9c3803-58c6-4599-9236-66141a96b642.png)

可以看出来是2214模块

再使用加载器，调试下这个模块。

window.my\_load(2214)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/03009470-e513-4c32-8752-bc40aea0311f.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/c7da9e71-c08b-4aba-a352-8c1f723f389e.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/4c947fc2-51cd-4de2-928b-cd94b737faeb.png)

通过输出结果，发现2214这个模块没有加载进来。

好，刚才我们已经定位到这个js了，直接全扣出来。

新建个文件 wp\_model.js, 将那个js全部复制到文件中![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/70ff3b1c-c591-4ac7-8266-512f853f7beb.png)

再将该js导入到load.js里面，再次执行。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/4a746d69-e5bb-48a9-8882-33032ddb048e.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/f0192eff-86e3-43c7-99d0-4b2d8b41c107.png)

好，发现还缺少另一个模块 67294。

直接通过开发者工具搜索，67294:，找到对应的模块加载js，将其扣入wp\_model.js中，再次执行load.js直到把所有使用到的模块补全。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/bdc5d5d8-2fa3-4094-80a9-e70aad16f8ec.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/1115e733-d6df-4d48-9297-eca1a14bee3b.png)

这个案例用到了这两个模块（其实我们扣多了，有更简化的方案，这里不赘述）；

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/eaf72524-c0f8-4a0e-a8b9-bb7d879a7a7a.png)

直到输出没有报错，表示已经完成🎉。

6.  使用全局变量导出解密函数  
    前面我们已经找到了解密关键代码块对应的function，这里直接用全局变量给他导出。
    

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/49e00760-5ba4-44ff-88eb-202939d42c9b.png)

也就是这个function。

找到我们wp\_model.js中对应的位置，使用window全局变量挂到自定义一个变量上去。即可使用。

比如window.my\_decrypt = function(t) ....

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/be7a7203-a0c3-41bc-9c4d-d8d750c0177e.png)

这里可以看到需要传入t参数，t参数怎么来呢？直接在浏览器上debugger到这里，复制t 这个obj就行。

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/70ea8b29-bbe2-417a-ae99-67d74e7e035a.png)

![image.png](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/res/8K4nyR1P5pWYqLbj/img/6ffe602d-1d5b-4c45-aeac-4c4ffe8b6761.png)

至此恭喜你完成解密。🎉
