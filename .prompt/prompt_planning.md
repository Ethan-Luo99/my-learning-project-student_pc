# Prompt 出题规划

## 总体说明

### 目标项目
学生门户 PC 端（student-pc），前端页面。

### 技术栈
Vue 3 + Vite 6 + Vue Router + Pinia + Axios + Element Plus + JavaScript（非 TypeScript）

### 当前环境
Vue 3 项目已手动创建（避免安装失败），package.json 中 vue、vite、@vitejs/plugin-vue 已安装完毕。
其余依赖（vue-router、pinia、axios、element-plus 等）尚未安装，需要学生自行安装。
vite.config.js 只有最简配置（仅注册了 vue 插件），src 下只有 main.js 和 App.vue，缺少页面目录结构。
index.html 已存在且正常引用了 /src/main.js。
后端 student-api 已在 http://localhost:3000 运行，提供 RESTful JSON API，统一响应格式 { code, message, data }，JWT Bearer 认证。

### 设计原则
每一轮聚焦一个独立任务模块，但每轮保持适中的复杂度，确保有足够的产出空间供评估发现问题。

### 规划轮次
共 10 轮，覆盖从开发环境完善到完整功能页面可运行的流程。

---

## 第 1 轮

### 任务类型
工程化

### 业务领域
大前端与服务端类

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容

检查根目录下的 vite.config.js，添加以下配置：
1. resolve.alias 将 @ 指向 src 目录
2. server.proxy 将 /api 请求代理到 http://localhost:3000，注意后端 API 实际路径为 /api/v1，前端请求时统一以 /api 为前缀，代理时需要重写路径去掉 /api 前缀
3. 添加 Element Plus 自动导入的配置（使用 unplugin-vue-components 和 unplugin-auto-import）

安装以下依赖（使用 pnpm add）：

运行时依赖：vue-router、pinia、axios、element-plus、@element-plus/icons-vue

开发依赖：unplugin-vue-components、unplugin-auto-import

检查 package.json，确保 type 为 module（当前项目的 tsconfig 已不是问题，前端使用 ESM 是正确的）。

创建 src 下的子目录：src/router、src/views、src/api、src/stores、src/components、src/utils、src/assets

> 意图：前端工程的必要配置，包括路径别名、API 代理避免跨域、Element Plus 组件自动注册、项目目录骨架搭建。可考察学生对 Vite 配置、代理原理、自动导入工具链的掌握。

---

## 第 2 轮

### 任务类型
工程化

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

在 src/router 目录下创建 index.js，使用 createRouter 创建路由实例，路由模式使用 createWebHashHistory。

定义以下路由（先创建对应的视图占位文件，每个视图组件内容暂时只导出一个带页面标题的空白组件）：
- /login -> Login 视图（无需认证）
- / -> Home 视图（需要认证）
- /courses -> Courses 视图（需要认证）
- /grades -> Grades 视图（需要认证）
- /campus-life -> CampusLife 视图（需要认证）
- /profile -> Profile 视图（需要认证）

使用 beforeEach 全局导航守卫实现路由鉴权：检查 localStorage 中是否存在 token，如果不存在且目标路由不是 /login，则重定向到 /login。如果存在 token 且目标是 /login，则重定向到 /。

在 src/main.js 中导入并使用 router 实例，确保路由系统能正常工作。

在 src/App.vue 中添加 router-view 组件，并包裹一个 Element Plus 的 el-config-provider（使用默认配置即可）。

> 意图：搭建完整的前端路由体系，包括路由配置、懒加载（如果学生能考虑到）、导航守卫鉴权。可考察对 Vue Router 的掌握程度和鉴权逻辑的严谨性。

---

## 第 3 轮

### 任务类型
0-1代码生成

### 业务领域
大前端与服务端类

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容

在 src/api 目录下创建 request.js，使用 axios.create 创建一个 Axios 实例，baseURL 设为 /api/v1（因为 vite 代理会自动转发到后端）。

添加请求拦截器：从 localStorage 读取 token，如果存在则添加到请求头的 Authorization 字段，格式为 Bearer token。

添加响应拦截器：提取响应数据的 data 字段直接返回（后端返回格式为 { code, message, data }）。如果 code 不是 200，用 Element Plus 的 ElMessage 显示错误提示。如果 code 是 401（未授权），清除 localStorage 中的 token 并跳转到登录页。

