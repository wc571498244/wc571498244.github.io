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

## 四、路由相关

### 1.路由匹配规则

1.1 普通路由匹配

  在一个 路由定义模块中, 若一个URL可以匹配上多个 自定义路由的情况下

  会优先匹配到 先定义的那一个。

举个例子:

    # coding: utf-8
    from fastapi import FastAPI
    import uvicorn
     
    app = FastAPI()
     
    @app.get("/users/me")
    async def read_user_me():
        return {"user_id": "the current user"}
     
    @app.get("/users/{user_id}")
    async def read_user(user_id: int):
        return {"user_id": user_id}
     
    if __name__ == "__main__":
        uvicorn.run("main:app", host="0.0.0.0", port=8080)


**因为路径操作是按照顺序进行的，所以这里要保证** `/users/me` **在** `/users/{user\_id}` **的前面，否则的话只会匹配到** `/users/{user\_id}`**，此时如果访问** `/users/me`**，那么会返回一个解析错误，因为字符串 "me" 无法解析成整型。**

### 2.APIRouter路由组

APIRouter主要是给路由加前缀, 方便于在项目中 对于不同模块或者不同类别的接口 加一个统一的前缀进行绑定, 有点类似分组/文件夹的意思

APIRouter: 有点类似于GO中gin框架中的路由组的概念, 所以这里写的是路由组

使用方式如下:

1.在app具体的模块中

    # app/user/user_login.py
    from fastapi import APIRouter
     
    user_router = APIRouter(prefix="/v1/users")
     
    # 以后访问的时候要通过 /v1/users/login	 来访问
    @router.get("/login")
    async def v1():
        return {"message": "hello world"}

2.在项目启动加载的 main.py中:

    # main.py
    from fastapi import FastAPI
    from app.user.user_login import user_router
    import uvicorn
     
    app = FastAPI()
     
    # 将 user_router 注册到 app 中，相当于 Flask 中的 register_blueprint
    app.include_router(router)
     
    if __name__ == "__main__":
        uvicorn.run("main:app", host="0.0.0.0", port=8080)

### 3.禁用一个路由:

参数 include\_in\_schema 设置为 False

 @app.get("/items/", include\_in\_schema=False)

但是这个 禁用路由仅仅只是在 docs页面隐藏该 接口 不显示, 实际上并不影响其调用和真正使用。只是接口文档处对外界不可知~

## 五、请求参数获取

PS： 这里只介绍参数获取, 对于参数校验的写法， 详见第六章

### 5.1 获取路径参数

路径参数的声明:

路径参数声明在url中, 并且用一对花括号`{user\_id}`包裹； eg. `@app.get("/user/{user\_id}")`

对于多个 路径参数来说, 用多个 `{}` 包裹即可；eg. `@app.get("/user/{part\_id}/{user\_name}")`

路径参数的获取:

    @app.get("/user/{part_id}/{user_name}")
    async def get_user(part_id: str, user_name: str):
        """在路由函数中定义的两个路径参数 part_id/user_name
        	只要路由函数中的 参数名能与之对应(一模一样), 
            那么在这里路由函数获取的 part_id/user_name就是路径参数上对应的 part_id/user_name
           在路由函数中 这两个参数的顺序没有要求
        """
        return {"part_id": part_id, "user_name": user_name}

对于特殊路径参数的获取, 详见 5.4 获取path类型参数

### 5.2 获取params参数

查询参数不需要声明, 如果函数中定义了不属于路径参数的参数时，那么它们将会被自动解释会查询参数。

**方式一: 默认方式**

    @app.get("/user/{user_id}")
    async def get_user(user_id: str, name: str, age: int):
        """我们在函数中参数定义了 user_id、name、age 三个参数
           显然 user_id 和 路径参数中的 user_id 对应，然后 name 和 age 会被解释成查询参数
           这三个参数的顺序没有要求，但是一般都是路径参数在前，查询参数在后
           
           PS: 这里做了 类型注释, 因此传进来的 参数会经过 是否必要性检测即参数类型检测
           		这里的声明表示, params参数 name/age 是必填参数, 没有传会抛错
                虽然params参数的数据框架在接收的时候, value都是 str类型的, 但是这里声明 age: int时, 
                age: '10' 会被强转为 age: 10; 但是对于 age: 'test' 这类的参数 则会报类型错误~
        """
        return {"user_id": user_id, "name": name, "age": age}

