我将按照以下步骤完成任务，首先查看项目结构和相关配置文件。
        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          

        
            
toolName: view_folder
            
status: success
          
g:\Remote\prompt program\20260511\student-pc
          

        
            
toolName: file_search
            
status: success
          
file_pattern: vite.config.*
          

        
            
toolName: view_files
            
status: success
          
            
filePath: g:\Remote\prompt program\20260511\student-pc\package.json
          

        
            
toolName: view_files
            
status: success
          
            
filePath: g:\Remote\prompt program\20260511\student-pc\vite.config.js
          

        
            
toolName: view_files
            
status: success
          
            
filePath: g:\Remote\prompt program\20260511\student-pc\vite.config.ts
          
现在我已经了解了项目结构。我看到有两个 Vite 配置文件，而且 package.json 已经是 ESM 模式，也已经安装了 pinia 和 vue-router。让我先安装缺失的依赖。

        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          

        
            
toolName: run_command
            
status: success
          
command: pnpm add axios element-plus @element-plus/icons-vue undefined
          

        
            
toolName: run_command
            
status: success
          
command: pnpm add -D unplugin-vue-components unplugin-auto-import undefined
          
依赖安装成功。现在我需要检查一下应该修改哪个配置文件。我需要确认 TypeScript 配置来决定主要使用哪个配置文件。


        
            
toolName: file_search
            
status: success
          
file_pattern: tsconfig*.json
          

        
            
toolName: file_search
            
status: success
          
file_pattern: jsconfig*.json
          

        
            
toolName: AskUserQuestion
            
status: success
          
          
用户选择了修改 `vite.config.ts` 并删除 `vite.config.js`。


        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          
现在我来修改 `vite.config.ts`，添加所有需要的配置：




        
            
toolName: Write
            
status: success
          
            
filePath: g:\Remote\prompt program\20260511\student-pc\vite.config.ts
          
changes: undefined
          

        
            
toolName: delete_file
            
status: success
          
deleteFilesSingular
filePath: g:\Remote\prompt program\20260511\student-pc\vite.config.js
          
配置文件已更新。现在让我检查 package.json 的 type 字段（已确认是 module），然后创建所需的目录结构。




        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          

        
            
toolName: view_files
            
status: success
          
            
filePath: g:\Remote\prompt program\20260511\student-pc\package.json
          
package.json 确认 `"type": "module"` 已正确设置。现在创建 src 下的子目录：




        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          

        
            
toolName: run_command
            
status: success
          
command: New-Item -ItemType Directory -Path 'src/router','src/views','src/api','src/stores','src/components','src/utils','src/assets' -Force undefined
          
目录创建成功。现在验证配置：





        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          

        
            
toolName: view_folder
            
status: success
          
g:\Remote\prompt program\20260511\student-pc\src
          

        
            
toolName: GetDiagnostics
            
status: success
          
          

        
            
toolName: file_search
            
status: success
          
file_pattern: src/*
          
让我用 PowerShell 确认一下目录是否真的创建了：



        
            
toolName: run_command
            
status: success
          
command: Get-ChildItem -Path src -Directory undefined
          
所有任务已完成。让我标记最后一个任务并总结：




        
            
toolName: todo_write
            
status: success
          
Todos updated: 6 items
          
已完成所有任务！以下是完成的内容：

## 1. 依赖安装

**运行时依赖（新增）：**
- `axios`
- `element-plus`
- `@element-plus/icons-vue`

**开发依赖（新增）：**
- `unplugin-vue-components`
- `unplugin-auto-import`

> 注：`pinia` 和 `vue-router` 项目中已预先安装。

## 2. `vite.config.ts` 配置

修改了 [vite.config.ts](file:///g:/Remote/prompt%20program/20260511/student-pc/vite.config.ts)，包含以下配置：

- **resolve.alias**: `@` 指向 `src` 目录
- **server.proxy**: 将 `/api` 请求代理到 `http://localhost:3000`，并重写路径去掉 `/api` 前缀
- **Element Plus 自动导入**: 使用 `unplugin-vue-components` 和 `unplugin-auto-import` 配合 `ElementPlusResolver`

同时删除了多余的 `vite.config.js`。

## 3. package.json

已确认 `"type": "module"` 正确设置。

## 4. 目录结构

已创建 `src` 下的 7 个子目录：
- `src/router/`
- `src/views/`
- `src/api/`
- `src/stores/`
- `src/components/`
- `src/utils/`
- `src/assets/`

配置已通过 TypeScript 诊断检查，无错误。