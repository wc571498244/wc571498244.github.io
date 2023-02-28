---
title: WSGI和AWSGI
top: false
cover: false
toc: true
mathjax: true
date: 2021-09-27 00:03:50
password:
summary:
tags: web
categories: python
---

# 什么是WSGI？
WSGI全称Web Server Gateway Interface(Web服务器网关接口)，这是一个规范（标准）；规定了web server如何与web application交互，application如何处理请求。

## WSGI不足
1. WSGI应用是单一的、同步的可调用对象（即输入一个请求，返回一个响应）。这个模式无法支持长连接或者Websocket这样的连接（有些WSGI server，比如Gunicorn里是能保持长连接，1-5s，但也仅限于他们的异步模式）。
2. 即使我们使应用程序可以异步调用，但他也只能处理单路径，它也无法处理一些协议的多个数据传入事件，比如websocket frames（请求多路复用把一个消息分成了多个块，一个连接会有不同路径的消息交错，类似http2/3）。


# ASGI
ASGI全称Asynchronous  Server Gateway Interface(异步服务器网关接口)，他是构建与WSGI规范之上的，是WSGI的延伸和扩展。
ASGI尝试保持在一个简单的应用接口的前提下，提供允许数据能够在任意时候、被任意应用进程发送和接受的抽象。还采用了将ASGI协议转换为python兼容、异步友好的消息集的原则，具体为两部分：一个用于通信的标准化接口，通过实现它来构建服务器，以及一组用于每个web协议的标准消息格式。

# 简单总结
- WSGI和ASGI，都是基于Python设计的网关接口（Gateway Interface，GI）。
- WSGI是基于http协议模式开发的，为同步应用程序提供标准，不支持Websocket。
- ASGI是为异步、同步应用程序提供标准，支持WSGI不支持当前Web开发中的一些新的协议标准。
- ASGI支持原有模式和Websocket的扩展，即ASGI是WSGI的扩展。

WSGI服务器: uWSGI
WSGI框架: Flask1.0、Django3.0之前

AWSGI服务器: uvicorn
AWSGI框架：FastAPI、Django3.0之后、Flask2.0

