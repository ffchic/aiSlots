# Active Context

## 当前状态

项目处于**规划完成、准备开发**阶段。技术选型已全部确定，文档已更新。

## 最近完成

- 确定并更新了技术选型：
  - 游戏渲染：PixiJS v7（v8 太新、坑多）
  - AI 生图：即梦 Jimeng API（成本合理、插画风格适合老虎机符号）
  - AI 文案：DeepSeek（成本低、中文理解好）
- 确定生图策略：AI 只生成干净底图，特效全部在 PixiJS 层动态叠加
- 完成文档更新，初始化 git 仓库并推送到 GitHub

## 下一步骤

按 Phase 1 开始开发：
1. 搭建 Sanic 项目骨架，连接 MongoDB
2. 实现 DeepSeek 调用，生成主题文案 JSON
3. 搭建基础 PixiJS 卷轴（色块占位，不需要真实图片）
4. WebSocket 旋转流程前后端联调
5. ThemeStudio 页面：输入框 + 结果展示
