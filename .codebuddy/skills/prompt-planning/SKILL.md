---
name: prompt-planning
description: 用于辅助大模型训练的出题规划。当用户输入 "/prompt-planning" 指令时触发。负责阅读项目中的 prompt 需求文档、项目概览，结合用户提供的反馈和提示信息，生成后续的 prompt 规划并写入指定文件。注意生成的 prompt 不能使用 markdown 语法（如加粗、下划线等），以去除 AI 味。
---

# Prompt Planning Skill

## 概述

当用户输入 `/prompt-planning` 指令时触发本 skill。负责为"学生"（受训练的大模型）规划下一轮或后续多轮的 prompt。

## 工作流程

### Step 1: 收集输入信息

1. 读取 `.prompt/prompt_require.md`，获取并确认本轮出题的要求和限定范围。
2. 读取 `.prompt/project_overview.md`，获取并确认当前项目的背景情况。
3. 接收用户在输入 `/prompt-planning` 指令后附加的反馈信息、提示信息，以及指定要生成的 prompt 数量（轮数）。

### Step 2: 规划 Prompt

结合以上所有输入信息，为"学生"设计具体的 prompt。注意：
- **不使用任何 markdown 语法**（不要加粗、下划线、标题标记、列表标记、代码块标记等）。
- 使用纯文本格式输出，保持自然、去 AI 味。
- 如果用户指定了轮数，则生成对应数量的 prompt。
- 如果用户未指定轮数，默认生成 1 轮 prompt。

### Step 3: 写入文件

将规划好的 prompt 更新写入 `.prompt/prompt_planning.md` 文件中。如果文件已存在则覆盖更新。

### Step 4: 输出确认

向用户确认已生成完毕，简要说明生成了多少轮 prompt 以及每个 prompt 的核心方向。
