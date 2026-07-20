# 基本介绍
### 一、FastAPI介绍

- FastAPI的性能比Django和Flask都要好，但是框架附带的功能最弱，如果要使用其他功能，可能需要接入其他的框架

#### 1. Uvicorn  -->  异步Web服务器

- FastAPI 本身是一个 Web 框架，它不能直接与网络通信。它需要依赖一个 ASGI 服务器来接收网络请求。Uvicorn 就是一个高性能的 ASGI 服务器，支持异步，能和 FastAPI 的异步特性完美配合，让我们的系统能同时处理多个请求而不阻塞。

**一句话总结**：Uvicorn 是接线员，FastAPI 是业务员。接线员把请求转给业务员，业务员处理后，接线员再把结果返回。

### 二、项目创建

- 环境配置完成后，创建FastAPI项目，初始项目中有两个文件：main.py和test_main.http![](assets/Pasted%20image%2020260719232434.png)
	- 服务器在定义URL时，用了什么method（get/post/...)，那么客户端在请求这个URL时就要用到相同的URL
		- URL就是\@app.get注解标明的地址
	- 客户端只要访问了指定的URL（例如 app.get("/")对应的URL），就会执行该注解下的函数（如：def root()函数），并将返回值返回给客户端
#### `await` 后的操作**何时执行**？

> **正确答案：** 
> `await` 会**立即发起**（启动）后面的异步操作（比如发起网络请求、读数据库），但**当前函数会在此处“挂起”**，把控制权交还给事件循环。**`await` 后面的剩余代码（如 `return` 或 `print`），只有在等待的异步操作**“完成”（拿到结果或抛出异常）之后**，才会被重新调度并继续执行**。

##### 1. `async` 关键字（定义者）
- **作用**：用于定义**协程函数**（`async def`）。调用该函数时，**不会立即执行代码**，而是返回一个**协程对象**。
- **本质**：它告诉 Python 解释器：“这个函数内部可能会挂起，允许在 `await` 处切出去执行别的任务”。
- **注意**：`async` 函数内部**必须运行在事件循环（Event Loop）中**（如 FastAPI 启动时自动创建的循环）。

##### 2. `await` 关键字（等待者）
- **作用**：**必须**写在 `async` 函数内部，用于等待一个“可等待对象”（`Awaitable`，如协程、`asyncio.Task`、`Future`）的**执行结果**。
- **执行时机（填空答案）**：
  1. 执行到 `await` 时，Python **立即**发起（调度）后面的异步操作（例如发送 HTTP 请求）。
  2. 发起后，当前协程会**立即挂起**，将 CPU 控制权交还给事件循环，让事件循环去处理其他任务（处理其他用户请求）。
  3. **只有当被等待的操作完成了**（数据返回或报错），事件循环才会把控制权交回来，当前函数才会**恢复执行** `await` 后面的下一行代码。
- **核心**：`await` 解决了 **“阻塞”** 问题，等待期间服务器不会卡死。

##### 3. 重要补充（极易踩坑）
- **`await` 后必须跟“可等待对象”**：不能是普通函数或变量，必须是协程对象、`asyncio.Task` 或支持异步的库对象（如 `asyncpg.fetch()`）。
- **`async` 里千万别混用同步阻塞库**：如果在 `async` 函数里使用 `time.sleep(5)`（同步阻塞）而不加 `await`，整个事件循环会被卡死 5 秒，所有用户都进不来。必须用 `await asyncio.sleep(5)`。
- **两种编程风格（FastAPI 场景）**：
  - **全异步**：`async def` + `await` + 异步库（`httpx.AsyncClient`, `asyncpg`）→ **高并发首选**。
  - **同步模式**：普通 `def` + 同步库（`requests`, `sqlalchemy`）→ FastAPI 会自动放到线程池执行，不卡死事件循环，但性能略逊于纯异步。

---

