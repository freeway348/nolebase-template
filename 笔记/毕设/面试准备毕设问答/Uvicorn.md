是的，系统使用了 **Uvicorn**。它是 FastAPI 官方推荐的 ASGI 服务器，用来运行我们的后端应用。

### 它做了什么事？

Uvicorn 可以理解成一个**接线员**。当浏览器（前端）发送 HTTP 请求到服务器时，Uvicorn 负责接收这些请求，然后把它们转交给 FastAPI 处理，最后再把 FastAPI 的响应返回给浏览器。

### 在哪里用到？

1. 在 `backend/main.py` 文件底部有启动代码：
   ```python
   if __name__ == "__main__":
       uvicorn.run("main:app", host="127.0.0.1", port=8000, reload=True)
   ```
2. 平时启动后端时用的命令也是：
   ```bash
   uvicorn main:app --reload --port 8000
   ```

### 为什么要用 Uvicorn？

FastAPI 本身是一个 Web 框架，它不能直接与网络通信。它需要依赖一个 ASGI 服务器来接收网络请求。Uvicorn 就是一个高性能的 ASGI 服务器，支持异步，能和 FastAPI 的异步特性完美配合，让我们的系统能同时处理多个请求而不阻塞。

**一句话总结**：Uvicorn 是接线员，FastAPI 是业务员。接线员把请求转给业务员，业务员处理后，接线员再把结果返回。