检查根目录下的 vite.config.js，添加以下配置：
1. resolve.alias 将 @ 指向 src 目录
2. server.proxy 将 /api 请求代理到 http://localhost:3000，注意后端 API 实际路径为 /api/v1，前端请求时统一以 /api 为前缀，代理时需要重写路径去掉 /api 前缀
3. 添加 Element Plus 自动导入的配置（使用 unplugin-vue-components 和 unplugin-auto-import）

安装以下依赖（使用 pnpm add）：

运行时依赖：vue-router、pinia、axios、element-plus、@element-plus/icons-vue

开发依赖：unplugin-vue-components、unplugin-auto-import

检查 package.json，确保 type 为 module（当前项目的 tsconfig 已不是问题，前端使用 ESM 是正确的）。

创建 src 下的子目录：src/router、src/views、src/api、src/stores、src/components、src/utils、src/assets