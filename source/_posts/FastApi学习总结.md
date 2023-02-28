---
title: FastApi学习总结
top: false
cover: false
toc: true
mathjax: true
date: 2021-11-26 16:23:04
password:
summary:
tags: FastAPI
categories: Python
---
 `官网学习教程：`[`https://fastapi.tiangolo.com/zh/`](https://fastapi.tiangolo.com/zh/)

## 一、为什么要使用 FastAPI; FastAPI有哪些特点?

###  1.快速：

拥有非常高的性能，归功于 Starlette 和 Pydantic；Starlette 用于路由匹配，Pydantic 用于数据验证

 Starlette

           1.官网使用介绍: https://www.starlette.io/

           2.什么是 Starlette?

            Starlette 是一种轻量级的 ASGI 框架/工具包，是构建高性能 asyncio 服务的理想选择。

           3.为什么使用 Starlette?

            Starlette 是目前测试最快的 Python 框架。只有 Uvicorn 超越了它，Uvicorn 不是框架，而是服务器。

           4.其他

            Starlette 提供了所有基本的 Web 微框架功能。但是它不提供自动数据验证，序列化或API 文档。

            自动数据验证由 Pydantic负责, API文档则是 OpenAPI  

            FastAPI 使用它来处理所有核心 Web 部件, 类 FastAPI 本身直接继承Starlette。

因此，使用 Starlette 可以执行的任何操作，都可以直接使用 FastAPI 进行。

            即路由匹配规则之内的 具体实现都要看 Starlette源码

  Pydantic

           1.官网使用介绍:

[https://pydantic-docs.helpmanual.io/](https://pydantic-docs.helpmanual.io/)

           2.什么是Pydantic?

                Pydantic 是一个库，基于Python类型提示来定义数据验证，序列化和文档（使用JSON模式）。

           3.为什么使用 Pydantic?

                FastAPI使用它来处理所有数据验证, 除了pydantic库之外，像是valideer库、marshmallow库、trafaret库以及cerberus库等都可以完成相似的功能，但是相较之下，pydantic库的执行效率会更加优秀一些。

Uvicorn

   1.官网使用介绍

        [https://www.uvicorn.org/](https://www.uvicorn.org/)

   2.什么是 Uvicorn？

    Uvicorn 是基于 uvloop 和 httptools 构建的如闪电般快速的 ASGI 服务器。它不是Web框架，而是服务器

###  2.开发效率：

功能开发效率提升 200% 到 300%

      2.1 路径参数/查询参数/Body参数 的提取与校验都被最大程度的支持

      2.2 依赖注入能较大程度的复用公共逻辑

###  3.减少 bug：

减少 40% 的因为开发者粗心导致的错误

  Pydantic提供的 数据验证功能与 类型注释的结合能有效的减少BUG

###  4.智能：

内部的类型注解非常完善，编辑器可处处自动补全

  支持Pydantic的IDE(编辑器)都能很好的支持补全等操作

###  5.简单：

框架易于使用，文档易于阅读

###  6.简短：

使代码重复最小化，通过不同的参数声明实现丰富的功能

      依赖注入

###  7.健壮：

可以编写出线上使用的代码，并且会自动生成交互式文档

###  8.标准化：

兼容 API 相关开放标准

## 二、FastAPI开发的相关依赖

###  1. Python

  Python的版本要大于等于 3.6

###  2. 其他模块依赖  

  pip install fastapi; 该操作会自动安装 Starlette 和 Pydantic

  pip install uvicorn;  uvicorn 是运行相关应用程序的服务器

  pip install fastapi\[all\]; 一步到位, 所有依赖全部安装

## 三、交互式文档

 FastAPI 会自动提供一个类似于 Swagger 的交互式文档，我们输入 "localhost:port/docs" 即可进入。

 交互式文档会显示 接口API的相关内容, 并且可以自定义接口信息, 具体如何操作, 后续再更新