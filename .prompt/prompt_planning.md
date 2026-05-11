# Prompt 出题规划

## 总体说明

### 目标项目
学生服务（student-api），后端 API 服务。

### 技术栈
Node.js 22.22.2 + TypeScript + Express + Prisma ORM + PostgreSQL 18

### 当前环境
电脑中已安装 Node 22.22.2 和 PostgreSQL 18，不需要处理安装步骤。
git 本地仓库已初始化完成，package.json 已创建且 name 为 student-api，生产依赖（express、cors、dotenv、helmet、morgan、jsonwebtoken、bcryptjs）已安装完成。

### 设计原则
每一轮聚焦一个独立任务模块，但每轮保持适中的复杂度，确保有足够的产出空间供评估发现问题。

### 规划轮次
共 10 轮，覆盖从开发环境完善到基础 API 全部可运行的完整流程。

---

## 第 1 轮

### 任务类型
工程化

### 业务领域
纯后端服务

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容
运行 bash> pnpm add -D @types/express @types/cors @types/morgan @types/jsonwebtoken typescript ts-node-dev @types/node prisma，安装开发依赖，所有包用最新版本不锁版本号。

检查根目录下的 tsconfig.json，确保配置正确：target ES2022、module commonjs、outDir dist、rootDir src，strict 开启，esModuleInterop 和 resolveJsonModule 启用，include 只包含 src，exclude 排除 node_modules 和 dist。如果配置有误或缺失，修正它。

检查根目录下的 package.json，如果其中存在 "type": "module" 配置，将其删除，因为 tsconfig 中 module 设为 commonjs，两者冲突会导致模块加载异常。

创建 src 下的子目录：src/config、src/middleware、src/routes、src/controllers、src/services、src/models、src/utils，以及 prisma 目录。

> 意图：学生需要独立处理开发依赖安装（可能遇到网络问题），同时修复 tsconfig 和 package.json 中的配置冲突，创建项目目录骨架。可考察学生检查现有配置的能力和对 CommonJS/ESM 模块系统的理解。

---

## 第 2 轮

### 任务类型
工程化

### 业务领域
纯后端服务

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容
在根目录创建 .env 文件，设置数据库连接字符串 DATABASE_URL，格式为 postgresql://用户名:密码@localhost:5432/student_db，使用默认 postgres 用户，密码为空。

运行 bash> pnpm dlx prisma init 生成 prisma/schema.prisma。

在 schema.prisma 中定义以下数据模型，所有表名使用小写复数形式：

User 表：id（自增主键）、username（唯一字符串）、password（字符串）、role（字符串，取值 admin 或 teacher）、created_at（DateTime 默认 now）
Class 表：id（自增主键）、name（字符串）、grade（字符串）、teacher_id（外键关联 User）、created_at（DateTime 默认 now）
Student 表：id（自增主键）、name（字符串）、student_no（学号，唯一字符串）、gender（字符串可选）、phone（字符串可选）、email（字符串可选）、status（字符串，默认值 "在读"）、class_id（外键关联 Class）、created_at（DateTime 默认 now）、updated_at（DateTime 自动更新）
Course 表：id（自增主键）、name（字符串）、teacher_id（外键关联 User）、credit（浮点数）、created_at（DateTime 默认 now）
Grade 表：id（自增主键）、student_id（外键关联 Student）、course_id（外键关联 Course）、score（浮点数）、exam_type（字符串，取值 期中、期末、补考）、created_at（DateTime 默认 now）

定义完成后运行 bash> pnpm dlx prisma migrate dev --name init。

> 意图：学生需要处理 Prisma 模型之间的关联关系、外键定义、枚举约束以及自动迁移。可考察对 Prisma schema 语法和数据库关系建模的掌握程度。

---

## 第 3 轮

### 任务类型
0-1代码生成

### 业务领域
纯后端服务

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容
在 src/config 目录下创建 database.ts，导入 PrismaClient，创建并导出一个 PrismaClient 单例实例，确保整个应用只初始化一次。

创建 src/config/index.ts，从 .env 读取环境变量，导出一个配置对象，包含 port（默认 3000）、jwt_secret（默认 your-secret-key）、jwt_expires_in（默认 7d）、database_url。

> 意图：学生需要实现 PrismaClient 单例模式避免连接泄漏，以及读取 .env 配置。可考察单例模式的实现是否严谨、dotenv 配置是否正确加载。

---

## 第 4 轮

### 任务类型
0-1代码生成

### 业务领域
纯后端服务

### 修改范围
单文件

### 任务难度
简单

### Prompt 内容
创建 src/middleware/errorHandler.ts，导出一个 Express 错误处理中间件，统一返回格式为 { code: 状态码, message: 错误信息, data: null } 的 JSON，未知错误统一返回 500。

> 意图：错误处理中间件看似简单，但学生容易遗漏四个参数签名、中间件注册顺序等细节。

---

## 第 5 轮

### 任务类型
0-1代码生成

### 业务领域
纯后端服务

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容
创建 src/app.ts，用 express() 创建应用实例，按正确顺序注册 cors（允许所有来源）、morgan（dev 格式）、express.json、express.urlencoded 中间件。在根路径 / 注册一个路由返回 { code: 200, message: 'Student API is running', data: null }。在所有路由之后注册 errorHandler 中间件，导出 app。

