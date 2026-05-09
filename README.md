# AI Slots

AI 驱动的老虎机主题生成器游戏平台。输入一句话描述，AI 自动生成完整老虎机主题（文案 + 符号图片），前端提供完整的老虎机游戏体验。

## 技术栈

- **后端**：Python + Sanic + MongoDB
- **AI 文案**：DeepSeek
- **AI 生图**：即梦（Jimeng）
- **前端**：Vue 3 + Vite + PixiJS v7 + GSAP

## 快速开始

```bash
# 激活 Python 环境
conda activate bislots

# 安装后端依赖
pip install -r backend/requirements.txt

# 配置环境变量
cp .env.example .env
# 编辑 .env 填入 API Key

# 启动后端
python backend/main.py

# 安装前端依赖并启动
cd frontend
npm install
npm run dev
```

## 项目结构

```
ai-slots/
├── backend/        # Sanic 后端
├── frontend/       # Vue 3 前端
├── cline_docs/     # 项目记忆库文档
└── .env.example    # 环境变量模板
```
