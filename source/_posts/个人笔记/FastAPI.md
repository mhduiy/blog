---
title: FastAPI
date: 2024-10-16 17:03:46
tags: code
categories: 个人笔记
---
## 简介

FastAPI 是一个用于构建 API 的现代、快速（高性能）的 web 框架

### 官网

https://fastapi.tiangolo.com/zh/

### 简单示例

```python
from fastapi import FastAPI  # 导入FastAPI
import uvicorn
app = FastAPI()  # 创建一个app实例
 
@app.get("/")  # 编写一个路径操作装饰器
async def root():  # 编写一个路径操作函数
    return {"你好！": "朋友。"}
 
 
if __name__ == '__main__':
    uvicorn.run(app='main:app', host="127.0.0.1", port=9999, reload=True)
```

运行：
```bash
python3 ./main.py

# 或者不需要main，直接调用unicorn服务器
uvicorn main:app --reload --port 9999
```
### UVicorn

UVicorn是一个基于ASGI（Asynchronous Server Gateway Interface）的高性能异步服务器，用于运行Python的Web应用程序。它是由编写了Starlette框架的同一团队开发的，同时也是FastAPI框架的建议服务器。UVicorn旨在提供快速的HTTP请求响应，同时支持异步处理，这使得它成为异步Python web应用程序的理想选择之一

### 生成api文档

http://127.0.0.1:9999/redoc

## 路径参数

- 第一个`/`后面的内容就是路径参数

#### 支持使用python字符串格式化语法声明路径参数

```python
@app.get("/data/{item_id}")
async def getItemId(item_id):
    return {"item_id":item_id}
```

#### 支持声明参数的类型

- 可用于提供类型检查

```python
@app.get("/data/{item_id}")
async def getItemId(item_id: int):
    return {"item_id":item_id}
```

#### 可创建枚举进行访问

```python
class ModelName(str, Enum):
    alexnet = "alexnet"
    resnet = "resnet"
    lenet = "lenet"

@app.get("/data/{model_name}")
async def getModel(model_name: ModelName):
    if model_name is ModelName.alexnet:
        return ModelName.alexnet
    elif model_name is ModelName.resnet:
        return ModelName.resnet
    else:
        return ModelName.lenet
```

#### 顺序很重要

解析路径的时候会按代码顺序从上到下解析，在上面的路径优先被解析使用

#### 使用文件路径作为路径参数

```python
# 必须指定:path表示一个路径
@app.get("/files/{file_path:path}")
def readFile(file_path):
    with open(file_path, 'r', encoding='utf-8') as file:
        return file.read()
```

## 查询参数

声明的参数不是路径参数的时候默认解析为查询参数

查询字符串是键值对的集合，这些键值对位于 URL 的 ? 之后，以 & 分隔
例如
```url
http://127.0.0.1:8000/items/?skip=0&limit=10
```
查询参数为：
- skip = 0
- limit = 10

#### 可以声明可选的查询参数

```python
@app.get("/getTel/")
async def getData(name: str, sex: str | None = None):
    print(name)
    if name is '张三':
        return "123456"
    else:
        return {quote("张三"): name}
```

## 请求体和响应体

- 请求体时客户端项API发送数据 POST,DELETE,PATCH
- 响应体时API向客户端发送数据【必须】

```python
class Item(BaseModel):
    name: str
    description: str | None = None
    price: float

app = FastAPI()  # 创建一个app实例

@app.post("/item/")
async def createItem(item: Item):
    return {"name": item.name, "des": item.description, "price" : item.price}
```