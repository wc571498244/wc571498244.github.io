---
title: Python Pypi Server
top: false
cover: false
toc: true
mathjax: true
date: 2021-06-05 17:28:25
password:
summary:
tags: Pypi
categories: Python
---

# Python Pypi Server

[转载自《PyPI打包上传实践》](https://www.jianshu.com/p/be91c70adb27)

## 1. 代码打包

要打包代码，首先需要编写自己的代码包。比如你写了一个.py文件，里面有一些函数啥的，为了方便调用，你需要将代码打包，下次使用时直接调用就好，因此，第一步，将你写的代码打包。
创建一个文件夹，并在该文件夹下创建 `__init__.py` 文件，然后将你写的.py文件放到这个文件夹下面就行。

```
packagename/
    |
    +-- __init__.py
    |
    +-- myfunction.py
    |
    +-- mymorefunction.py
    |
    +-- ...
    |

```

`packagename`为你创建的包名称，`myxxx.py`是你写的python代码，还有添加个`__init__.py`文件（文件内容可以为空）.
现在你可以调用这个包了(引入包的路径)

```
import packagename
```

## 2\. 符合pypi的格式

将上面的文件的目录结构改成如下格式

```
packagename
    |
    +-- COPYING.txt
    |
    +-- README.rst
    |
    +-- setup.py
    |
    +-- packagename
    .       |
    .       +-- __init__.py
    .       |
    .       +-- myscripts1.py
    .       |
    .       +-- mysscripts2.py
    .       |
    .       +-- mymorescripts.py
    .       |
    .
    |
    +-- docs/
    |

```

就是将原来的目录深移一层，文件夹的名称一样即可。在第一层目录下创建些特殊文件。
***Tips***

> *   COPYING.txt :***可以不要（节约时间，重要的事情先说、简单说）。***
>     就是授权文件，里面是你关于这个包的授权，比如：MIT license，那么你里面放入MIT License全文即可，当然，如果你不清楚这个，你完全可以不要这个文件。
> *   README.rst：***就是介绍，可以不要吧（不推荐，要是想让大家用的话还是好好写一写）***
>     这个文件想必研发都应该清楚。如果有，尽量放些东西在这里了，后面如果可能我们会用到它的。
> *   setup.py：***核心文件***
>     这里面的内容后面讲
> *   docs/（这是个文件夹，存放一些文档的）
>     这个文件夹你放你的documents吧，不过要用心写文档真是个难事，所以这个文件夹基本是不存在的——为自己的懒惰可耻一把。

**setup.py**的样例

```
# coding: utf-8
import codecs
import os
import sys

try:
    from setuptools import setup
except:
    from distutils.core import setup

"""
打包的用的setup必须引入，
"""

#!/usr/bin/env python
# -*- coding: utf-8 -*-

import sys
if sys.version_info < (2, 5):
    sys.exit('Python 2.5 or greater is required.')

try:
    from setuptools import setup
except ImportError:
    from distutils.core import setup

import SendMoney

with open('README.rst', 'rb') as fp:
    readme = fp.read()

# 版本号，自己随便写
VERSION = "1.0.7"

LICENSE = "MIT"

setup(
    name='<项目的名称>',
    version=VERSION,
    description=(
        '<项目的简单描述>'
    ),
    long_description=readme,
    author='<你的名字>',
    author_email='<你的邮件地址>',
    maintainer='<维护人员的名字>',
    maintainer_email='<维护人员的邮件地址',
    license=LICENSE,
    packages=find_packages(),
    platforms=["all"],
    url='<项目的网址，我一般都是github的url>',
    install_requires=[
        "beautifulsoup4",
        lxml_requirement
        ],
    classifiers=[
        'Development Status :: 4 - Beta',
        'Operating System :: OS Independent',
        'Intended Audience :: Developers',
        'License :: OSI Approved :: BSD License',
        'Programming Language :: Python',
        'Programming Language :: Python :: Implementation',
        'Programming Language :: Python :: 2',
        'Programming Language :: Python :: 2.7',
        'Programming Language :: Python :: 3',
        'Programming Language :: Python :: 3.4',
        'Programming Language :: Python :: 3.5',
        'Programming Language :: Python :: 3.6',
        'Topic :: Software Development :: Libraries'
    ],
)

# URL 你这个包的项目地址，如果有，给一个吧，没有你直接填写在PyPI你这个包的地址也是可以的
# INSTALL_REQUIRES 模块所依赖的python模块
# 以上字段不需要都包含

```

文中的classifiers的内容并不是随便填写的，你需要参照本文参考文档中的PyPI Classifiers来写

## 3、开始使用Distutils进行打包

为了保证效果，在打包之前我们可以验证setup.py的正确性，执行下面的代码

> python setup.py check

输出一般是running check
如果有错误或者警告，就会在此之后显示
没有任何显示表示Distutils认可你这个setup.py文件。

如果没有问题，那么就可以正式打包，执行下面的代码：

> python setup.py sdist

执行完成后，会在顶层目录下生成dist目录和egg目录

打包完成后就可以准备将打包好的模块上传到pypi了，首先你需要在[pypi](https://link.jianshu.com?t=https%3A%2F%2Fpypi.org%2F)上进行注册
注册完成后，你需要在本地创建好pypi的配置文件，不然有可能会出现使用http无法上传到pypi的问题
在用户目录下创建.pypirc文件，文件的内容如下
window用户创建`.pypirc`可以命名为`.pypirc.`    位置示例：`C:\Users\admin\.pypirc.`
```
[distutils]
index-servers=pypi

[pypi]
repository = https://upload.pypi.org/legacy/
username = <username>
password = <password>

```

完成后运行：

> python setup.py register sdist upload

最后出现`Server response (200): OK`就是成功了，可以去pypi上查看自己发布的包

包到这里，就完成了上传PyPI的工作了。你如果要用，安装下就好：

> pip install packagename

这个过程还是很顺利的，以后多尝试，出现问题再补充！

作者：snowy_sunny
链接：https://www.jianshu.com/p/be91c70adb27
