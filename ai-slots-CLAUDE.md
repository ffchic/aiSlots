# AI Slots — 项目文档

## 项目概述

一个 AI 驱动的老虎机主题生成器游戏平台。核心功能是让运营/用户输入一句话描述（如"生成一个古埃及风格主题"），AI 自动生成完整的老虎机主题，包括符号设计文案、图片生成、以及可直接使用的游戏配置。前端同时提供完整的老虎机游戏体验。

## 技术栈

| 层级 | 技术 | 说明 |
|------|------|------|
| 后端框架 | Python + Sanic | 异步 HTTP + WebSocket |
| 数据库 | MongoDB | 主题配置、玩家数据、生成记录 |
| AI 文案生成 | DeepSeek | 主题名称、符号描述、背景故事 |
| AI 生图 | 即梦（Jimeng） | 批量生成符号图片 |
| 前端框架 | Vue 3 + Vite | 页面结构、路由、状态管理 |
| 游戏渲染 | PixiJS v7 | 卷轴动画、粒子特效、WebGL 渲染 |
| 动画补间 | GSAP | 复杂动画时序控制 |
| 样式 | Tailwind CSS | UI 部分快速布局 |
| 状态管理 | Pinia | Vue 全局状态 |
| 实时通信 | WebSocket | 旋转结果推送、AI 流式输出 |
| 文件存储 | 本地 / 阿里云 OSS | 生成的图片资源（初期本地） |

> AI 模型具体版本后续确定，接口统一封装，方便切换。

## 项目目录结构

```
ai-slots/
├── backend/
│   ├── main.py                  # Sanic 入口，注册路由和中间件
│   ├── config.py                # 配置（MongoDB URI、DeepSeek key、即梦 key、OSS 等）
│   ├── routers/
│   │   ├── theme.py             # 主题生成相关 HTTP 接口
│   │   ├── game.py              # 游戏逻辑接口（旋转、结算）
│   │   └── ws.py                # WebSocket 处理
│   ├── services/
│   │   ├── ai_theme.py          # DeepSeek 调用：生成主题文案 + 符号描述
│   │   ├── ai_image.py          # 即梦 API 调用：批量生成符号图片
│   │   ├── slots_engine.py      # 老虎机核心逻辑（卷轴、赔率、中奖判断）
│   │   └── storage.py           # 图片存储（本地或 OSS）
│   ├── models/
│   │   ├── theme.py             # MongoDB：主题数据模型
│   │   ├── player.py            # MongoDB：玩家数据模型
│   │   └── game_record.py       # MongoDB：对局记录
│   └── requirements.txt
│
└── frontend/
    ├── src/
    │   ├── main.js
    │   ├── router/index.js
    │   ├── stores/
    │   │   ├── game.js          # 游戏状态（余额、当前主题、旋转结果）
    │   │   └── theme.js         # 主题生成状态
    │   ├── views/
    │   │   ├── ThemeStudio.vue  # 主题生成工作台（输入 → 生成 → 预览）
    │   │   ├── GamePlay.vue     # 游戏主界面（含 PixiJS canvas）
    │   │   └── ThemeList.vue    # 已生成主题库
    │   ├── game/
    │   │   ├── SlotMachine.js   # PixiJS 卷轴渲染核心
    │   │   ├── effects/
    │   │   │   ├── Particles.js # 金币爆炸、粒子特效
    │   │   │   └── WinLine.js   # 获奖连线高亮
    │   │   └── animations/
    │   │       └── Reel.js      # GSAP 卷轴滚动动画
    │   └── components/
    │       ├── SymbolCard.vue   # 单个符号展示组件
    │       └── ChatInput.vue    # AI 主题描述输入框
    ├── public/
    │   └── assets/              # 静态资源（框架图、音效）
    ├── index.html
    └── vite.config.js
```

## 核心功能

### 1. AI 主题生成（核心特色）

**流程：**
```
用户输入一句话描述
        ↓
   DeepSeek API
        ↓
生成：主题名 / 背景故事 / 符号列表（high/med/low 分级）/ 美术风格词
        ↓
   即梦 API（并发生成）
        ↓
11 张符号图片（scatter / wild / high×1 / med×4 / low×4）
        ↓
保存到存储 + MongoDB 写入主题配置
        ↓
前端预览 + 一键上架到游戏
```

**符号分级结构（与行业标准对齐）：**
- `scatter`：触发免费旋转
- `wild`：万能替代符号
- `high_1`：最高价值普通符号
- `med_1` ~ `med_4`：中等价值
- `low_1` ~ `low_4`：低价值