方式二: 使用Query 类型注释

    @app.get("/user/{user_id}")
    async def get_user(user_id: str, name: str=Query(...), age: int=Query(...)):
        return {"user_id": user_id, "name": name, "age": age}

### 5.3 获取body参数

#### 5.3.1 获取json类型 body参数(单个请求体)

**方式一: 使用Body类型**

    from datetime import datetime, time, timedelta
    from typing import Optional
    from uuid import UUID
    
    from fastapi import Body, FastAPI
    
    app = FastAPI()


​    
    @app.put("/items/{item_id}")
    async def read_items(
        item_id: UUID,
        start_datetime: Optional[datetime] = Body(None),		# 这里的 Body类型注释 表明了获取的是一个 body参数
        end_datetime: Optional[datetime] = Body(None),
        repeat_at: Optional[time] = Body(None),
        process_after: Optional[timedelta] = Body(None),
    ):
        start_process = start_datetime + process_after
        duration = end_datetime - start_process
        return {
            "item_id": item_id,
            "start_datetime": start_datetime,
            "end_datetime": end_datetime,
            "repeat_at": repeat_at,
            "process_after": process_after,
            "start_process": start_process,
            "duration": duration,
        }

**方式二: 继承BaseModel （Pydantic提供）**

如果参数的类型被声明为一个 Pydantic 模型，它将被解释为请求体。

    from typing import Optional, List
    from fastapi import FastAPI, Request, Response
    from pydantic import BaseModel
    import uvicorn
     
    app = FastAPI()


​     
    class Girl(BaseModel):
        """数据验证是通过 pydantic 实现的，我们需要从中导入 BaseModel，然后继承它"""
        name: str
        age: Optional[str] = None
        length: float
        hobby: List[str]  # 对于 Model 中的 List[str] 我们不需要指定 Query（准确的说是 Field）


​     
    @app.post("/girl")
    async def read_girl(girl: Girl):
        # girl 就是我们接收的请求体，它需要通过 json 来传递，并且这个 json 要有上面的四个字段（age 可以没有）
        # 通过 girl.xxx 的方式我们可以获取和修改内部的所有属性
        return dict(girl)  # 直接返回 Model 对象也是可以的

#### 5.3.2 获取json类型 body参数(多个请求体)

上面只是接收一个请求体的情况, 如果是接收两个请求体呢？

    class Girl(BaseModel):
        name: str
        age: Optional[str] = None
     
    class Boy(BaseModel):
        name: str
        age: int
     
    @app.post("/boy_and_girl")
    async def read_boy_and_girl(girl: Girl,
                                boy: Boy):
        return {"girl": dict(girl), "boy": dict(boy)}

对于上述这种写法, 在传入body数据时, 在json串中 应该有两个子json串，且其key分别为 girl/boy

如何理解?

实际上对于 继承了 BaseModel或者 路由函数中 直接使用 Body类型进行声明的 参数, 框架都会接收为 body参数, 这些body参数可以看做是关键字参数, 但是当 只有一个 BaseModel类型时, 就可以不使用关键字进行识别

上述案例的请求体示例如下:

    {
        "girl": {"name": "新垣结衣", "age": 18},
        "boy": {"name": "星野源", "age": 18}
    }

PS: 其实上述写法, 也可以只是用一个 请求体来表示( 即这种 BaseModel的写法是可以多层嵌套的~)

    class Girl(BaseModel):
        name: str
        age: Optional[str] = None
     
    class Boy(BaseModel):
        name: str
        age: int
    
    class BoyGirl(BaseModel):
        girl: Girl
        boy: Boy
     
    @app.post("/boy_and_girl")
    async def read_boy_and_girl(boy_girl: BoyGirl):
        return dict(boy_girl)

#### 5.3.3 获取from表单数据