创建 src/server.ts，导入 app 和 config，监听配置的端口，启动时在控制台打印 Server is running on port 端口号。

> 意图：学生需要理解 Express 中间件注册顺序、app 与 server 的分离设计。可考察中间件顺序是否合理、模块导入方式是否一致。

---

## 第 6 轮

### 任务类型
工程化

### 业务领域
纯后端服务

### 修改范围
单文件

### 任务难度
简单

### Prompt 内容
在 package.json 的 scripts 中添加以下命令：
1. dev：ts-node-dev --respawn --transpile-only src/server.ts
2. build：tsc
3. start：node dist/server.js
4. prisma:migrate：prisma migrate dev
5. prisma:generate：prisma generate
6. prisma:studio：prisma studio

在项目根目录创建 .gitignore，忽略 node_modules、dist、.env。

运行 bash> pnpm dev 启动服务，确认终端显示 Server is running on port 3000 且没有报错。如果启动失败，排查错误并修复。

> 意图：学生需要配置 npm scripts 并实际启动服务。可考察脚本命令是否写对、编译/运行阶段是否报错、ts-node-dev 是否正常工作。

---

## 第 7 轮

### 任务类型
Feature迭代

### 业务领域
纯后端服务

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容
在 src/routes 目录下创建 index.ts，用 express.Router() 创建主路由器。

定义一个健康检查路由 GET /health，返回 { code: 200, message: 'ok', data: { uptime: process.uptime(), timestamp: new Date().toISOString() } }。

在 src/app.ts 中删除根路径 / 的旧路由，改为通过 app.use('/api/v1', router) 挂载主路由器，并添加一个根路径重定向到 /api/v1/health。

启动服务后用 bash> curl http://localhost:3000/api/v1/health 验证，确认返回正确的 JSON 响应。

> 意图：学生需要组织路由架构、实现健康检查端点，并验证 API 可达性。可考察路由挂载路径是否正确、curl 验证是否完整。

---

## 第 8 轮

### 任务类型
Feature迭代

### 业务领域
纯后端服务

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容
创建 src/middleware/auth.ts，导出一个 JWT 认证中间件，从 Authorization Header 提取 Bearer token，使用 jsonwebtoken 的 verify 验证，验证通过后将 decoded 信息挂载到 req.user，验证失败返回 401。

创建 src/services/authService.ts，包含 login 函数接收 username 和 password，查询 User 表验证密码（bcryptjs compare），验证通过生成 JWT token 并返回。

创建 src/controllers/authController.ts，包含 login 控制器调用 authService.login，按统一格式返回响应。

创建 src/routes/auth.ts，用 express.Router() 创建认证路由，POST /login 和 POST /register 均不需要认证。

在 src/routes/index.ts 中挂载 auth 路由：router.use('/auth', authRouter)。

启动服务器，先注册一个用户再登录，用 curl 验证返回的 token。

> 意图：完整的 JWT 认证链路，涉及中间件、服务层、控制器层、路由层。可考察学生对 JWT 流程、bcrypt 密码处理、req 类型扩展、TypeScript 类型声明的掌握。

---

## 第 9 轮

### 任务类型
Feature迭代

### 业务领域
纯后端服务

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容
创建 src/services/studentService.ts，包含以下函数：
- getAllStudents：查询所有学生，关联班级和课程信息
- getStudentById：根据 id 查询单个学生详情及成绩
- createStudent：创建学生，校验学号唯一性
- updateStudent：更新学生信息
- deleteStudent：删除学生（同时删除关联成绩记录）

创建 src/controllers/studentController.ts，包含对应的 CRUD 控制器，处理请求参数校验，统一返回格式 { code, message, data }。

创建 src/routes/student.ts，所有路由需要 JWT 认证中间件保护。

在 src/routes/index.ts 中挂载 student 路由：router.use('/students', studentRouter)。

启动服务后用 curl 完成完整的 CRUD 流程测试。

> 意图：完整的 Student CRUD，涉及服务层复杂查询（关联、聚合）、控制器参数校验、JWT 中间件集成。可考察 Prisma 关联查询的写法、参数校验的严谨程度、错误处理是否全面。

---

## 第 10 轮

### 任务类型
Feature迭代

### 业务领域
纯后端服务

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容
创建 src/services/classService.ts，包含班级 CRUD 功能，以及获取班级下所有学生的接口。
创建 src/services/courseService.ts，包含课程 CRUD 功能。
创建 src/services/gradeService.ts，包含录入成绩、按学生查询成绩、按课程查询成绩、统计某课程平均分功能。

为以上三个服务分别创建对应的控制器和路由文件，所有路由需要 JWT 认证。

在 routes/index.ts 中挂载 class、course、grade 路由。

启动服务后，用 curl 完成以下完整流程测试：
1. 创建班级
2. 创建学生并分配到班级
3. 创建课程
4. 录入学生成绩
5. 查询某学生的所有成绩
6. 查询某课程的平均分

> 意图：多模块 API 协同工作，涉及班级-学生关联、课程-成绩关联、统计聚合查询。可考察学生处理多表关联查询和业务逻辑的完整性。
