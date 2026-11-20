# 进阶练习题库 · Web 后端主线（路线图阶段 4）

> 覆盖 Django / FastAPI、SQLAlchemy、REST API、数据库缓存与异步任务、鉴权、部署。
> 每道题都标注【知识点】，答错请回到对应知识点或路线图阶段 4 复习。答案紧跟每题之后。

---

## 1. REST API 设计

**1.（单选）REST 中，对资源进行"创建、读取、更新、删除"分别对应哪个 HTTP 方法？（ ）**
A. GET / POST / PUT / DELETE　B. POST / GET / PUT / DELETE　C. PUT / GET / POST / DELETE　D. POST / GET / DELETE / PUT
【知识点】REST API·HTTP 方法
【答案】B（创建 POST、读取 GET、更新 PUT/PATCH、删除 DELETE）。

**2.（填空）GET 请求应该是____的（无副作用）；POST 用于____；PUT 通常是____的（幂等）。**
【知识点】REST API·幂等性
【答案】幂等（多次调用结果一致、无副作用）；创建/提交数据；幂等（重复提交结果相同）。

**3.（单选）下列哪个 HTTP 状态码表示"请求成功"、"资源未找到"、"服务器内部错误"？（ ）**
A. 200 / 400 / 500　B. 200 / 404 / 500　C. 201 / 404 / 403　D. 200 / 404 / 401
【知识点】REST API·状态码
【答案】B。

**4.（填空）401 表示____；403 表示____；201 表示____；400 表示____。**
【知识点】REST API·状态码
【答案】未认证（未登录/凭证无效）；已认证但无权限；创建成功（通常配合 POST）；客户端请求参数错误。

**5.（简答）RESTful 的资源命名有什么规范？举例一个"获取用户列表"和"获取单个用户"的 URL 设计。**
【知识点】REST API·资源命名
【答案】用名词复数表示资源、层级清晰、动词用 HTTP 方法表达。
```
GET /api/users        # 用户列表
GET /api/users/123    # 单个用户
POST /api/users       # 创建用户
PUT /api/users/123    # 更新用户
```

**6.（代码）分页接口通常返回什么结构？写出一个常见的分页响应 JSON。**
【知识点】REST API·分页
【答案】
```json
{
  "total": 100,
  "page": 1,
  "page_size": 20,
  "items": [ { ... } ]
}
```

---

## 2. Django 框架

**7.（单选）Django 采用的架构模式是？（ ）**
A. MVC　B. MTV（Model-Template-View）　C. MVVM　D. 三层架构
【知识点】Django·MTV架构
【答案】B（Model 数据层、Template 模板层、View 视图层，本质与 MVC 对应）。

**8.（填空）Django 中，创建项目用命令____；创建应用用命令____；生成迁移文件用____；应用迁移到数据库用____。**
【知识点】Django·项目与命令
【答案】`django-admin startproject 项目名`；`python manage.py startapp 应用名`；`python manage.py makemigrations`；`python manage.py migrate`。

**9.（单选）Django 中 Model 的作用是？（ ）**
A. 处理 HTTP 请求　B. 定义数据表结构并与数据库交互（ORM）　C. 渲染页面　D. 路由分发
【知识点】Django·Model/ORM
【答案】B。

**10.（代码）写一个简单的 Django 模型 Book（含 title、price、published_date 字段），并说明如何查询所有记录。**
【知识点】Django·Model
【答案】
```python
from django.db import models

class Book(models.Model):
    title = models.CharField(max_length=100)
    price = models.DecimalField(max_digits=6, decimal_places=2)
    published_date = models.DateField()

# 查询所有
books = Book.objects.all()
```

**11.（填空）Django 的路由写在____文件中；视图函数通过____装饰器或 path/re_path 与 URL 绑定。**
【知识点】Django·URL路由
【答案】urls.py；（在 urls.py 中用 `path()` 把 URL 映射到视图函数）。

**12.（判断）Django 自带了 Admin 后台、用户认证系统和 ORM，属于"全家桶"框架。（ ）**
【知识点】Django·特性
【答案】对。

**13.（简答）Django 的 request 对象从哪里来？视图函数如何返回一个 JSON 响应？**
【知识点】Django·视图
【答案】request 由 Django 自动传入视图函数，封装了 HTTP 请求信息。返回 JSON：
```python
from django.http import JsonResponse
def api(request):
    return JsonResponse({"name": "Tom"})
```

---

## 3. FastAPI 框架

**14.（多选）FastAPI 的特点包括？（ ）**
A. 原生支持异步 async/await　B. 基于 Pydantic 做数据校验　C. 自动生成 OpenAPI/Swagger 文档　D. 依赖注入
【知识点】FastAPI·特点
【答案】ABCD。