在 src/api 目录下创建 auth.js，封装以下 API 函数：
- login(data) 发送 POST /auth/login，参数为 { username, password }
- register(data) 发送 POST /auth/register，参数为 { username, password, role }

确保请求函数可以被导入使用。

> 意图：构建统一的 HTTP 请求层，涵盖拦截器、错误处理、自动携带 Token 等。可考察学生对 Axios 拦截器的理解和对后端接口约定的适配能力。

---

## 第 4 轮

### 任务类型
0-1代码生成

### 业务领域
大前端与服务端类

### 修改范围
模块内多文件

### 任务难度
一般

### Prompt 内容

在 src/stores 目录下创建 user.js，用 Pinia 的 defineStore 定义用户状态仓库。

状态包含：token（从 localStorage 初始化）、userInfo（用户信息对象，初始为 null）。

actions 包含：
- login(credentials)：调用 auth API 的 login 函数，成功后保存 token 到 state 和 localStorage
- logout()：清除 token 和 userInfo，清除 localStorage，跳转到登录页
- fetchUserInfo()：如果已登录，通过 GET /users/me 或从 JWT 解析获取当前用户信息（先实现从 JWT payload 解析 username、role 等基本信息，不需要额外 API 调用）

getters 包含：
- isLoggedIn：返回 token 是否存在
- username：返回 userInfo 中的用户名
- userRole：返回 userInfo 中的角色

在 src/main.js 中导入 Pinia，使用 createPinia 创建实例并注册到应用。

> 意图：Pinia 状态管理的配置和使用，涉及持久化 token、用户认证状态管理。可考察学生对 Pinia 设计模式的理解、actions/getters 的正确使用。

---

## 第 5 轮

### 任务类型
0-1代码生成

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/views/Login.vue，实现登录页面。

页面居中布局，使用 Element Plus 组件：
1. 一个 el-card 卡片居中显示，宽度 420px，带标题"学生门户登录"
2. 卡片内使用 el-form 包含两个表单项：用户名（el-input，带 User 图标前缀）和密码（el-input，type=password，带 Lock 图标前缀）
3. 表单项添加非空校验规则
4. 下方加一个 el-button 提交按钮，宽度 100%，type=primary，文案"登录"
5. 登录按钮下方加一个链接或小按钮"还没有账号？去注册"，点击后切换为注册表单模式

注册模式时表单增加角色选择 el-radio-group（选项：学生/教师，默认选中"学生"）和确认密码字段。

点击提交时调用 user store 的 login 方法，登录成功后跳转到首页 /。登录失败时用 ElMessage 显示后端返回的错误信息。

> 意图：完整的登录/注册页面，涉及表单校验、模式切换、与 store 和 API 的联动。可考察表单处理、组件交互、状态管理的综合运用。

---

## 第 6 轮

### 任务类型
Feature迭代

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/components/AppHeader.vue，使用 Element Plus 的 el-menu 实现顶部导航栏。

导航栏样式：
- 背景色 #1890ff，文字白色
- mode=horizontal
- 菜单项从左到右：首页（/）、我的课程（/courses）、成绩查询（/grades）、校园生活（/campus-life，带子菜单下拉）、个人中心（/profile）
- el-menu 通过 router 属性启用路由模式
- 右侧显示当前登录用户名和一个退出登录按钮

在 src/views/Home.vue 中实现首页，布局如下：
1. 顶部欢迎区域：左侧显示"欢迎回来" + 用户名，右侧显示当前日期
2. 中间三个 el-card 快捷入口卡片横排：查看课表（链接到 /courses）、最新成绩（链接到 /grades）、今日菜品（链接到 /campus-life），每个卡片带 Element Plus 图标和简短文案
3. 将 AppHeader 放到 App.vue 中，在所有需要登录的页面上方显示（路由出口上方），登录页面不需要显示导航栏

> 意图：构建可复用的导航组件和首页展示页面，涉及路由联动、导航状态高亮。可考察组件复用、导航菜单与路由的集成。

---

## 第 7 轮

### 任务类型
Feature迭代

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/api/student.js，封装以下 API 调用：
- getStudents(params) 发送 GET /students，params 可选 page 和 pageSize
- getStudentById(id) 发送 GET /students/:id

