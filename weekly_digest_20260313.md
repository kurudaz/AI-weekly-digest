週報已生成，內容如下：

---

# OpenCode 週報 - 2026.03.13

> 本週書籤精選：Qwen3.5 本地推理狂潮、Qwopus 蒸餾模型、ComfyUI 大更新、OpenClaw 龍蝦軍團、NVIDIA Godot 官方 Fork

---

## 🔥 本週焦點

### 1. Qwen3.5 本地推理全面爆發

Qwen3.5 系列本週成為最熱話題，從瀏覽器 WebGPU 到消費級顯示卡，各路跑法齊發：

- **WebGPU 瀏覽器端**：Qwen3.5-4B INT4 在瀏覽器達到 **180 tok/s**，無需伺服器、完全私有，由 Xenova 用 Transformers.js 實現
- **RTX 5090 極速版**：透過 vLLM NVFP4 patch，Qwen3.5 35B A3B 從 150 tps 提升至 **200 tps**，另有 27B 版本達 80 tps，支援 256K context
- **Apple Silicon MLX**：ParoQuant 支援 Qwen3.5，配合 4-bit 量化保留推理品質，在 M2 Max 本地運行 Agent Demo
- **Unsloth 修復**：Claude Code 與本地 LLM 搭配時有 KV cache 失效問題（推理慢 90%），Unsloth 發布修復指南

