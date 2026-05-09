# System Patterns

## 架构模式

### 前后端分离
- 后端：Python + Sanic，提供 HTTP API + WebSocket
- 前端：Vue 3 + Vite，游戏渲染用 PixiJS v7

### AI 服务解耦
- 文案生成和生图分别封装在独立的 service 文件中
- API Key 通过 .env 配置，便于切换模型
- `ai_theme.py`：DeepSeek 调用
- `ai_image.py`：即梦 API 调用

### 实时通信
- WebSocket 处理两类消息：
  1. 旋转结果推送（spin_result）
  2. AI 生图进度推送（theme_progress）

### 特效分层原则
- AI 生成**干净底图**，不在图片里加特效
- 所有游戏特效（发光、粒子爆炸、连线高亮）由 PixiJS 在运行时动态叠加
- 好处：特效可动画化、可复用、不依赖重新生图

### 数据存储
- MongoDB 存主题配置、玩家数据、对局记录
- 图片文件存本地（开发期）→ 阿里云 OSS（上线后）

## 关键技术决策

1. **Sanic**：原生 async/await + WebSocket，适合 IO 密集型（大量 AI API 调用）
2. **PixiJS v7**：WebGL 渲染，60fps，行业标准，社区资源最丰富
3. **并发生图**：`asyncio.gather` 并发调用即梦 API，WebSocket 推送进度
4. **MongoDB**：主题 schema 灵活，运营可随时增删改查，不需要改代码上线新主题
