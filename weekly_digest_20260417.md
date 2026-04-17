# OpenCode 週報 - 2026.04.17

---

## 🔥 本週焦點

### 1. ERNIE Image — Baidu 開源最強圖像生成模型
Baidu 發布 ERNIE Image（8B DiT 參數），在 GenEval、OneIG、LongTextBench 三大 benchmark 均奪得開源第一。支援英文、中文精確文字渲染，複雜指令跟隨能力強，並提供 Turbo 版本。Ostris AI Toolkit 已第一時間支援 LoRA 訓練。量化版本（fp8、nvfp4、int8）已由社群釋出至 Hugging Face。
- 來源：[@ErnieforDevs](https://huggingface.co/GuangyuanSD/ErnieTurbo_extracted_lora)、[@ostrisai](https://github.com/ostris/ai-toolkit)

### 2. Tencent HY-World 2.0 — 從單張圖／影片生成可互動 3D 世界
Tencent 開源 HY-World 2.0，支援從文字、圖片、影片一鍵生成 3D 世界，並可直接匯入 Unity / Unreal Engine。社群已建立 Web UI Playground 可本地試玩。
- GitHub：[filliptm/HY-World-2.0-Playground](https://github.com/filliptm/HY-World-2.0-Playground)
- HuggingFace：[tencent/HY-World-2.0](https://huggingface.co/tencent/HY-World-2.0)

### 3. AniGen — 單張圖像直出帶 Rig 的 3D 角色（SIGGRAPH 2025）
VAST AI Research 的 SIGGRAPH 研究成果，輸入一張圖像，同時輸出 3D 形狀 + 骨架 + Skinning Weights，支援人物、動物、機械，大幅縮短 Rigging 工時，已完整開源並提供 HuggingFace Demo。
- GitHub：[VAST-AI-Research/AniGen](https://github.com/VAST-AI-Research/AniGen)
- HF Demo：[huggingface.co/spaces/VAST-AI/AniGen](https://huggingface.co/spaces/VAST-AI/AniGen)

### 4. DFlash — Apple Silicon MLX 推測解碼，最高 4x 加速
DFlash 為 Apple M 系列晶片帶來 Speculative Decoding，基於 stock MLX 無需 fork，Qwen3.5-9B 從 30 tok/s 提升至 127 tok/s（4.13x）。完全開源。
- GitHub：[bstnxbt/dflash-mlx](https://github.com/bstnxbt/dflash-mlx)

### 5. Qwen3.6-35B-A3B — Alibaba 開源稀疏 MoE 模型
35B 總參數、3B 激活參數，Apache 2.0 授權。Agentic coding 能力媲美 10 倍大小的模型，支援多模態感知、推理，具備 Thinking / Non-thinking 雙模式。
- 來源：[@Alibaba_Qwen](https://twitter.com/Alibaba_Qwen)

---

## 🛠️ 工具清單

### DFlash MLX
- **安裝**：`git clone https://github.com/bstnxbt/dflash-mlx`
- **GitHub**：[bstnxbt/dflash-mlx](https://github.com/bstnxbt/dflash-mlx)
- **用途**：Apple Silicon 本地 LLM 推測解碼加速，Qwen3.5 系列最高 4x 加速

### AniGen
- **安裝**：`git clone https://github.com/VAST-AI-Research/AniGen`
- **GitHub**：[VAST-AI-Research/AniGen](https://github.com/VAST-AI-Research/AniGen)
- **用途**：單張圖像生成帶骨架與 Skinning 的完整 3D 角色

### HY-World 2.0 Playground
- **安裝**：`git clone https://github.com/filliptm/HY-World-2.0-Playground`
- **GitHub**：[filliptm/HY-World-2.0-Playground](https://github.com/filliptm/HY-World-2.0-Playground)
- **用途**：本地運行 Tencent 3D 世界生成模型的 Web UI

### ComfyUI-Woosh（Sony 音效生成）
- **安裝**：透過 ComfyUI Manager 搜尋 `ComfyUI-Woosh`
- **GitHub**：[Saganaki22/ComfyUI-Woosh](https://github.com/Saganaki22/ComfyUI-Woosh)
- **用途**：ComfyUI 節點，基於 Sony Woosh 模型的 T2A/V2A 音效生成，4 步蒸餾推理

### ComfyUI_toyxyz_test_nodes（含 Anima Region 支援）
- **安裝**：透過 ComfyUI Manager 搜尋 `toyxyz_test_nodes`
- **GitHub**：[toyxyz/ComfyUI_toyxyz_test_nodes](https://github.com/toyxyz/ComfyUI_toyxyz_test_nodes)
- **用途**：ComfyUI 節點集，新增 Anima 支援至 ComfyCouple Region 節點

### Spark 2.0（3D Gaussian Splatting 串流）
- **安裝**：參見官方文件（基於 Three.js）
- **GitHub**：[sparkjsdev](https://github.com/sparkjsdev)
- **用途**：可串流 100M+ splat 的 3DGS 渲染庫，支援 WebGL2，適用手機至 VR

### omlx（macOS 本地 LLM 服務）
- **安裝**：`git clone https://github.com/jundot/omlx`
- **GitHub**：[jundot/omlx](https://github.com/jundot/omlx)
- **用途**：Mac 自架 LLM 推理服務，比 vllm-studio 更輕量易用

### ai4animationpy（Meta 開源 Python 角色動畫框架）
- **安裝**：`pip install ai4animationpy`（或 clone repo）
- **GitHub**：[facebookresearch/ai4animationpy](https://github.com/facebookresearch/ai4animationpy)
- **用途**：純 Python（PyTorch + NumPy）驅動完整 3D 角色動畫，無需 Unity

### logo-generator-skill（Claude Code Skill）
- **安裝**：`npx skills add` → 選擇 logo-generator-skill
- **GitHub**：[op7418/logo-generator-skill](https://github.com/op7418/logo-generator-skill)
- **用途**：Claude Code Skill，輸入產品介紹自動生成 SVG Logo 並產出展示網頁

### sparkjs-skill（Claude Code Skill）
- **安裝**：`npx skills add`（參見 repo）
- **GitHub**：[shi3z/sparkjs-skill](https://github.com/shi3z/sparkjs-skill)
- **用途**：讓 Claude 即知 SparkJS 完整 API，包含 3DGS、SDF editing、LoD 等 117+ 函式

### svg-hand-drawn-skill（Claude Code Skill）
- **安裝**：`npx skills add`
- **GitHub**：[shaom/svg-hand-drawn-skill](https://github.com/shaom/svg-hand-drawn-skill)
- **用途**：將 SVG 轉為手繪風格逐路徑動畫，輸出可直接嵌入網頁的播放器

### border-beam（UI 元件庫）
- **安裝**：`npm install border-beam`
- **GitHub**：查看 npmjs
- **用途**：前端邊框光束動畫元件，可直接整合至現有專案

### delaunay-depth-mesh（瀏覽器即時 Delaunay 三角化）
- **安裝**：`git clone https://github.com/rettuce/delaunay-depth-mesh`
- **GitHub**：[rettuce/delaunay-depth-mesh](https://github.com/rettuce/delaunay-depth-mesh)
- **用途**：純瀏覽器 + Webcam 即時人物 Delaunay 三角分割低多邊形化

### OnnxSharp（Windows 3DGS 生成工具）
- **安裝**：下載 exe，參見 [suto.bex.jp](https://suto.bex.jp/win/onnxsharp/indexj.html)
- **用途**：單張 2D 圖像生成高品質 3DGS 模型，附 3DGS 通用 Viewer

### Shaders MCP
- **安裝**：參見 [shaders.com/docs/guide/mcp](https://shaders.com/docs/guide/mcp)（Pro 用戶）
- **用途**：MCP 工具，讓 AI Agent 自動查找、調整、生成 WebGL shader preset

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| ERNIE Image | 圖像生成模型 | HuggingFace 下載 | 開源最強 T2I，精確文字渲染 | Baidu / HuggingFace |
| DFlash MLX | LLM 加速工具 | git clone | Apple Silicon 4x 推理加速 | GitHub |
| AniGen | 3D 角色生成 | git clone | 單圖生成帶 Rig 3D 角色 | GitHub / HF |
| HY-World 2.0 | 3D 世界生成 | git clone | 文字/圖片生成可互動 3D 世界 | GitHub |
| ComfyUI-Woosh | ComfyUI 節點 | ComfyUI Manager | T2A/V2A 音效生成 | GitHub |
| Spark 2.0 | 3DGS 渲染庫 | npm | 串流百萬級 splat，WebGL2 | GitHub |
| omlx | LLM 本地服務 | git clone | Mac 自架輕量 LLM 推理 | GitHub |
| ai4animationpy | 動畫框架 | pip install | 純 Python 3D 角色動畫 | Meta / GitHub |
| logo-generator-skill | Claude Code Skill | npx skills add | AI 生成 SVG Logo + 展示頁 | GitHub |
| sparkjs-skill | Claude Code Skill | npx skills add | Claude 直通 SparkJS 完整 API | GitHub |
| svg-hand-drawn-skill | Claude Code Skill | npx skills add | SVG 手繪風格動畫生成 | GitHub |
| border-beam | npm 元件 | npm install | 前端邊框光束動畫 | npm |
| delaunay-depth-mesh | 瀏覽器工具 | git clone | Webcam 即時低多邊形化 | GitHub |
| OnnxSharp | Windows 桌面工具 | exe 下載 | 單圖生成 3DGS 模型 | 個人網站 |
| ComfyUI_toyxyz_test_nodes | ComfyUI 節點 | ComfyUI Manager | Anima Region 節點 | GitHub |
| Shaders MCP | MCP 工具 | shaders.com | AI Agent 自動生成 shader | shaders.com |
| Qwen3.6-35B-A3B | LLM（MoE） | HuggingFace 下載 | 3B 激活參數媲美 30B 模型 | Alibaba |
| Gemini 3.1 Flash TTS | TTS 模型 | Google API | 多說話者、70+ 語言 TTS | Google |

---

## 🎯 本週趨勢

### 1. 3D 世界生成全面爆發
NVIDIA Lyra 2.0、Tencent HY-World 2.0、AniGen 同週登場，從單張圖片生成可遊走的 3D 場景、帶骨架的角色已成現實，且全部開源可本地運行。遊戲開發與具身智能（Embodied AI）的製作流程正在被根本性重塑。

### 2. Apple Silicon 本地推理進入 4x 加速時代
DFlash 帶來 Speculative Decoding、llama.cpp Flash-MoE 支援 MiniMax M2.7，搭配 NVFP4/IQ2_XXS 量化，M1 Max 64GB 機器已可流暢跑 73GB 級別的大模型。Mac 作為本地 AI 工作站的可行性大幅提升。

### 3. Claude Code Skill 生態快速成型
本週出現 logo-generator、sparkjs、svg-hand-drawn 等多個 Claude Code Skill，`npx skills add` 安裝模式形成社群規範。Claude Code 正從單一 CLI 工具演變為可擴充的 AI 開發平台。

### 4. ComfyUI 持續成為開源 AI 工具的首選整合目標
SAM 3.1 原生支援、Sony Woosh 音效節點、LTX 2.3 LoRA、VNCCS Utils 更新，ComfyUI 生態在影像、影片、音效三個維度同步擴張，成為開源 AI 工具的事實標準整合平台。

---

*週報整理自 X/Twitter 公開書籤內容，技術名詞保留原文。*