**15.（代码）写一个 FastAPI 的 GET 接口 `/hello/{name}`，返回 `{"hello": name}`。**
【知识点】FastAPI·路径参数
【答案】
```python
from fastapi import FastAPI
app = FastAPI()

@app.get("/hello/{name}")
def hello(name: str):
    return {"hello": name}
```

**16.（填空）FastAPI 中用____模型（继承自 pydantic 的____）做请求体和响应体的数据校验。**
【知识点】FastAPI·Pydantic
【答案】Pydantic 模型；BaseModel。

**17.（代码）定义一个 Pydantic 模型 User（name: str, age: int），并用它做 POST 请求体校验。**
【知识点】FastAPI·请求体校验
【答案】
```python
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int

@app.post("/users")
def create_user(user: User):
    return {"created": user.name}
```

**18.（简答）FastAPI 的依赖注入（Depends）有什么作用？**
【知识点】FastAPI·依赖注入
【答案】把公共逻辑（如数据库连接、鉴权、参数校验）抽成可复用的依赖，由框架自动注入到路由函数，避免重复代码、便于测试。

**19.（单选）FastAPI 的自动交互文档地址通常是？（ ）**
A. /admin　B. /docs（Swagger UI）　C. /api　D. /debug
【知识点】FastAPI·文档
【答案】B（还有 /redoc）。

---

## 4. SQLAlchemy / ORM

**20.（填空）ORM 的全称是____，作用是____。**
【知识点】ORM·概念
【答案】Object-Relational Mapping（对象关系映射）；把数据库表映射为 Python 类、把 SQL 操作映射为对象方法调用。

**21.（代码）用 SQLAlchemy 定义一个 User 模型，并用 Session 插入一条记录。**
【知识点】SQLAlchemy·模型与Session
【答案】
```python
from sqlalchemy import create_engine, Column, Integer, String
from sqlalchemy.orm import declarative_base, sessionmaker

Base = declarative_base()

class User(Base):
    __tablename__ = "users"
    id = Column(Integer, primary_key=True)
    name = Column(String(50))

engine = create_engine("sqlite:///app.db")
Base.metadata.create_all(engine)
Session = sessionmaker(bind=engine)
session = Session()
session.add(User(name="Tom"))
session.commit()
```

**22.（填空）SQLAlchemy 查询中，`filter` 用于____；`filter_by` 用____做简单等值过滤；查询所有记录用____。**
【知识点】SQLAlchemy·查询
【答案】复杂条件表达式（如 `User.age > 18`）；关键字参数（如 `filter_by(name="Tom")`）；`session.query(User).all()`。

**23.（代码）用 SQLAlchemy 查询 age 大于 18 且 name 以 "T" 开头的用户。**
【知识点】SQLAlchemy·查询过滤
【答案】
```python
users = session.query(User).filter(
    User.age > 18,
    User.name.like("T%")
).all()
```

**24.（单选）一对多关系中，外键应定义在？（ ）**
A. "一"的一方　B. "多"的一方　C. 任意一方　D. 单独一张表
【知识点】SQLAlchemy·关系
【答案】B（多的一方通过 ForeignKey 指向一的一方）。

**25.（代码）定义一个"作者 Author 与书籍 Book 一对多"关系（Book 含 author_id 外键和 relationship）。**
【知识点】SQLAlchemy·relationship
【答案】
```python
from sqlalchemy import ForeignKey
from sqlalchemy.orm import relationship

class Author(Base):
    __tablename__ = "authors"
    id = Column(Integer, primary_key=True)
    name = Column(String(50))
    books = relationship("Book", back_populates="author")

class Book(Base):
    __tablename__ = "books"
    id = Column(Integer, primary_key=True)
    title = Column(String(100))
    author_id = Column(Integer, ForeignKey("authors.id"))
    author = relationship("Author", back_populates="books")
```

**26.（填空）Alembic 是 SQLAlchemy 的____工具；生成迁移脚本用____命令。**
【知识点】SQLAlchemy·迁移
【答案】数据库迁移（版本管理）；`alembic revision --autogenerate`。

---

## 5. 数据库、缓存与异步任务

**27.（单选）Redis 最常用于？（ ）**
A. 关系型存储　B. 缓存、分布式锁、计数器等（内存 KV 存储）　C. 全文搜索　D. 数据仓库
【知识点】缓存·Redis
【答案】B。

**28.（填空）缓存三大常见问题：缓存穿透（查不存在的数据）、____（大量 key 同时过期）、____（热点 key 失效打爆后端）。**
【知识点】缓存·三大问题
【答案】缓存雪崩；缓存击穿。

**29.（简答）Celery 的作用是什么？它通常需要配合什么消息中间件使用？**
【知识点】异步任务·Celery
【答案】Celery 是分布式异步任务队列，用于把耗时任务（发邮件、处理图片、统计）异步执行，避免阻塞请求。常配合 Redis 或 RabbitMQ 作 broker（消息中间件）。