**方式一: 使用 Form 类型**

    from fastapi import FastAPI, Form
    	
    app = FastAPI()
    @app.post("/login")
    async def user_login(username: str = Form(...),		# 这里的Form类型 表明接收的是 form表单的数据
                       password: str = Form(...)):
        return {"username": username, "password": password}

**方式二：使用request**

    from fastapi import FastAPI, Request
    	
    app = FastAPI()
    @app.post("/girl")
    async def girl(request: Request):
        # 此时 await request.json() 报错，因为是通过 data 参数传递的，相当于 form 表单提交
        # 如果是通过 json 参数传递，那么 await request.form() 会得到一个空表单
        form = await request.form()
        return [form.get("name"), form.getlist("age")]

PS: python如何发送 form表单数据（使用 data参数）

    requests.post(url, data={"name": "test1", "password": "test2"})

#### 5.3.4 获取文件上传数据

**1.获取单个文件(使用 File类型)**

    from fastapi import FastAPI, File, UploadFile
    	
    app = FastAPI()
    @app.post("/file1")
    async def file1(file: bytes = File(...)):		# 这里使用的是 File类型 接收的是一个 bytes 字节流
        return f"文件长度: {len(file)}"
     
    @app.post("/file2")
    async def file1(file: UploadFile = File(...)):		# 这里接收的是 一个文件句柄对象, 这两种方式都可以获取到上传的文件
        return f"文件名: {file.filename}, 文件大小: {len(await file.read())}"

**2.获取多个文件**

    @app.post("/file")
    async def file(files: List[UploadFile] = File(...)):
        """指定类型为列表即可"""
        for idx, f in enumerate(files):
            files[idx] = f"文件名: {f.filename}, 文件大小: {len(await f.read())}"
        return f

PS： Python如何上传文件?

