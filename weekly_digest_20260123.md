# X 書籤週報 - 2026/01/17 ~ 01/23

> 本週 AI 開發圈重點：GLM-4.7-Flash 本地部署熱潮、Flux.2 Klein 圖像編輯能力驚艷、Skills 生態系統成形

---

## 本週焦點

### 1. GLM-4.7-Flash：本地 LLM 新標竿

智譜 AI 開源的 30B MoE 模型，在 SWE-Bench 和 GPQA 上表現優異，支援 200K 上下文。重點是**可在消費級 GPU 上運行**：

- RTX 3090 可達 20 tok/sec（144K 上下文）
- RTX 5090 配合 NVFP4 量化可運行完整模型
- KV-cache 修復後，200K 上下文僅需約 10GB VRAM

**@TheAhmadOsman** 分享的單行修復讓 vLLM 的 KV-cache 從 180GB 降到 10GB，這是本地部署的關鍵突破。

### 2. Flux.2 Klein：圖像編輯新玩法

Black Forest Labs 的新模型，支援 9B 和 4B 版本：

- 文字到圖像、圖像編輯雙模式
- Ollama 已原生支援：`ollama run x/flux2-klein`
- fal 釋出多個特化 LoRA：物件移除、背景移除、外擴

**@nobisiro_2023** 發現可用 prompt 精確控制灰色區域抽取，適合漫畫製作流程。

### 3. Skills 生態系統成形

Vercel 推出的「AI 技能的 npm」正式上線：

- 安裝指令：`npx skills i vercel-labs/agent-skills`
- agent-browser 成為熱門技能，可讓 AI 讀取網頁
- **@dotey** 宝玉老師的 baoyu-skills 包含完整的內容創作工具鏈

### 4. HeartMuLa：本地音樂生成

ComfyUI 新支援的音樂生成模型，日語歌詞表現優於 ACE-Step：

- RTX 5090 上 4 分鐘歌曲約需 150 秒生成
- 可與 Flux.2 Klein + Wan2.2 S2V 整合做完整 MV

### 5. Pencil：Claude Code 的設計畫布

**@tomkrcha** 推出的無限設計畫布：

- WebGL 高速渲染，可編輯
- 設計檔存在 git repo（.pen 格式）
- 支援並行設計 agent，設計轉代碼一條龍

---

## 工具清單

### LLM / 本地模型