**30.（代码）写一个 Celery 任务装饰器示例，并说明 `.delay()` 的作用。**
【知识点】异步任务·Celery
【答案】
```python
from celery import Celery
app = Celery("tasks", broker="redis://localhost:6379/0")

@app.task
def send_email(to):
    print("发送邮件给", to)

send_email.delay("user@example.com")   # 异步投递，不阻塞
```

**31.（判断）对于频繁读取、变化不频繁的数据，可以先查 Redis，命中直接返回，未命中再查数据库并回填缓存（Cache-Aside 模式）。（ ）**
【知识点】缓存·Cache-Aside
【答案】对。

---

## 6. 鉴权（Session/Cookie、JWT、OAuth2）

**32.（填空）Session 是存储在____端的用户状态，通常用一个____（会话ID）关联客户端；Cookie 存储在____端。**
【知识点】鉴权·Session/Cookie
【答案】服务（服务端）；session_id（Cookie 中保存）；客户（浏览器）。

**33.（单选）JWT 由哪三部分组成？（ ）**
A. Header / Payload / Signature　B. Header / Body / Token　C. Token / Payload / Key　D. Secret / Payload / Header
【知识点】鉴权·JWT
【答案】A。

**34.（简答）JWT 相比 Session 的优缺点是什么？**
【知识点】鉴权·JWT
【答案】优点：无状态、服务端不存会话、便于分布式/跨域；缺点：无法主动失效（除非额外黑名单）、token 体积较大、一旦泄露在有效期内可被滥用。

**35.（填空）OAuth2 的常见授权流程中，"授权码模式"会先返回一个____，再用它换取____（access_token）。**
【知识点】鉴权·OAuth2
【答案】授权码（code）；访问令牌（access_token）。

**36.（代码）在 FastAPI 中，如何用依赖注入从请求头解析并校验 JWT（写思路/框架代码即可）。**
【知识点】鉴权·JWT 实现
【答案】
```python
from fastapi import Depends, Header, HTTPException
import jwt

def get_current_user(authorization: str = Header(None)):
    if not authorization:
        raise HTTPException(401, "缺少凭证")
    token = authorization.split(" ")[1]     # "Bearer <token>"
    payload = jwt.decode(token, "SECRET", algorithms=["HS256"])
    return payload["user_id"]

@app.get("/me")
def me(user_id: int = Depends(get_current_user)):
    return {"user_id": user_id}
```

---

## 7. 部署

**37.（填空）生产环境通常用 Nginx 作为____服务器（反向代理、负载均衡、静态资源），用 Gunicorn/uWSGI 作为 Python 的____服务器运行 Django/FastAPI 应用。**
【知识点】部署·Nginx与WSGI
【答案】反向代理/Web；WSGI（应用）服务器。

**38.（简答）为什么生产环境不直接用 `python manage.py runserver`（开发服务器）？**
【知识点】部署·生产环境
【答案】runserver 是单线程、未优化的开发服务器，不具备并发处理、性能、安全和稳定性，仅用于开发调试。

**39.（单选）Docker 的作用是？（ ）**
A. 数据库　B. 容器化打包应用及其依赖，保证环境一致、便于部署　C. 反向代理　D. 消息队列
【知识点】部署·Docker
【答案】B。

**40.（代码）写一个最简 Dockerfile，用于运行一个 FastAPI/Django 应用（思路：基于 python 镜像、安装依赖、暴露端口、启动）。**
【知识点】部署·Dockerfile
【答案】
```dockerfile
FROM python:3.12-slim
WORKDIR /app
COPY requirements.txt .
RUN pip install -r requirements.txt
COPY . .
EXPOSE 8000
CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "8000"]
```

**41.（填空）CI/CD 中，CI 指____（持续集成），CD 指____（持续交付/部署）；GitHub Actions 通过____文件定义流水线。**
【知识点】部署·CI/CD
【答案】Continuous Integration；Continuous Delivery/Deployment；YAML（`.github/workflows/*.yml`）。

**42.（简答）一个 Web 应用上线后，还需要关注哪些运维指标？（至少列 3 个）**
【知识点】部署·监控
【答案】响应时间/接口延迟、错误率、QPS（每秒请求数）、CPU/内存使用率、日志与告警等。

---

## 附：本套题对应路线图阶段

| 章节 | 对应路线图阶段 4 主题 |
|------|----------------------|
| REST API 设计 | REST API |
| Django / FastAPI | Web 框架 |
| SQLAlchemy | ORM |
| 数据库缓存异步任务 | 缓存/队列 |
| 鉴权 | 鉴权 |
| 部署 | 部署 |

> 建议学完阶段 1~3 后再刷本套题，配合一个"注册登录 + 增删改查 + 缓存 + 异步任务 + Docker 部署"的实战项目一起做，效果最好。