**赔率参考（可配置）：**
- high_1：x5=5倍, x4=1倍, x3=0.2倍
- med_1：x5=4倍, x4=0.6倍, x3=0.1倍
- low 系列：x5=1倍, x4=0.2倍, x3=0.1倍

### 2. 老虎机游戏

- 5 列 3 行，40 条赔线（标准布局）
- WebSocket 实时推送旋转结果
- PixiJS 渲染卷轴动画（60fps）
- 获奖后粒子特效 + 连线高亮
- 支持多主题切换

### 3. 主题管理

- 主题列表展示（ThemeList）
- 主题预览（所有符号图片 + 基础信息）
- 上架 / 下架 / 删除
- 生成历史记录

## MongoDB 数据结构

### themes 集合
```json
{
  "_id": "ObjectId",
  "theme_id": "theme_001",
  "name": "法老的宝藏",
  "story": "主题背景故事...",
  "art_style": "Egyptian mythology, flat illustration...",
  "color_palette": ["#C8A850", "#1A3A5C"],
  "symbols": {
    "scatter": { "name": "神秘石碑", "description": "...", "image_url": "..." },
    "wild":    { "name": "荷鲁斯之眼", "description": "...", "image_url": "..." },
    "high_1":  { "name": "黄金法老面具", "description": "...", "image_url": "..." }
  },
  "paytable": { "high_1_x5": 5, "med_1_x5": 4 },
  "status": "active",
  "created_at": "ISODate"
}
```

### players 集合
```json
{
  "_id": "ObjectId",
  "player_id": "string",
  "balance": 1000.0,
  "current_theme": "theme_001",
  "created_at": "ISODate"
}
```

### game_records 集合
```json
{
  "_id": "ObjectId",
  "player_id": "string",
  "theme_id": "string",
  "bet": 10,
  "result_grid": [["high_1","med_1",...], ...],
  "win_amount": 50,
  "win_lines": [0, 3],
  "created_at": "ISODate"
}
```

## WebSocket 消息协议

**客户端 → 服务端：**
```json
{ "action": "spin", "bet": 10, "theme_id": "theme_001" }
```

**服务端 → 客户端：**
```json
{
  "type": "spin_result",
  "grid": [["high_1","med_2","low_1"],["wild","med_1","low_3"],["high_1","med_2","low_2"],["med_1","low_4","med_3"],["high_1","med_1","low_1"]],
  "win_lines": [0, 5],
  "win_amount": 50,
  "balance": 1040
}
```

**AI 生成进度推送：**
```json
{ "type": "theme_progress", "step": "generating_images", "progress": 3, "total": 11 }
```

## 开发阶段规划

### Phase 1 — 跑通核心流程（约 2~3 周）
- [ ] Sanic 项目初始化，MongoDB 连接
- [ ] DeepSeek 调用：输入描述 → 生成主题文案 JSON
- [ ] 基础老虎机卷轴（PixiJS，无主题图，用色块占位）
- [ ] WebSocket 旋转流程跑通（前后端联调）
- [ ] ThemeStudio 页面：输入框 + 结果展示

### Phase 2 — 生图 + 完整游戏（约 2~3 周）
- [ ] 即梦 API 批量生图，图片保存到本地
- [ ] 主题图片加载到 PixiJS 卷轴
- [ ] 获奖判断逻辑完整（赔线计算）
- [ ] 获奖粒子特效 + 连线高亮
- [ ] ThemeList 页面：主题库管理

### Phase 3 — 上线准备
- [ ] 用户系统（注册/登录/JWT）
- [ ] 图片迁移到 OSS
- [ ] Docker 打包部署
- [ ] 性能优化（生图并发、WebSocket 连接管理）

## 关键设计决策

1. **Sanic 选型原因**：原生支持 async/await 和 WebSocket，适合 IO 密集型（大量 OpenAI API 调用）
2. **PixiJS 选型原因**：WebGL 渲染，60fps 卷轴动画，行业标准方案，有完整粒子系统
3. **AI 模型解耦**：文案生成（DeepSeek）和生图（即梦）分别封装在 `services/` 层，API Key 通过配置传入，便于后续切换
4. **生图策略**：使用即梦 API，11 张符号图片并发生成（`asyncio.gather`），通过 WebSocket 推送进度；底图保持干净，特效在 PixiJS 层动态叠加
5. **主题配置存 MongoDB**：方便运营增删改查，不需要改代码上线新主题

## 环境变量（.env）

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
