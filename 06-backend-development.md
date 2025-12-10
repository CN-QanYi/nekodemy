# 后端开发教程

深入学习 Project N.E.K.O. 项目的后端开发，包括基于 FastAPI 的异步 Web 服务器、API 开发、AI 集成、记忆系统等内容。

## 📋 目录

- [后端架构概览](#后端架构概览)
- [FastAPI 服务器开发](#fastapi-服务器开发)
  - [技术栈](#技术栈)
  - [项目结构](#项目结构)
  - [启动服务器](#启动服务器)
  - [基本路由结构](#基本路由结构)
- [API 开发指南](#api-开发指南)
  - [添加新的 API 端点](#添加新的-api-端点)
  - [使用 Pydantic 进行数据验证](#使用-pydantic-进行数据验证)
  - [错误处理](#错误处理)
- [AI 能力集成](#ai-能力集成)
- [记忆系统使用](#记忆系统使用)
- [调试和测试](#调试和测试)
- [常见开发任务](#常见开发任务)
- [性能优化](#性能优化)

---

## 后端架构概览

Project N.E.K.O. 项目的后端采用**多服务器架构**：

1. **主服务器** (`main_server.py`)
   - FastAPI Web 服务器
   - 提供 Web 界面和 API 接口
   - 处理用户请求和 WebSocket 连接
   - 端口：默认 48911

2. **记忆服务器** (`memory_server.py`)
   - FastAPI 服务器
   - 管理 AI 的记忆存储和检索
   - 语义搜索功能
   - 端口：默认 48912

3. **启动器** (`launcher.py`)
   - 统一启动入口
   - 管理多个服务的生命周期

> 💡 **提示**：关于服务器启动和端口配置，请参考 [环境搭建指南 - 启动服务器](./01-getting-started.md#6-启动服务器)。关于项目结构，请参考 [项目架构与目录结构](./03-project-structure.md)。

---

## FastAPI 服务器开发

### 技术栈

- **框架**：FastAPI
- **异步支持**：asyncio + async/await
- **数据验证**：Pydantic
- **WebSocket**：原生支持
- **模板引擎**：Jinja2

### 项目结构

```
main_server.py          # 主服务器入口
├── config/             # 配置管理
│   ├── api_providers.json
│   ├── prompts_chara.py
│   └── prompts_sys.py
├── main_helper/        # 核心对话模块
│   ├── core.py         # 对话核心逻辑
│   ├── omni_realtime_client.py
│   ├── omni_offline_client.py
│   └── tts_helper.py
├── brain/              # AI 智能体模块
│   ├── analyzer.py
│   ├── planner.py
│   ├── processor.py
│   └── task_executor.py
├── memory/             # 记忆管理
│   ├── router.py
│   ├── semantic.py
│   └── recent.py
└── utils/              # 工具函数
    ├── config_manager.py
    ├── preferences.py
    └── frontend_utils.py
```

### 启动服务器

```bash
# 使用 uv（推荐）
uv run python main_server.py

# 或激活虚拟环境后
python main_server.py

# 启动记忆服务器（需要单独终端）
uv run python memory_server.py
```

> 💡 **提示**：关于环境配置，请参考 [环境搭建指南](./01-getting-started.md)。

### 基本路由结构

```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()

@app.get("/api/endpoint")
async def your_handler():
    # 异步处理逻辑
    return {"message": "success"}

@app.post("/api/endpoint")
async def your_post_handler(request: Request):
    data = await request.json()
    # 处理逻辑
    return {"message": "success", "data": data}
```

---

## API 开发指南

### 添加新的 API 端点

#### 1. GET 请求示例

```python
from fastapi import FastAPI
from datetime import datetime

@app.get("/api/health")
async def health_check():
    """健康检查端点"""
    return {
        "status": "ok",
        "timestamp": datetime.now().isoformat()
    }
```

#### 2. POST 请求示例

```python
from fastapi import FastAPI, Request
from pydantic import BaseModel

class UserCreate(BaseModel):
    name: str
    email: str

@app.post("/api/user")
async def create_user(user: UserCreate):
    """创建用户"""
    # Pydantic 自动验证数据
    # 处理逻辑
    return {
        "message": "User created",
        "user_id": "123"
    }
```

#### 3. 带路径参数的路由

```python
@app.get("/api/user/{user_id}")
async def get_user(user_id: int):
    """获取用户信息"""
    # FastAPI 自动转换类型
    return {"user_id": user_id}
```

#### 4. 查询参数

```python
@app.get("/api/items")
async def get_items(skip: int = 0, limit: int = 10):
    """获取项目列表"""
    return {"skip": skip, "limit": limit}
```

### 使用 Pydantic 进行数据验证

```python
from pydantic import BaseModel, EmailStr, Field

class UserCreate(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    email: EmailStr
    age: int = Field(..., ge=0, le=150)

@app.post("/api/user")
async def create_user(user: UserCreate):
    # 数据已自动验证
    return {"message": "User created", "user": user}
```

### 错误处理

```python
from fastapi import HTTPException

@app.get("/api/user/{user_id}")
async def get_user(user_id: int):
    if user_id < 1:
        raise HTTPException(status_code=400, detail="Invalid user_id")
    
    # 如果用户不存在
    if not user_exists(user_id):
        raise HTTPException(status_code=404, detail="User not found")
    
    return {"user_id": user_id}
```

### 自定义异常处理器

```python
from fastapi import FastAPI, Request, status
from fastapi.responses import JSONResponse
from fastapi.exceptions import RequestValidationError

@app.exception_handler(RequestValidationError)
async def validation_exception_handler(request: Request, exc: RequestValidationError):
    return JSONResponse(
        status_code=status.HTTP_422_UNPROCESSABLE_ENTITY,
        content={"detail": exc.errors(), "body": exc.body}
    )
```

---

## AI 能力集成

### 使用对话核心模块

N.E.K.O 使用 WebSocket 进行实时对话，支持流式响应：

```python
from fastapi import WebSocket, WebSocketDisconnect
from main_helper import core

@app.websocket("/ws/{lanlan_name}")
async def websocket_endpoint(websocket: WebSocket, lanlan_name: str):
    """WebSocket 对话端点"""
    await websocket.accept()
    
    try:
        # 创建会话管理器
        session_manager = core.LLMSessionManager(
            sync_message_queue=None,
            lanlan_name=lanlan_name,
            lanlan_prompt="你的角色提示词"
        )
        
        while True:
            # 接收消息
            data = await websocket.receive_json()
            message = data.get("message", "")
            
            # 处理对话（异步）
            response = await process_message_async(message, session_manager)
            
            # 发送响应
            await websocket.send_json({"response": response})
            
    except WebSocketDisconnect:
        # 清理资源
        pass
```

### 集成 AI 智能体

```python
from brain.analyzer import analyze_intent
from brain.planner import plan_task
from brain.processor import execute_task
from brain.task_executor import TaskExecutor

@app.post("/api/agent/task")
async def agent_task(request: Request):
    """处理智能体任务"""
    data = await request.json()
    user_request = data.get("request", "")
    
    # 使用任务执行器
    executor = TaskExecutor()
    result = await executor.execute_task(user_request)
    
    return {
        "success": True,
        "result": result
    }
```

### 配置 API 服务商

编辑 `config/api_providers.json`：

```json
{
  "core": {
    "stepfun": {
      "name": "阶跃星辰",
      "base_url": "https://api.stepfun.com/v1",
      "models": ["step-1-8k", "step-1-32k"]
    },
    "qwen": {
      "name": "通义千问",
      "base_url": "https://dashscope.aliyuncs.com/api/v1",
      "models": ["qwen-turbo", "qwen-plus"]
    }
  }
}
```

> 💡 **提示**：关于 API 配置，请参考 N.E.K.O 项目的 `config/api_providers.json` 文件。

---

## 记忆系统使用

记忆系统运行在独立的服务器上（`memory_server.py`），通过 HTTP 请求访问。

### 存储记忆

```python
import httpx

async def store_memory(content: str, metadata: dict = None):
    """存储记忆到记忆服务器"""
    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"http://localhost:48912/api/memory",
            json={
                "content": content,
                "metadata": metadata or {}
            }
        )
        return response.json()

# 在 API 端点中使用
@app.post("/api/memory")
async def store_user_memory(request: Request):
    data = await request.json()
    
    result = await store_memory(
        content=data.get("content"),
        metadata=data.get("metadata", {})
    )
    
    return result
```

### 检索记忆

```python
async def search_memories(query: str, limit: int = 5):
    """从记忆服务器检索记忆"""
    async with httpx.AsyncClient() as client:
        response = await client.post(
            f"http://localhost:48912/api/memory/search",
            json={
                "query": query,
                "limit": limit
            }
        )
        return response.json()

@app.post("/api/memory/search")
async def search_memory(request: Request):
    data = await request.json()
    query = data.get("query", "")
    
    results = await search_memories(query, limit=5)
    
    return results
```

### 使用跨服务器通信模块

N.E.K.O 提供了 `main_helper.cross_server` 模块来简化跨服务器通信：

```python
from main_helper import cross_server

# 存储记忆
memory_id = await cross_server.store_memory(
    content="用户喜欢喝咖啡",
    metadata={"type": "preference"}
)

# 检索记忆
memories = await cross_server.search_memories("用户偏好", limit=5)
```

---

## 数据库和存储

### 使用 JSON 文件存储

```python
import json
import os

def load_data(filepath):
    if os.path.exists(filepath):
        with open(filepath, 'r', encoding='utf-8') as f:
            return json.load(f)
    return {}

def save_data(filepath, data):
    with open(filepath, 'w', encoding='utf-8') as f:
        json.dump(data, f, ensure_ascii=False, indent=2)

# 使用示例
data = load_data('data/users.json')
data['user_123'] = {'name': 'Alice'}
save_data('data/users.json', data)
```

### 使用 SQLite（可选）

```python
import sqlite3

def get_db():
    conn = sqlite3.connect('database.db')
    conn.row_factory = sqlite3.Row
    return conn

@app.route('/api/users', methods=['GET'])
def get_users():
    db = get_db()
    users = db.execute('SELECT * FROM users').fetchall()
    db.close()
    
    return jsonify([dict(user) for user in users])
```

---

## 调试和测试

### 日志记录

N.E.K.O 使用统一的日志配置：

```python
from utils.logger_config import setup_logging
import logging

# 设置日志
logger, log_config = setup_logging(
    service_name="MainServer",
    log_level=logging.INFO
)

@app.get("/api/test")
async def test():
    logger.debug("Debug message")
    logger.info("Info message")
    logger.warning("Warning message")
    logger.error("Error message")
    
    return {"status": "ok"}
```

### FastAPI 自动文档

FastAPI 自动生成交互式 API 文档：

- **Swagger UI**：`http://localhost:48911/docs`
- **ReDoc**：`http://localhost:48911/redoc`

### API 测试

使用 curl 测试：

```bash
# GET 请求
curl http://localhost:48911/api/health

# POST 请求
curl -X POST http://localhost:48911/api/preferences \
  -H "Content-Type: application/json" \
  -d '{"model_path": "/path/to/model"}'
```

使用 Python httpx（异步）：

```python
import httpx

async def test_api():
    async with httpx.AsyncClient() as client:
        # GET 请求
        response = await client.get("http://localhost:48911/api/health")
        print(response.json())
        
        # POST 请求
        response = await client.post(
            "http://localhost:48911/api/preferences",
            json={"model_path": "/path/to/model"}
        )
        print(response.json())
```

使用 Python requests（同步）：

```python
import requests

response = requests.get("http://localhost:48911/api/health")
print(response.json())

response = requests.post(
    "http://localhost:48911/api/preferences",
    json={"model_path": "/path/to/model"}
)
print(response.json())
```

---

## 常见开发任务

### 添加 CORS 支持

FastAPI 内置 CORS 支持：

```python
from fastapi.middleware.cors import CORSMiddleware

app = FastAPI()

# 添加 CORS 中间件
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # 允许所有来源（生产环境应指定具体域名）
    allow_credentials=True,
    allow_methods=["*"],
    allow_headers=["*"],
)
```

### 文件上传

```python
from fastapi import File, UploadFile
import os

UPLOAD_FOLDER = "uploads"
ALLOWED_EXTENSIONS = {"png", "jpg", "jpeg", "gif"}

def allowed_file(filename: str) -> bool:
    return "." in filename and \
           filename.rsplit(".", 1)[1].lower() in ALLOWED_EXTENSIONS

@app.post("/api/upload")
async def upload_file(file: UploadFile = File(...)):
    if not allowed_file(file.filename):
        raise HTTPException(status_code=400, detail="Invalid file type")
    
    # 保存文件
    file_path = os.path.join(UPLOAD_FOLDER, file.filename)
    with open(file_path, "wb") as f:
        content = await file.read()
        f.write(content)
    
    return {
        "message": "File uploaded",
        "filename": file.filename
    }
```

### WebSocket 支持

FastAPI 原生支持 WebSocket：

```python
from fastapi import WebSocket, WebSocketDisconnect

@app.websocket("/ws")
async def websocket_endpoint(websocket: WebSocket):
    await websocket.accept()
    
    try:
        while True:
            # 接收消息
            data = await websocket.receive_json()
            print(f"Received: {data}")
            
            # 发送响应
            await websocket.send_json({
                "message": "Server received",
                "data": data
            })
    except WebSocketDisconnect:
        print("Client disconnected")
```

### 静态文件服务

```python
from fastapi.staticfiles import StaticFiles

# 挂载静态文件目录
app.mount("/static", StaticFiles(directory="static"), name="static")
```

### 模板渲染

```python
from fastapi.templating import Jinja2Templates
from fastapi import Request
from fastapi.responses import HTMLResponse

templates = Jinja2Templates(directory="templates")

@app.get("/", response_class=HTMLResponse)
async def read_root(request: Request):
    return templates.TemplateResponse(
        "index.html",
        {"request": request, "title": "N.E.K.O"}
    )
```

---

## 性能优化

### 使用缓存

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def expensive_function(arg: str) -> str:
    # 耗时操作
    return arg.upper()

# 在异步函数中使用
@app.get("/api/cached")
async def cached_endpoint():
    result = expensive_function("test")
    return {"result": result}
```

### 异步处理

FastAPI 原生支持异步，充分利用 async/await：

```python
import asyncio

async def background_task(data: dict):
    """后台异步任务"""
    await asyncio.sleep(1)
    # 处理任务
    pass

@app.post("/api/async")
async def async_task(request: Request):
    data = await request.json()
    
    # 启动后台任务（不等待完成）
    asyncio.create_task(background_task(data))
    
    return {"message": "Task started"}
```

### 数据库连接池

如果使用数据库，建议使用连接池：

```python
from sqlalchemy.ext.asyncio import create_async_engine, AsyncSession
from sqlalchemy.orm import sessionmaker

engine = create_async_engine("sqlite+aiosqlite:///./database.db")
AsyncSessionLocal = sessionmaker(
    engine, class_=AsyncSession, expire_on_commit=False
)

async def get_db():
    async with AsyncSessionLocal() as session:
        yield session
```

### 响应压缩

使用中间件压缩响应：

```python
from fastapi.middleware.gzip import GZipMiddleware

app.add_middleware(GZipMiddleware, minimum_size=1000)
```

---

## 相关文档

在开始后端开发之前，建议先阅读：

- [环境搭建指南](./01-getting-started.md) - 环境配置和项目启动
- [项目架构与目录结构](./03-project-structure.md) - 了解项目结构
- [开发实战入门](./04-development-basics.md) - 完成第一个功能

## 下一步

完成后端开发学习后，可以继续学习：

- [前端开发教程](./05-frontend-development.md) - 了解前端如何调用后端 API

---

**遇到问题？** 在 [GitHub Issues](https://github.com/Project-N-E-K-O/nekodemy/issues) 提交你的问题，我们会及时解答！