| 工具 | 安裝 / 連結 | 說明 |
|------|-------------|------|
| GLM-4.7-Flash | `ollama run glm4.7-flash` | 30B MoE，本地最強 |
| GLM-4.7-Flash GGUF | [unsloth/GLM-4.7-Flash-GGUF](https://huggingface.co/unsloth/GLM-4.7-Flash-GGUF) | Unsloth 量化版 |
| GLM-4.7-Flash NVFP4 | [GadflyII/GLM-4.7-Flash-NVFP4](https://huggingface.co/GadflyII/GLM-4.7-Flash-NVFP4) | 20.4GB，RTX 5090 可跑 |
| llama.cpp Anthropic API | [llama.cpp](https://github.com/ggerganov/llama.cpp) | 支援 Claude Code 兼容模式 |

### 圖像生成 / 編輯

| 工具 | 安裝 / 連結 | 說明 |
|------|-------------|------|
| Flux.2 Klein | `ollama run x/flux2-klein` | 9B/4B 圖像生成編輯 |
| Z-Image Turbo | `ollama run x/z-image-turbo` | 阿里通義圖像模型 |
| flux2.c | [antirez/flux2.c](https://github.com/antirez/flux2.c) | 純 C 推理，無依賴 |
| Nunchaku-Qwen-Image | [QuantFunc/Nunchaku-Qwen-Image-2512](https://huggingface.co/QuantFunc/Nunchaku-Qwen-Image-2512) | ComfyUI nunchaku 兼容 |
| fal Klein LoRAs | [fal/flux-2-klein-4B-object-remove-lora](https://huggingface.co/fal/flux-2-klein-4B-object-remove-lora) | 物件/背景移除、外擴 |

### ComfyUI 節點

| 節點 | 連結 | 說明 |
|------|------|------|
| HeartMuLa_ComfyUI | [benjiyaya/HeartMuLa_ComfyUI](https://github.com/benjiyaya/HeartMuLa_ComfyUI) | 音樂生成 |
| ComfyUI-Flux2Klein-Enhancer | [capitan01R/ComfyUI-Flux2Klein-Enhancer](https://github.com/capitan01R/ComfyUI-Flux2Klein-Enhancer) | Klein 提示詞增強、區域控制 |
| ComfyUI LTX-2 VRAM | [RandomInternetPreson/ComfyUI_LTX-2_VRAM_Memory_Management](https://github.com/RandomInternetPreson/ComfyUI_LTX-2_VRAM_Memory_Management) | LTX-2 VRAM 優化，24GB 可跑 900+ 幀 |
| Z-Image Omni | ComfyUI PR #11979 | 官方支援 zimage omni |

### AI Agent / Skills

| 工具 | 安裝 | 說明 |
|------|------|------|
| Skills CLI | `npx skills i <package>` | AI 技能包管理器 |
| agent-browser | `npx skills add vercel-labs/agent-browser` | 瀏覽器自動化 |
| Remotion Skills | `npx skills add remotion-dev/skills` | Claude Code 做影片 |
| baoyu-skills | [JimLiu/baoyu-skills](https://github.com/JimLiu/baoyu-skills) | 內容創作全套（漫畫、發文等） |
| awesome-agent-skills | [libukai/awesome-agent-skills](https://github.com/libukai/awesome-agent-skills) | Skills 中文終極指南 |
| everything-claude-code | [affaan-m/everything-claude-code](https://github.com/affaan-m/everything-claude-code) | Anthropic 黑客松獲獎配置 |
| ask-questions-if-underspecified | [mzbac/skills](https://github.com/mzbac/skills/tree/main/skills/ask-questions-if-underspecified) | Codex 必備 skill |

### 影片 / 音訊

| 工具 | 連結 | 說明 |
|------|------|------|
| whisperVideo | [showlab/whisperVideo](https://github.com/showlab/whisperVideo) | 影片說話人辨識 + 字幕 |
| Video-Caption-Suite | [filliptm/Video-Caption-Suite](https://github.com/filliptm/Video-Caption-Suite) | Qwen3-VL 批次影片字幕 |
| VoxCPM | OpenBMB 開源 | 即時語音克隆，RTX 4090 約 0.15x 實時 |
| LTX2-Rapid-Merges | [Phr00t/LTX2-Rapid-Merges](https://huggingface.co/Phr00t/LTX2-Rapid-Merges) | LTX2 快速合併 |

### 開發者工具

| 工具 | 連結 | 說明 |
|------|------|------|
| Vibecraft | [Nearcyan/vibecraft](https://github.com/Nearcyan/vibecraft) | Claude Code 視覺化管理 + 空間音效 |
| Pencil | @tomkrcha | Claude Code 無限設計畫布 |
| OpenCode | [opencode](https://github.com/opencode-ai/opencode) | 開源 AI 編輯器，支援本地模型 |
| codex-autorunner | [Git-on-my-level/codex-autorunner](https://github.com/Git-on-my-level/codex-autorunner) | Codex 自動執行器 |
| Clawdbot | [docs.clawd.bot](https://docs.clawd.bot) | Hetzner 部署指南 |
| colmapview | [colmapview.github.io](https://colmapview.github.io) | COLMAP 可視化 + 對齊工具 |
| concept-viz-agent | [lbq110/concept-viz-agent](https://github.com/lbq110/concept-viz-agent) | 技術文章轉科學風格概念圖 |
| AI Toolkit | [ostrisai](https://github.com/ostrisai/ai-toolkit) | Flux.2 Klein LoRA 訓練 |

### 實用服務

| 服務 | 連結 | 說明 |
|------|------|------|
| World Labs API | [theworldlabs](https://worldlabs.ai) | 文字/圖片生成可探索 3D 世界 |
| Visual Sitemaps | 商業服務 | 競品分析自動截圖 + 站點地圖 |
| Qwen3-VL-Embedding 搜尋 | [zenn.dev/gosrum](https://zenn.dev/gosrum) | 本地多模態圖片搜尋 |
| GSplatProcessor | [PlayCanvas](https://engine-fkbv1dqga-playcanvas.vercel.app/#/gaussian-splatting/paint) | Gaussian Splat GPU 著色器 |

---

## 快速參考卡

### 本地 LLM 快速啟動

```bash
# GLM-4.7-Flash (Ollama)
ollama run glm4.7-flash

# GLM-4.7-Flash (llama.cpp，支援 Claude Code)
llama-server -m GLM-4.7-Flash-IQ4_XS.gguf --alias "GLM-4.7-Flash" -ngl 999 -ncmoe 999

# GLM-4.7-Flash (vLLM + Docker)
docker run -p 8001:8001 --gpus all -v models:/models local/llama.cpp:server-cuda \
  -m /models/unsloth/GLM-4.7-Flash-IQ4_XS.gguf --ctx-size 32000 --n-gpu-layers 999
```

### 圖像生成快速啟動

```bash
# Flux.2 Klein (Ollama，macOS 先行)
ollama run x/flux2-klein

# Z-Image Turbo
ollama run x/z-image-turbo
```

### Skills 快速安裝

```bash
# 基礎安裝
npx skills i vercel-labs/agent-skills

# agent-browser
npx skills add vercel-labs/agent-browser --skill "agent-browser"

# Remotion 影片製作
npx skills add remotion-dev/skills

# 宝玉老師全套
git clone https://github.com/JimLiu/baoyu-skills
```

---

## 本週數據

- 書籤數：約 70+ 則
- 涵蓋日期：2026/01/17 - 01/23
- 主要語言：日文 40%、中文 35%、英文 25%
- 熱門關鍵字：GLM-4.7、Flux.2 Klein、Skills、ComfyUI、本地部署

---

*Generated by Claude Code | 2026-01-23*
