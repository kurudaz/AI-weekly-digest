# OpenCode 週報 - 2026.04.10

---

## 🔥 本週焦點

### 1. 量化技術大爆發：本地 LLM 的 VRAM 革命

本週最熱話題是多個 KV cache 壓縮方案同時爆發。**RotorQuant** 號稱 10x KV cache 壓縮、28% 加速解碼、5x 加速 prefill；**TurboQuant** 分支持續更新，讓 Harmonic 9B 在 context 從 65K 擴展至 384K 而僅多用 3.5GB VRAM；**PolarQuant** 則攻向影像模型，FLUX.2 klein 9B 壓縮至 Q5 後與原模型相似度達 0.9986。`pip install turboquant-gpu` 讓任何 GPU 三行程式碼即可啟用 3-bit Lloyd-Max 壓縮。
> 來源：[@anirudhbv_ce](https://x.com/anirudhbv_ce)、[@no_stp_on_snek](https://x.com/no_stp_on_snek)、[@caiovicentino1](https://x.com/caiovicentino1)

---

### 2. Qwopus / Gemopus 系列：把 Claude Opus 推理蒸餾進本地模型

**Qwopus** 概念持續發酵——將 Claude Opus 推理能力蒸餾進 Qwen3.5 架構。本週亮點：
- **Qwopus MoE 35B A3B**：72GB 壓縮至 21GB，256 experts 但每 token 只啟動 3B，RTX 4090 可跑
- **Gemopus-4-E4B**（by Jackrong）：Gemma 4 + Opus 4.6 推理，iPhone 跑 45-60 tok/s、M3/M4 MacBook Air 跑 90-120 tok/s
- **Qwopus3.5-27B-v3-FP8**：vLLM 優化版，品質比 GGUF 更接近原模型

> 來源：[@leftcurvedev_](https://x.com/leftcurvedev_)、[@caiovicentino1](https://x.com/caiovicentino1)、[@samuelcardillo](https://x.com/CardilloSamuel)

---

### 3. LTX Video 2.3 生態系爆炸成長

**LTX-2.3-22B** 的 IC-LoRA 社群週內出現多個特化模型：outpaint 擴展畫面（oumoumad）、相機運鏡控制（Cseti Cameraman v1）、anime-to-half-real 轉換（Banodoco 社群）。PolarQuant 更將原本 46.2GB 的模型壓至 15GB，Transformer 從 37GB 壓至 4.6GB，RTX 4090 加 offload 即可運行。

> 來源：[@toyxyz3](https://x.com/toyxyz3)、[@banodoco](https://x.com/banodoco)、[@caiovicentino1](https://x.com/caiovicentino1)

---

### 4. ComfyUI 新節點週：從影片調色到人臉修復全覆蓋

本週 ComfyUI 生態新增多個實用節點：
- **ComfyUI-VideoColorGrading**（Kijai）：自動 LUT 生成進行影片調色，對標 DaVinci Resolve
- **ComfyUI-Flux-FaceIR**：FLUX.2 為基礎的盲目/參考導向人臉修復
- **ComfyUI-HiresFix-Ultra-AllInOne**：高解析度放大一體化節點，整合色彩校正與 OOM 防護
- **Wan VACE Outpaint**：輕量 ComfyUI 視頻外擴節點，無額外依賴

> 來源：[@nodesandnoodles](https://x.com/nodesandnoodles)、[@cosmicrealm](https://x.com/cosmicrealm)、[@Brie_Wensleydale](https://x.com/SlipperyGem)

---

### 5. Claude Code × 本地 LLM：整合嘗試與現實落差

社群積極嘗試用 Gemma 4 本地跑 Claude Code，步驟為：安裝 llama.cpp → 啟動 llama-server → 設定 Claude Code endpoint。但現實是 Ollama v0.20.1 tool parser 仍損壞、Gemma 4 function calling 實測只能對話無法呼叫工具。同期 **OpenCode** 則有社群釋出 `opencode-local-provider` 插件，讓本地 LLM 直接作為 Cloud provider 使用，無需手動編輯設定檔。

> 來源：[@outsource_](https://x.com/outsource_)、[@gonizahavy](https://x.com/gonizahavy)

---

## 🛠️ 工具清單

### RotorQuant
- **安裝**：`git clone https://github.com/scrya-com/rotorquant`
- **GitHub**：[scrya-com/rotorquant](https://github.com/scrya-com/rotorquant)
- **用途**：KV cache 10x 壓縮，28% 加速解碼，適用本地 LLM 推理

### TurboQuant（buun-llama-cpp fork）
- **安裝**：`git clone https://github.com/spiritbuun/buun-llama-cpp`
- **GitHub**：[spiritbuun/buun-llama-cpp](https://github.com/spiritbuun/buun-llama-cpp)
- **用途**：llama.cpp 的 TurboQuant 分支，支援 `--cache-type-k turbo4` 等參數，大幅擴展 context window

### turboquant-gpu（Python 套件）
- **安裝**：`pip install turboquant-gpu`
- **用途**：三行程式碼為任何 GPU 啟用 3-bit Lloyd-Max KV 壓縮，支援 transformers 整合

### sam3.cpp
- **GitHub**：[pierre-antoine-bannier/sam3.cpp](https://github.com/PierreBannier/sam3.cpp)（依原文推測）
- **用途**：Meta SAM 3 的純 C++ 實作，支援 SAM 3.1/3/2.1/EdgeTAM，CUDA/Metal/CPU，EdgeTAM 4-bit 僅 15MB，100ms 物件偵測

### Draw Things CLI
- **安裝**：`brew install --HEAD drawthingsai/draw-things/draw-things-cli`
- **用途**：macOS 上最快的本地圖像生成 CLI，可腳本化，GPL-v3 授權

### Unsloth（Gemma 4 微調）
- **安裝**：`pip install unsloth`
- **GitHub**：[unslothai/unsloth](https://github.com/unslothai/unsloth)
- **用途**：8GB VRAM 即可微調 Gemma 4，速度 1.5x、VRAM 節省 50%

### TriAttention
- **GitHub**：[yukangchen/triattention](https://github.com/yukangchen) （依原文）
- **用途**：KV cache 壓縮，2.5× 加速推理、10.7× 減少 KV cache 記憶體，vLLM 相容，單張 RTX 4090 可跑 OpenClaw 32B

### ComfyUI-VideoColorGrading
- **安裝**：ComfyUI Manager 搜尋，或 `git clone https://github.com/kijai/ComfyUI-VideoColorGrading`
- **GitHub**：[kijai/ComfyUI-VideoColorGrading](https://github.com/kijai/ComfyUI-VideoColorGrading)
- **用途**：自動 LUT 生成，兩段式對齊影片色彩風格，時序一致性

### ComfyUI-HiresFix-Ultra-AllInOne
- **安裝**：`git clone https://github.com/ThetaCursed/ComfyUI-HiresFix-Ultra-AllInOne`
- **GitHub**：[ThetaCursed/ComfyUI-HiresFix-Ultra-AllInOne](https://github.com/ThetaCursed/ComfyUI-HiresFix-Ultra-AllInOne)
- **用途**：高解析度放大一站式節點，含色彩校正、OOM 防護、混合放大

### opencode-local-provider
- **安裝**：`opencode plugin opencode-local-provider`
- **用途**：OpenCode 插件，讓本地 LLM 作為 Cloud provider，無需手動改設定

### see-through-webui
- **GitHub**：[BeamManP/see-through-webui](https://github.com/BeamManP/see-through-webui)
- **用途**：單張圖片自動分層輸出 PSD，RTX 2060+ 可跑，含簡易 WebUI

### FrameFusion
- **用途**：輕量開源影片插幀 AI，參數量 < 10M，ComfyUI 自訂節點，支援實拍與動畫

### Recordly（Screen Studio 替代品）
- **用途**：Mac/Windows/Linux 螢幕錄製，免費開源，功能對標 Screen Studio（原價 1500+ 人民幣）

### Frozen（WebGL shader）
- **GitHub**：[takuma-hmng8/frozen](https://github.com/takuma-hmng8/frozen)
- **用途**：R3F/Three.js 場景包裹器，一行加入冰凍 GLSL shader 效果

### grok-cli
- **GitHub**：[superagent-ai/grok-cli](https://github.com/superagent-ai/grok-cli)
- **用途**：Grok 驅動的開源自主 CLI Agent

### Gemma4 Benchmark（MLX）
- **GitHub**：[Incept5/gemma4-benchmark](https://github.com/Incept5/gemma4-benchmark)
- **用途**：M 系列 Mac 上 Gemma-4 TurboQuant MLX 效能基準測試，E2B 版各量化方案對照

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| RotorQuant | KV 壓縮 | git clone | 10x KV cache 壓縮 | GitHub |
| TurboQuant (buun fork) | llama.cpp 分支 | git clone | 擴展 context window | GitHub |
| turboquant-gpu | Python 套件 | pip install | 任意 GPU KV 壓縮 | PyPI |
| sam3.cpp | C++ 推論 | git clone + cmake | SAM3 本地目標偵測 | GitHub |
| Draw Things CLI | 圖像生成 CLI | brew install | macOS 最快本地圖像生成 | Homebrew |
| Unsloth | 微調框架 | pip install | 低 VRAM 微調 Gemma4/Qwen | GitHub |
| TriAttention | KV 壓縮 | vLLM 整合 | 2.5x 加速、單卡跑 32B | GitHub |
| ComfyUI-VideoColorGrading | ComfyUI 節點 | git clone | 自動 LUT 影片調色 | GitHub |
| ComfyUI-HiresFix-Ultra | ComfyUI 節點 | git clone | 高解析度放大一體化 | GitHub |
| opencode-local-provider | OpenCode 插件 | opencode plugin | 本地 LLM 接入 OpenCode | opencode |
| see-through-webui | WebUI 工具 | git clone | 單圖自動分層輸出 PSD | GitHub |
| FrameFusion | 影片插幀 | ComfyUI 節點 | 輕量高速影片插幀 | GitHub |
| Recordly | 螢幕錄影 | 直接下載 | 免費替代 Screen Studio | 官方 |
| Frozen | WebGL shader | npm/git | R3F 冰凍視覺效果 | GitHub |
| grok-cli | CLI Agent | git clone | Grok 驅動自主 Agent | GitHub |
| Gemma4 Benchmark | 測試工具 | git clone | MLX 量化效能基準 | GitHub |

---

## 🎯 本週趨勢

### 1. 量化技術進入「工程化競賽」階段
TurboQuant、PolarQuant、RotorQuant、SpectralQuant 同週出現，不再只是學術論文而是可直接 `pip install` 或替換 llama.cpp 的實用工具。重點轉向：**哪個量化方案在你的 GPU 上品質損失最小、速度最快**。RTX 4090（24GB）已成為新的本地 LLM 基準硬體。

### 2. 「Claude Opus 蒸餾」成為 Hugging Face 新流派
Qwopus、Gemopus 系列代表一個新趨勢：用 Claude Opus 的推理輸出訓練開源模型。社群在激烈競爭誰能做出最接近 Opus 推理、又能在消費級 GPU 或手機上運行的版本。FP8、GGUF、PolarQuant 多種量化格式同步推出。

### 3. ComfyUI 生態走向「工作流專業化」
本週 ComfyUI 節點不再是通用工具，而是瞄準特定痛點：影片調色自動化（告別 DaVinci）、人臉修復、一鍵高解析度放大。Wan VACE 的 outpaint 節點更直接瞄準影片穩定化後的黑邊填充應用場景。

### 4. Apple Silicon 成為本地 AI 的重要戰場
多個工具本週明確針對 M 系列 Mac 優化：MLX TurboQuant Metal kernels 加速、mlx-vlm 支援 VisionFeatureCache、Gemma4 MLX 基準測試、Gemopus 在 M3/M4 MacBook Air 達 90-120 tok/s。iPhone 端 Flash-iOS（MoE 模型直接跑在手機）也持續推進，Apple 生態正成為本地 AI 部署的核心平台。