#### 🎯 一句话终极记忆法
**`async` 给了函数“停车”的资格，`await` 是在“红灯”处主动停下车并把油门交给别的司机；只有当“绿灯”（异步操作完成）亮起，这辆车才能继续往前开。**
（它不会无缘无故等待，只有在“事件循环通知完成”时才会继续执行后续代码。）

### 三、Pydantic介绍

- 在FastAPI中，系统会通过Pydantic实现对前端上传的数据的校验
- Pydantic是python中最快的数据校验库之一，核心逻辑是使用Rust编写的
- pydantic可以发出JSON模式，允许与其他工具轻松集成
#### 基本使用

```
FastAPI依赖pydantic，所以pydantic一般与FastAPI是共同安装的
```
- Pydantic通过定义模型，以及在模型中指定字段来对值进行校验
```python
# 示例代码

from datetime import date
from pydantic import BaseModel
from typing import List
# from typing import Optional                     

class User(BaseModel):
    id: int
    name: str = 'John Doe'  # name应该为 str 类型，默认值为 John Doe
    date_joined: date | None # date_joined应该为 date 类型，如果不传值，则默认为None
    # 等价于：date_joined|None = Optional[date]   --> 该方法不需要导入Optional库
    # 等价于：date_joined: Optional[date]         --> 该方法需要导入Optional库
    departments: List[str] | None
    
    # 后两个字段为可选字段，如果不传入值，则默认为None

external_data = {
    'id': 123,  
    'date_joined': '2030-06-01', 
    # date_joined 传入的是字符串，但符合date类型的格式，所以pydantic会自动将其转为date类型
    'departments': ['技术部', '产品部'],
}

user = User(**external_data) # 进行数据校验
print(user.id, user.name)
# 输出：123 John Doe

# 打包数据转换成字典
print(user.model_dump()) 
# date_joined在字典里也会以date类型存在，而不会变成str类型
# 输出：{'id': 123, 'name': 'John Doe', 'date_joined': datetime.date(2030, 6, 1), 'departments': ['技术部', '产品部']}

# 如果要打包成JSON输出，则使用 print(user.model_dump_json())
```

### 四、请求数据

- 请求数据包括路由参数、Body参数等
#### 1. 路由参数
##### 1）路径参数

- 路径参数：在路由（URL）的path上定义好的，使用`{ }` 来包裹参数
```python
# /item/111  --> 正常运行
# /item      --> 报错
@app.get("/item/{item_id}")
async def read_item(item_id: int):
	return {"item_id": item_id}
	# 返回给前端，这里写的是字典，但是FastAPI底层会调用json.dump()将字典序列化（或者说：打包）为JSON数据，并通过HTTP协议发送出去
```
- 路径参数属于URL的一部分，不能少
- 路径参数可以通过`fastapi.Path`来实现校验
```python
# 校验路径参数
from typing import Annotated

@app.get("/item/{item_id}")
# async def read_items(item_id: int=Path(ge=2)):  --> 相同作用，写法更简便，只有item_id大于等于 2 时才能通过校验
# ge：大于等于；  
async def read_items(item_id: Annotated(int, Path(description='yyy', gt=2))):
    results = {"item_id": item_id}
    return results
```
##### 2）查询参数

- 查询参数不用在定义URL时指定，而是通过`?key=value`进行拼接（`?`是分隔符，表示后续内容是查询参数）
- 一般查询参数后都会跟一个默认值，如果不跟默认值，则说明这个查询参数是必须传值的
```python
# /item?skip=1&limit=2   -->  传回指定值
# /item                  -->  传回默认值 
@app.get("/item")
async def read_item(skip: int = 0, limit: int = 10): # 设置默认值，查询参数可以不传值
    return {"skip": skip, "limit": limit} 
    # 返回给前端，这里写的是字典，但是FastAPI底层会调用json.dump()将字典序列化（或者说：打包）为JSON数据，并通过HTTP协议发送出去
```
- 查询参数同样可以通过`fastapi.Path`来实现校验，需要通过Annotated的Query函数
#### 2. Body参数

- 如果需要获取到通过Body传递的参数，那么必须使用pydantic先定义一个包含所有字段的模型。
