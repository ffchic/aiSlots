# Tech Context

## 技术栈

| 层级 | 技术 | 说明 |
|------|------|------|
| 后端框架 | Python + Sanic | 异步 HTTP + WebSocket |
| 数据库 | MongoDB | 主题配置、玩家数据、生成记录 |
| AI 文案生成 | DeepSeek | 主题名称、符号描述、背景故事 |
| AI 生图 | 即梦（Jimeng） | 批量生成符号底图（干净无特效）|
| 前端框架 | Vue 3 + Vite | 页面结构、路由、状态管理 |
| 游戏渲染 | PixiJS v7 | 卷轴动画、粒子特效、WebGL 渲染 |
| 动画补间 | GSAP | 复杂动画时序控制 |
| 样式 | Tailwind CSS | UI 部分快速布局 |
| 状态管理 | Pinia | Vue 全局状态 |
| 实时通信 | WebSocket | 旋转结果推送、AI 流式输出 |
| 文件存储 | 本地 / 阿里云 OSS | 生成的图片资源（初期本地）|

## 开发环境

- Python 环境：conda 环境 `bislots`，Python 3.11
  - 路径：`/opt/miniconda3/envs/bislots`
  - 激活：`conda activate bislots`
  - 依赖：`backend/requirements.txt`
- 环境变量：项目根目录 `.env` 文件，参考 `.env.example`

## 环境变量

```
DEEPSEEK_API_KEY=sk-...        # 文案生成（DeepSeek）
JIMENG_API_KEY=...             # 生图（即梦）
MONGODB_URI=mongodb://localhost:27017
MONGODB_DB=ai_slots
STORAGE_TYPE=local             # local 或 oss
LOCAL_STORAGE_PATH=./storage/images
OSS_BUCKET=...                 # 上线后配置
OSS_ENDPOINT=...
```

## 注意事项

- PixiJS 使用 **v7**，不要参考 v6/v8 的文档和教程
- `.env` 已加入 `.gitignore`，绝不提交 git