來源：[@xenovacom](https://x.com/xenovacom)、[@TheAhmadOsman](https://x.com/TheAhmadOsman)、[@aliez_ren](https://x.com/aliez_ren)、[@zhijianliu_](https://x.com/zhijianliu_)、[@gosrum](https://x.com/gosrum)

---

### 2. Qwopus：Claude Opus 4.6 知識蒸餾進 Qwen3.5 27B

本週最具話題性的模型發布——**Qwopus**（`Qwen3.5-27B-Claude-4.6-Opus-Reasoning-Distilled`）：

- 將 Claude Opus 4.6 的推理能力蒸餾進 Qwen3.5 27B dense 模型
- 在單張 **RTX 3090** 上運行，29-35 tok/s，佔用 **16.5 GB VRAM**
- Thinking Mode 原生支援，無 Jinja 崩潰問題
- 多位測試者確認：長文 HTML 生成等複雜任務較 base model 有顯著提升

模型：`Jackrong/Qwen3.5-27B-Claude-4.6-Opus-Reasoning-Distilled`（HuggingFace）
來源：[@sudoingX](https://x.com/sudoingX)、[@KyleHessling1](https://x.com/KyleHessling1)、[@BrianRoemmele](https://x.com/BrianRoemmele)

---

### 3. ComfyUI App Mode + ComfyHub 雙重大更新

ComfyUI 官方本週發布兩項重大功能：

- **App Mode**：將複雜 node graph workflow 封裝成簡潔應用介面，降低使用門檻
- **ComfyHub**：全新社群中心，可透過 URL 即時探索、執行、分享 workflow 與 app

搭配本週的新 ComfyUI 節點：
- `ComfyUI-TextGenerateQwen3Prompt`：用 Qwen3-4B 作為 LLM 生成提示詞（[GitHub](https://github.com/knishika62/ComfyUI-TextGenerateQwen3Prompt)）
- `ComfyUI-TextGenerateGemma3Prompt`：改用 Gemma3-12b-it，不需外部 LLM 即可玩耍

來源：[@ComfyUI](https://x.com/ComfyUI)、[@PhotogenicWeekE](https://x.com/PhotogenicWeekE)

---

### 4. OpenClaw 龍蝦軍團：多 AI Agent 協作浪潮

OpenClaw v2026.3.7 本週大更新，同時引發一波「龍蝦軍團」DIY 教學潮：

- **新版功能**：GPT-5.4 + Gemini 3.1 Flash-Lite 支援、Telegram forum per-topic agent routing（每個話題綁定不同 agent）、Docker multi-stage build 精簡化
- **龍蝦軍團玩法**：申請多個 Discord Bot token，透過 `openclaw agents add` 指令建立 agent 集群，讓多個 AI 協同工作
- **CLI 前端**：韓國開發者做了 Clawra CLI 版本（[GitHub](https://github.com/Hyeong-soo/clawra-in-cli)）

來源：[@Voxyz_ai](https://x.com/Voxyz_ai)、[@karminski3](https://x.com/karminski3)、[@langzihan](https://x.com/langzihan)

---

### 5. NVIDIA 官方 Godot Fork：Path Tracing + DLSS 登場

NVIDIA 在 GitHub 公開官方維護的 Godot Engine fork（`NVIDIA-RTX/godot`）：

- 加入 **Path Tracing** 渲染器與 **DLSS** 實作
- 包含 Streamline SDK header 24 個檔案，共 9,781 行、104 個檔案變更

對遊戲開發者而言，Godot 的光線追蹤與 AI 超解析度功能有了官方支援基礎。

來源：[@GameMakersJP](https://x.com/GameMakersJP)

---

## 🛠️ 工具清單

| 工具 | 安裝指令 | GitHub | 用途 |
|------|---------|--------|------|
| **TeloP** 直播字幕 | `git clone github.com/miyumiyu/TeloPon` | [連結](https://github.com/miyumiyu/TeloPon) | 直播多行字幕疊加，可拖曳 |
| **Mole** Mac 清理 | `brew install tw93/brew/mole` | [連結](https://github.com/tw93/Mole) | Mac 深度清理，測試清出 30GB |
| **Shiden** UE5 小說編輯器 | UE5 Plugin Manager | [連結](https://github.com/HANON-games/Shiden) | UE5 視覺小說製作，含存檔/回憶 |
| **Fish Speech S2** TTS | `pip install fish-speech` | [連結](https://github.com/fishaudio/fish-speech) | SOTA TTS，支援 `[laugh]`/`[whispers]` inline 控制 |
| **web-search-mcp** | 手動設定 MCP | [連結](https://github.com/mrkrsl/web-search-mcp) | LM Studio 本地 LLM 網路搜尋 |
| **unity-ai-cli-bridge** | `git clone` 後設定 | [連結](https://github.com/hatayama/unity-ai-cli-bridge) | Unity 官方 MCP 從 CLI 呼叫 |
| **autoresearch-mlx** | `pip install mlx` + clone | [連結](https://github.com/trevin-creator/autoresearch-mlx) | Karpathy autoresearch Apple Silicon 版 |
| **OpenClaw** | `docker pull openclaw/openclaw` | [連結](https://github.com/openclaw/openclaw) | 多 AI Agent 龍蝦軍團框架 |
| **NERV UI** | `git clone` | [連結](https://github.com/TheGreatGildo/nerv-ui) | Evangelion 風格 Web UI 設計包 |
| **suno-mcp** | 手動設定 MCP | [連結](https://github.com/sandraschi/suno-mcp) | CLI 控制 Suno 音樂生成 |
| **vllm-qwen3.5-nvfp4** | `git clone` + patch | [35B](https://github.com/Li-Lee/vllm-qwen3.5-nvfp4-5090) / [27B](https://github.com/aliez-ren/vllm-qwen3.5-nvfp4-sm120) | RTX 50 系 Qwen3.5 極速推理 |
| **omlx** | `git clone` | [連結](https://github.com/jundot/omlx) | Apple Silicon LLM 推理伺服器 |
| **clawra-in-cli** | `git clone` | [連結](https://github.com/Hyeong-soo/clawra-in-cli) | OpenClaw CLI 版 |

---

## 📊 快速參考卡

### 工具

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|---------|---------|------|
| TeloP | 直播工具 | git clone | 直播字幕疊加可拖曳 | GitHub |
| Mole | Mac 工具 | brew install | Mac 深度清理 | GitHub |
| Shiden | UE5 Plugin | Plugin Manager | 視覺小說製作 | GitHub |
| Fish Speech S2 | TTS | pip install | SOTA 情緒控制語音合成 | GitHub |
| web-search-mcp | MCP Server | 手動設定 | LM Studio 網路搜尋 | GitHub |
| unity-ai-cli-bridge | CLI Bridge | git clone | Unity MCP CLI 呼叫 | GitHub |
| autoresearch-mlx | Agent | pip + clone | Mac 本地研究 Agent | GitHub |
| OpenClaw | 多 Agent 框架 | Docker | Telegram/Discord 龍蝦軍團 | GitHub |
| NERV UI | UI 設計包 | git clone | Evangelion 風格前端 | GitHub |
| suno-mcp | MCP Server | 手動設定 | CLI 控制 Suno 音樂 | GitHub |
| vllm-qwen3.5-nvfp4 | vLLM Patch | git clone | RTX 50 系極速推理 | GitHub |
| omlx | 推理伺服器 | git clone | Apple Silicon LLM 伺服器 | GitHub |
| ComfyUI-TextGenerateQwen3Prompt | ComfyUI 節點 | git clone | 用 Qwen3-4B 生成提示詞 | GitHub |
| clawra-in-cli | CLI | git clone | OpenClaw CLI 版本 | GitHub |

### 模型

| 模型 | 大小 | 平台 | 特色 | 取得 |
|------|------|------|------|------|
| Qwen3.5-4B INT4 WebGPU | 4B | 瀏覽器 | 180 tok/s，零成本 | HF webml-community |
| Qwopus（Claude Opus 4.6 蒸餾） | 27B | RTX 3090 | 29-35 tok/s，16.5GB | HF Jackrong |
| Huihui-Qwen3.5-4B-abliterated | 4B | 各平台 | 無審查版 Qwen3.5 | HF huihui-ai |
| Huihui-Qwen3.5-122B-A10B | 122B MoE | 伺服器 | 無審查 GGUF | HF huihui-ai |
| Anima Preview 2 | — | ComfyUI | 動漫圖像生成 | HF circlestone-labs |
| Z-Image-Fun-Lora-Distill | LoRA | ComfyUI | 風格遷移 2 步驟 | HF alibaba-pai |
| Sarvam 30B / 105B | 30B/105B | 開源 | 印度語言特化，從頭訓練 | sarvam.ai |
| LTX-2.3 NVFP4 | — | RTX 50 系 | 影片生成快速版 | HF Kijai |

---

## 🎯 本週趨勢

### 1. 本地 LLM 進入「平民化」拐點
Qwen3.5 在瀏覽器、M2 Mac、RTX 3090 上全面可跑，加上 Qwopus（Claude Opus 4.6 蒸餾版）的出現，「在家用 Claude 等級模型」已從願景變成現實。與前沿雲端模型的差距縮小至「約 2 年」，且差距持續快速縮小。

### 2. 多 Agent 框架進入普及教學期
OpenClaw「龍蝦軍團」從中文圈到韓文圈大量傳播，從入門教學到 10 agent 集群進階玩法齊發。每個 Telegram 話題獨立綁定 agent 的設計讓多 AI 協作從實驗性玩具走向實用架構，DIY 門檻持續降低。

### 3. ComfyUI 生態加速封裝為產品
App Mode 與 ComfyHub 標誌著 ComfyUI 從「工程師工具」往「創作者產品」轉型。Nano Banana 系列 prompt 模板社群分享熱潮持續，AI 圖像生成工作流正在快速商品化。

### 4. AI 影片生成進入「可控創作」階段
Seedance 2.0、LTX-2.3 First-Last Frame 讓「指定首尾幀自動補間」成為可能，Anima ControlLoRA（Canny）的出現讓構圖控制更精確。業界共識逐漸成形：有審美品味與深度的創作者才能真正駕馭 AI，AI 降低的是技術門檻，而非審美門檻。

---

週報共涵蓋 **5 個焦點話題**、**13+ 工具**、**8 個模型**，已完整分析本週書籤。

如需儲存到 `reports/weekly_digest_20260313.md`，請授權寫入權限即可。