首先如果想使用文件上传功能，那么你必须要安装一个包 python-multipart，直接 pip install python-multipart 即可。

    # 上传单个文件
    requests.post(url, files={"file": open(r"/file_path/file_name", "rb")})
    
    # 上传多个文件
    requests.post(url, files={"file": [
    			("files", open(r"/file_path1/file_name1"),
               	("files", open(r"/file_path2/file_name2"),
                ("files", open(r"/file_path3/file_name3")
    ]})

### 5.4 获取path类型参数

假设我们有这样一个路由：`/files/{file\_path}`，而用户传递的 file\_path 中显然是可以带 / 的，假设 file\_path 是 `/root/test.py`，那么路由就变成了 `/files//root/test.py`，显然这样子进行路由匹配就会出问题。

这是我们需要声明 file\_path 的类型为 path, eg. @app.get("/files/{file\_path:path}")

**方式一: 声明path类型**

    from fastapi import FastAPI
     
    app = FastAPI()


​     
    # 声明 file_path 的类型为 path，这样它会被当成一个整体
    @app.get("/files/{file_path:path}")
    async def get_file(file_path: str):
        return {"file_path": file_path}

**方式二: 使用Path 类型注释**

    from fastapi import FastAPI, Path
    @app.get("/items/{item-id}")
    # 这里用 Path类型来接收, 也会将后面的整体作为一个path.
    # 这里的 alias表示 重命名, 也就是将 路由函数中的 变量 item_id 与 路由中的 item-id 对应上.
    async def read_items(item_id: int = Path(..., alias="item-id")):
        return {"item_id": item_id}

PS: 也就是说, 当路径参数中 声明了 一个参数为 Path类型, 那么这个参数后面不能在接其他的路径参数。即 `@app.get("/files/{file\_path:path}/{user\_id}")` 这样的写法， user\_id就获取不到~

### 5.5 多种请求参数混合获取

#### 5.5.1 路由函数中使用类型区别

**方式一: 直接使用内置方式获取**

1.如果在路径中也声明了该参数，它将被用作路径参数

2.如果参数属于单一类型（比如 int、float、str、bool 等）它将被解释为查询参数。

   3.如果参数的类型被声明为一个 Pydantic 模型，它将被解释为请求体。

    from fastapi import FastAPI
    from pydantic.main import BaseModel
    
    app = FastAPI()
    
    class Girl(BaseModel):
        name: str
        age: int
    
    @app.get("/user/{user_id}")
    async def get_user(user_id: str, name: str, age: int, girl: Girl):
        # user_id 为路径参数
        # name/age 为params参数
        # girl 为body参数
        return {"user_id": user_id, "name": name, "age": age, "girl": girl.dict()}

**方式二: 使用 类型注释来获取**

Path(路径参数)/Query(params参数)/Body(json请求体)/Form(form表单请求体)/File(文件)

Cookie(cookie参数)/Header(请求头)

关于 Path(路径参数)/Query(params参数)/Body(json请求体)/Form(form表单请求体)/File(文件)的使用方式, 上面已经有过展示， 这里不再赘述

Cookie:

1.声明：ads\_id: Optional\[str\] = Cookie(None)

Header：

1.声明： user\_agent: Optional\[str\] = Header(None)

2.默认情况下, Header 将把参数名称的字符从下划线 (\_) 转换为连字符 (-) 来提取并记录 headers.

3.设置Header的参数 convert\_underscores 为 False, 可以取消上述的 下划线到连字符的自动转换

4.headers 是大小写不敏感的

5.声明多个header: x\_token: Optional\[List\[str\]\] = Header(None) --> 例: 使用header传输 cookie的情况

PS：

Cookie 、Header、Path 、Query是兄弟类，它们都继承自公共的 Param 类 --> 很明显Body/File/Form不是

#### 5.5.2 使用request对象获取

任何一个请求都对应一个 Request 对象，请求的所有信息都在这个 Request 对象中

获取参数方式如下：

    @app.get("/girl/{user_id}")
    async def read_girl(user_id: str,
                        request: Request):
        """路径参数是必须要体现在参数中，但是查询参数可以不写了
           因为我们定义了 request: Request，那么请求相关的所有信息都会进入到这个 Request 对象中"""
        header = request.headers  # 请求头
        method = request.method  # 请求方法
        cookies = request.cookies  # cookies
        query_params = request.query_params  # 查询参数
        body_data = await request.json()  # 获取json请求体, 该函数本质上步骤为: body = await self.body() + self._json = json.loads(body)
        return {"name": query_params.get("name"), "age": query_params.get("age"), "hobby": query_params.getlist("hobby")}

#### 5.5.3 参数顺序

路径参数应该在查询参数的前面，尽管 FastAPI 没有这个要求，但是这样写明显更舒服一些。但是问题来了，如果路径参数需要指定别名，但是某一个查询参数不需要，这个时候就会出现问题

    @app.get("/items/{item-id}")
    async def read_items(q: str,
                         item_id: int = Path(..., alias="item-id")):
     
        return {"item_id": item_id, "q": q}

显然此时 Python 的语法就决定了 item\_id 就必须放在 q 的后面，当然这么做是完全没有问题的，FastAPI 对参数的先后顺序没有任何要求，因为它是通过参数的名称、类型和默认值声明来检测参数，而不在乎参数的顺序。但此时我们就要让 item\_id 在 q 的前面要怎么做呢？

    @app.get("/items/{item-id}")
    async def read_items(*, item_id: int = Path(..., alias="item-id"),
                         q: str):
        
        return {"item_id": item_id, "q": q}

通过将第一个参数设置为 \*，使得 item\_id 和 q 都必须通过关键字传递，所以此时默认参数在非默认参数之前也是允许的。当然我们也不需要担心 FastAPI 传参的问题，你可以认为它所有的参数都是通过关键字参数的方式传递的。

## 六、数据校验

### 6.1 类型注释的数据校验方式

Path(路径参数)/Query(params参数)/Body(json请求体)/Form(form表单请求体)/File(文件)/Cookie(cookie参数)/Header(请求头)

上述这些 类在用于 类型注释时, 使用方式基本都差不多, 只是在某些方面有一些细微的差别

6.1.1 公共校验部分

这里以Query为例

    Query(
    			None,
    			alias="item-query",
    			title="Query string",
    			description="Query string for the items to search in the database that have a good match",
    			min_length=3,
    			max_length=50,
    			regex="^fixedquery$",
    			deprecated=True,
    		)
    		
    		1.修改额外的原数据:
    			alias - 起别名
    			title/description - 文档描述
    			deprecated - 参数弃用标识
    		
    		2.用于校验的参数：	
    			min_length/max_length	长度匹配
    			regex		正则匹配
    			
    		3.数值校验:
    			gt/ge/lt/le： 大于/大于等于/小于/小于等于 定义为float类型
    			
    		4.设置必填/非必填/默认值
        	Query(...)	- 设置为必填
         	Query(None) - 设置为非必填
          Query("test") - 设置默认值 test
         
         5.接收列表
         	q: Optional[List[str]] = Query(None) 定义 query参数为必填的list, 即能匹配 xxx_url?q=foo&q=bar ---> q=['foo', 'bar']
         
         6.指定数据接收类型
         	q:  Optional[str] = Query(...)
          q: str	
         
          这里支持的数据类型:
          int/float/str/bool/list/dict/set
          UUID/datetime.datetime(2008-09-15T15:53:00+05:00)/datetime.date(2008-09-15)
          datetime.time(14:23:55.003)/datetime.timedelta(时间戳秒数)/bytes/Decimal(框架内可当做float使用)
         
         	支持指定多种数据类型：
          	user_id: Union[int, str]

举个例子:

    @app.get("/user")
    async def check_length(
            password: str = Query("satori", min_length=6, max_length=15, regex=r"^satori")
    ):
        """此时的 password 默认值为 'satori'，并且传递的时候必须要以 'satori' 开头
           但是值得注意的是 password 后面的是 str，不再是 Optional[str]，因为默认值不是 None 了
           当然这里即使写成 Optional[str] 也是没有什么影响的
        """
        return {"password": password}


​    	
    @app.get("/items")
    async def read_items(
            a1: str = Query(...),	# ... 设置为必填参数
            a2: List[str] = Query(...), # 查询参数变成一个列表; 对应于 url后 a=1&a=2的情况
            b: List[str] = Query(...)
    ):
        return {"a1": a1, "a2": a2, "b": b}
    
    @app.get("/items")
    async def read_items(
            # item1 必须大于 5
            item1: int = Query(..., gt=5),		# 数值检测
            # item2 必须小于等于 7
            item2: int = Query(..., le=7),
            # item3 必须必须等于 10
            item3: int = Query(..., ge=10, le=10)
    ):
        return {"item1": item1, "item2": item2, "item3": item3}

#### 6.1.2 各类型特殊部分

**Path:**

1.路径参数总是必须的, 所以应表示为Path(...), 即使写为了 Path(None), 也还是一个必选参数

**Header：**

1.默认情况下, Header 将把参数名称的字符从下划线 (\_) 转换为连字符 (-) 来提取并记录 headers.

2.设置Header的参数 convert\_underscores 为 False, 可以取消上述的 下划线到连字符的自动转换

3.headers 是大小写不敏感的

#### 6.1.3 特殊检验类型 Filed

Filed与BaseModel一样, 由pydantic模块提供

Filed类型的用法与 Query一致, 唯一的区别就是Filed可以对所有类型的 参数进行参数校验，而不像Query/Body这类参数, 在校验时, 只能获取对应的参数。

如果有个 username参数即在 params参数中会出现， 在body参数中也会出现, 甚至于在 response 中也可能会出现， 此时使用 Filed进行参数验证就比较合适

Filed的使用 示例见 6.3

### 6.2 变量类型声明的特殊情况

#### 6.2.1 默认值与声明值可不同

    @app.get("/user/{user_id}")
    async def get_user(user_id: str, name: str = "UNKNOWN", age: int = "哈哈哈"):	# 这里的 age 需要接收一个整型，但是默认值却是一个字符串
        return {"user_id": user_id, "name": name, "age": age}

这种情况下传递的 age 依旧需要整型，只不过在不传的时候会使用字符串类型的默认值，所以类型和默认值类型不同的时候也是可以的

#### 6.2.2 同一个变量指定多个类型

    from typing import Union, Optional
    	
    @app.get("/user/{user_id}")
    async def get_user(user_id: Union[int, str], name: Optional[str] = None):
        """通过 Union 来声明一个混合类型，int 在前、str 在后。会先按照 int 解析，解析失败再变成 str
           然后是 name，它表示字符串类型、但默认值为 None（不是字符串），那么应该声明为 Optional[str]"""
        return {"user_id": user_id, "name": name}

#### 6.2.3指定数据接收类型 

q: Optional\[str\] = Query(...)

q: str

这里支持的数据类型:

`int/float/str/bool/list/dict/set`

`UUID/datetime.datetime(2008-09-15T15:53:00+05:00)/datetime.date(2008-09-15)`

`datetime.time(14:23:55.003)/datetime.timedelta(时间戳秒数)/bytes/Decimal(框架内可当做float使用)`

支持指定多种数据类型：

user\_id: Union\[int, str\]

### 6.3 BaseModel校验方式

这里是用了 Filed做示例, 如果是 Body参数, 使用Body也可，其他的类推~

示例如下:

    class UserIn(BaseModel):
        """用户信息 请求格式"""
        name: str = Field(..., min_length=1, max_length=64)
        password: str = Field(..., min_length=1, max_length=32)
        nickname: str = Field(None, max_length=64)
        phone: str = Field(None, max_length=16, regex=r'^1(3\d|4[5-9]|5[0-35-9]|6[2567]|7[0-8]|8\d|9[0-35-9])\d{8}$')	# 正则匹配
        email: str = Field(None, max_length=32, regex=r'^[a-zA-Z0-9_-]+@[a-zA-Z0-9_-]+(\.[a-zA-Z0-9_-]+)+$')
            
    class UserOut(BaseModel):
        """用户信息响应格式"""
        id: str = Field(..., max_length=32)
        name: str = Field(..., min_length=1, max_length=64)
        nickname: str = Field(None, max_length=64)
        last_login_address: str = Field(None, max_length=32)
        last_login_time: datetime.datetime = Field(None)	# 指定datetime类型
        login_counts: int = Field(0)		# 设置默认值 0
        phone: str = Field(None, max_length=16)		# 设置为非必须字段
        email: str = Field(None, max_length=32)
        enabled: int = Field(1)
        is_deleted: int = Field(0)

### 6.4 HTTP验证 security

通过输入用户名和密码来确认身份

    from fastapi import FastAPI, Depends
    from fastapi.security import HTTPBasic, HTTPBasicCredentials
    import uvicorn
     
    app = FastAPI()
     
    security = HTTPBasic()
     
    @app.get("/index")
    async def index(credentials: HTTPBasicCredentials = Depends(security)):
        return {"username": credentials.username, "password": credentials.password}

当用户访问 /index 的时候，会提示输入用户名和密码：

![image](https://alidocs.oss-cn-zhangjiakou.aliyuncs.com/a/dvlvakm1DFPw2BLV/3291b0841ffd47239b6543f1f0c3d3771684.png) 

输入完毕之后，信息会保存在 credentials，我们可以获取出来进行验证。

### 6.5 其他

#### 6.5.1 使用枚举

如果我们希望有一个参数的取值范围只能是固定的 几个值, 可以使用枚举类型

示例如下:

    from enum import Enum
    	
    class Gander(str, Enum):
        type1 = "boy"		# 这里 type1 只是一个随意变量, 没有任何其他含义
        type2 = "girl"
     
    @app.get("/users/{gander_type}")
    async def get_user(gander_type: Gander):
        return {"gander_type": gander_type}	# 这里的 gander_type 就只能是 boy/girl; 否则就会抛错

#### 6.5.2 bool 类型自动转换

对于布尔类型，FastAPI 支持自动转换

    @app.get("/{flag}")
    async def get_flag(flag: bool):
        return {"flag": flag}		# 这里 flag 为 True/true/on/yes/1 均会转换为 True; 0/False/false/off/no 均会转为 False