创建 src/api/course.js，封装：
- getCourses() 发送 GET /courses
- getCourseById(id) 发送 GET /courses/:id

在 src/views/Courses.vue 中实现"我的课程"页面：
1. 顶部标题"我的课程"
2. 上半部分为本周课表：用 el-table 模拟周视图，横向表头为星期一至星期五，纵向为第1-8节，每个单元格显示课程名和教室（先用模拟数据占位，后续接入真实 API）
3. 下半部分为课程列表：用 el-card 卡片网格展示所有课程，每行 3-4 个卡片，每个卡片包含课程名称、授课教师、学分、课程简介
4. 加载课程数据时调用 API，如果获取失败显示空状态提示

> 意图：实现课程查看页面，包含表格渲染和卡片列表布局。可考察学生对 Element Plus 表格组件和卡片组件组合使用的能力。

---

## 第 8 轮

### 任务类型
Feature迭代

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/api/grade.js，封装：
- getGrades(params) 发送 GET /grades，params 可选 student_id、course_id、exam_type
- getGradeStats(courseId) 发送 GET /grades/stats/:courseId

在 src/views/Grades.vue 中实现成绩查询页面：
1. 顶部标题"我的成绩"，旁边添加学年学期选择器 el-select
2. 主体以学期为分组，每个学期显示一个 el-card
3. 卡片内用 el-table 展示课程成绩列表，列：课程名称、学分、成绩（及格用绿色文字、不及格用红色文字）、考试类型（期中/期末/补考）
4. 每个学期卡片底部汇总：总学分、平均分
5. 如果没有成绩数据，显示 Element Plus 的空状态组件 el-empty

> 意图：成绩查询页面的数据展示和条件渲染，涉及颜色状态区分和分组呈现。可考察复杂列表渲染、数据分组和条件样式的处理。

---

## 第 9 轮

### 任务类型
Feature迭代

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/views/CampusLife.vue 实现校园生活入口页面。

在校园生活页面中使用 el-tabs 实现多 Tab 切换，默认显示第一个 Tab：
1. 第一个 Tab "食堂菜单"：显示食堂名称下拉选择器、日期选择器（今天/明天/自定义），下方网格展示菜品卡片（先用 mock 模拟数据，每行 4 个卡片），每个菜品卡片显示：菜名、价格、标签（热门/推荐/新品），卡片右上角有点赞图标
2. 另外两个 Tab "每日书籍"和"每日游戏"暂时只显示"功能开发中，敬请期待"的占位提示

在导航栏的校园生活子菜单中，列出三个入口：食堂菜单、每日书籍、每日游戏，点击后页面内自动切换到对应 Tab。

使用 Element Plus 的 el-tag 组件显示菜品的标签，el-icon 显示点赞图标。

> 意图：校园生活模块的 Tab 切换与子路由/锚点联动，mock 数据的网格展示。可考察 Tab 组件、标签、图标组件的组合使用。

---

## 第 10 轮

### 任务类型
Feature迭代

### 业务领域
大前端与服务端类

### 修改范围
跨模块多文件

### 任务难度
一般

### Prompt 内容

创建 src/views/Profile.vue 实现个人中心页面，左右两栏布局：

左侧（宽度 300px）为个人资料卡片 el-card：
1. 顶部圆形头像占位区域（使用 el-avatar，size=large）
2. 姓名、学号、班级、专业等文本展示（先用固定占位数据）
3. 下方"编辑资料"按钮 el-button

右侧上方为账号安全区域 el-card，标题"账号安全"：
1. 列表形式展示"修改密码"、"绑定手机"、"绑定邮箱"三项
2. 每项右侧显示当前绑定状态（已绑定/未绑定），使用 el-tag 显示
3. 点击"修改密码"弹出 el-dialog 对话框，表单包含旧密码、新密码、确认新密码

右侧下方为系统设置区域 el-card，标题"系统设置"：
1. 消息通知开关 el-switch
2. 语言选择 el-select（选项：简体中文、English）

启动开发服务器后，确认所有页面正常加载、导航栏可正常跳转、登录流程完整。

> 意图：个人中心页面的复杂布局，涉及多模态表单、对话框交互、状态开关。可考察页面布局、表单对话框、交互反馈的综合能力。同时验证整体项目是否完整可运行。
