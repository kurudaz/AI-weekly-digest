# OpenCode 週報 - 2026.05.22

---

## 🔥 本週焦點

### 1. llama.cpp × MTP 本地推論速度大躍進
Multi Token Prediction（MTP）正式進入 llama.cpp，搭配 Qwen3.6 系列模型，在 A10G 上速度從 25 tok/s 飆升至 45 tok/s（+78%）。LM Studio 0.4.14+3 同步支援，Unsloth 也自動選最佳 MTP 設定。只需兩個 flag：
```
--spec-type draft-mtp --spec-draft-n-max 2
```
來源：[@ggerganov](https://twitter.com/ggerganov)、[@victormustar](https://twitter.com/victormustar)、[@UnslothAI](https://twitter.com/UnslothAI)

---

### 2. Stable Audio 3.0 開源，開放音頻生成新時代
Stability AI 發布 Stable Audio 3.0 開放權重模型，主打藝術實驗用途，輸出版權歸用戶所有可商用發布。這是繼圖像、影片之後，音頻生成領域的重要開源里程碑。
來源：[@StabilityAI](https://twitter.com/StabilityAI)

---

### 3. Pixal3D 改為 MIT 授權，單圖轉高保真 3D 資產
TencentARC 的 SIGGRAPH'26 論文成果 Pixal3D（Pixel-aligned image-to-3D）宣布採用 MIT License，已有 ComfyUI 節點可直接使用。支援 textured GLB 匯出、FlashAttention 2/3、手動相機控制。
來源：[@WangZhao_0849](https://twitter.com/WangZhao_0849)、[@SD_Tutorial](https://twitter.com/SD_Tutorial)

---

### 4. Cohere Command A+ 開源：218B 參數、僅需極少硬體
Command A+ 為 MoE 架構（218B 總參數、25B active），採用 ASL 2.0 授權。注意力層保持全精度，MoE expert 層選擇性量化為 NVFP4 W4A4（4-bit），並以量化感知蒸餾（QAD）補償劣化。128K context，待 llama.cpp 支援後可本地運行。
來源：[@cohere](https://twitter.com/cohere)、[@kohya_tech](https://twitter.com/kohya_tech)

---

### 5. SANA：16GB 筆電生成 4096×4096 圖像
僅 0.6B 參數，使用線性注意力 + 32× latent 壓縮，1024px 生成速度達亞秒級；4-bit 量化版本 8GB 以下可跑。完整訓練 pipeline 全開源。
來源：[@GithubProjects](https://twitter.com/GithubProjects)

---

## 🛠️ 工具清單

### 本地推論 / LLM

**LM Studio 0.4.14+3（MTP 支援）**
- 安裝：在 app 內更新，或執行 `lms runtime update llama.cpp --channel beta`
- 用途：本地 LLM GUI，新增 Multi Token Prediction 加速推論
- 官網：https://lmstudio.ai

**Unsloth（MTP + 投機解碼）**
- 安裝：`pip install unsloth` 或 `github.com/unslothai/unsloth`
- 用途：Mac/GPU/CPU 自動選最佳 MTP 設定，支援 Qwen3.6、4-bit GGUF
- GitHub：https://github.com/unslothai/unsloth

**Huihui Qwen3.6-27B abliterated MTP-GGUF**
- 安裝：透過 llama.cpp 載入，`--spec-type draft-mtp --spec-draft-n-max 6`
- 用途：去審查版 Qwen3.6-27B，MTP 加速版，速度加倍
- HuggingFace：`huihui-ai/Huihui-Qwen3.6-27B-abliterated-MTP-GGUF`

**Qwopus Coder 9B（MTP-GGUF）**
- 安裝：透過 llama.cpp 或 LM Studio 載入
- 用途：程式碼生成特化模型，支援 MTP 加速
- HuggingFace：`Jackrong/Qwopus3.5-9B-Coder-MTP-GGUF`

**Qwen Fixed Chat Templates**
- 安裝：下載並替換 chat template
- 用途：修復 Qwen 3.5/3.6 的 jinja chat template，支援動態切換 thinking 模式
- HuggingFace：`froggeric/Qwen-Fixed-Chat-Templates`

**visit-website（LM Studio Plugin）**
- 安裝：從 LM Studio plugin 頁面安裝 `danielsig/visit-website`
- 用途：讓 LLM 能「瀏覽」網頁，提供連結、圖片 URL 與文字內容
- LM Studio：`lmstudio.ai`

---

### ComfyUI 節點

**Pixal3D-ComfyUI**
- 安裝：ComfyUI Manager 搜尋或 `git clone github.com/Saganaki22/Pixal3D-ComfyUI`
- 用途：單圖轉高保真 3D，輸出 textured GLB，支援手動相機控制
- GitHub：https://github.com/Saganaki22/Pixal3D-ComfyUI

**ComfyUI-LiTo（Apple LiTo 封裝）**
- 安裝：`git clone github.com/PozzettiAndrea/ComfyUI-LiTo`
- 用途：單張 RGBA 圖像轉 3D Gaussians，Apple 官方模型，精準重現光澤反射
- GitHub：https://github.com/PozzettiAndrea/ComfyUI-LiTo

**SPEED Sampler（非官方 ComfyUI 節點）**
- 安裝：URL 見原推文回覆
- 用途：從低解析度漸進式升至高解析度，大幅縮短生成時間，專為 Anima 模型設計

**Nvidia RTX 2-Pass Upscaler**
- 安裝：ComfyUI 自訂節點
- 用途：DeBlur 等4種 RTX 功能搭配雙 pass 處理，提升 AI 影片清晰度

**LTX Director（WhatDreamsCost）**
- 安裝：`git clone github.com/WhatDreamsCost/WhatDreamsCost-ComfyUI`
- 用途：在 ComfyUI 內本地製作進階 LTX 2.3 AI 影片，完全免費開源
- GitHub：https://github.com/WhatDreamsCost/WhatDreamsCost-ComfyUI

---

### 語音合成（TTS）

**Irodori-TTS-Lite（1GB VRAM）**
- 安裝：`github.com/kizuna-intelligence/Irodori-TTS-Lite`
- 用途：Int4 量化推論，舊版需 5GB VRAM，現僅需 1GB，含官方 v3 模型
- GitHub：https://github.com/kizuna-intelligence/Irodori-TTS-Lite

**Irodori-TTS-500M-v3**
- 安裝：`pip install` 或透過 HuggingFace 下載
- 用途：500M 參數日文 TTS 模型，v3 改善了 v2 相容性問題
- HuggingFace：`Aratako/Irodori-TTS-500M-v3`

---

### 3D 生成 / 世界模型

**HY World 2.0（Tencent Hunyuan）**
- 安裝：`git clone github.com/Tencent-Hunyuan/HY-World-2.0`
- 用途：單張圖片生成可編輯的完整 3D 世界，支援引擎匯出
- GitHub：https://github.com/Tencent-Hunyuan/HY-World-2.0

**Lance（ByteDance，3B active params）**
- 安裝：HuggingFace 下載
- 用途：多模態輸入（文字+圖片+影片），同時輸出三種模態，3B active 參數
- HuggingFace：`bytedance-research/Lance`

---

### 推理框架

**vLLM v0.21.0**
- 安裝：`pip install vllm`
- 用途：LLM 推論框架，本版新增 KV Offload+HMA、thinking budget 投機解碼、DeepSeek V4 pipeline 平行
- GitHub：https://github.com/vllm-project/vllm

---

### 開發者工具

**anime.js（JavaScript 動畫引擎）**
- 安裝：`npm install animejs` 或 `<script src="anime.min.js">`
- 用途：輕量級 JS 動畫引擎，支援 CSS/SVG/DOM/JS 屬性，68K stars
- GitHub：https://github.com/juliangarnier/anime

**swift-markdown-engine（macOS Markdown 編輯器）**
- 安裝：Swift Package Manager
- 用途：原生 AppKit Markdown 引擎，基於 TextKit 2 橋接 SwiftUI，支援即時樣式與 Wiki 連結
- GitHub：https://github.com/nodes-app/swift-markdown-engine

**novel2hermes_jp（日文長篇小說生成）**
- 安裝：`git clone github.com/kgmkm/novel2hermes_jp`
- 用途：結合 hermes-agent 長期記憶 + MeCab/ruri 日文向量記憶，支援多 LLM 並行推敲
- GitHub：https://github.com/kgmkm/novel2hermes_jp

**AI Toolkit（LoRA merge）**
- 安裝：Ostris 的 AI Toolkit
- 用途：新增 LoRA 合併功能（類 poor man's EMA），合併最後幾個 checkpoint 效果更佳

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| LM Studio 0.4.14+ | 本地 LLM GUI | App 內更新 | MTP 加速推論 | @CryZe107 |
| Unsloth | 推論框架 | `pip install unsloth` | 自動 MTP + 投機解碼 | @UnslothAI |
| Qwen3.6-27B MTP-GGUF | LLM 模型 | llama.cpp 載入 | 本地高速推論 | @ggerganov |
| Huihui abliterated MTP | LLM 模型 | HuggingFace 下載 | 去審查版 Qwen3.6 | @support_huihui |
| Qwopus Coder 9B | Coding 模型 | HuggingFace / llama.cpp | 程式碼生成 MTP | @KyleHessling1 |
| Pixal3D-ComfyUI | ComfyUI 節點 | git clone | 單圖→高保真 3D GLB | @SD_Tutorial |
| ComfyUI-LiTo | ComfyUI 節點 | git clone | 單圖→3D Gaussians (Apple) | @wildmindai |
| SPEED Sampler | ComfyUI 節點 | 見回覆 URL | 漸進升解析度加速 | @aiaicreate |
| RTX 2-Pass Upscaler | ComfyUI 節點 | ComfyUI Manager | AI 影片清晰化 | @aiaicreate |
| LTX Director | ComfyUI 節點 | git clone | LTX 2.3 影片生成 | @WhatDreamsCost |
| Irodori-TTS-Lite | TTS 工具 | GitHub | 1GB VRAM 日文語音合成 | @yusuke_kizuna |
| Irodori-TTS-500M-v3 | TTS 模型 | HuggingFace | 輕量 500M 日文 TTS | @shinshin86 |
| Stable Audio 3.0 | 音頻生成 | HuggingFace | 開源音頻生成 | @StabilityAI |
| HY World 2.0 | 3D 世界生成 | git clone | 單圖→可編輯 3D 世界 | @DylanTFWang |
| Lance (ByteDance) | 多模態模型 | HuggingFace | 文/圖/影三模態 I/O | @support_huihui |
| Cohere Command A+ | LLM 模型 | HuggingFace / API | 218B MoE 開源旗艦 | @cohere |
| SANA | 圖像生成 | GitHub | 4096px，16GB 筆電可跑 | @GithubProjects |
| vLLM v0.21.0 | 推論框架 | `pip install vllm` | KV Offload、投機解碼 | @vllm_project |
| anime.js | JS 動畫庫 | `npm install animejs` | CSS/SVG/DOM 動畫 | @VincentLogic |
| swift-markdown-engine | macOS 開發 | Swift PM | 原生 Markdown 引擎 | @QingQ77 |
| novel2hermes_jp | 日文生成 | git clone | 長篇小說 + 長期記憶 | @kagami_kami_m |
| visit-website | LM Studio 插件 | LM Studio 插件頁面 | LLM 瀏覽網頁 | @PhotogenicWeekE |
| Froggeric Chat Templates | 模型 patch | HuggingFace 下載 | 修復 Qwen 3.5/3.6 模板 | @populartourist |

---

## 🎯 本週趨勢

### 1. MTP 投機解碼成為本地推論新標配
llama.cpp 加入 Qwen3.6 家族的 MTP 支援，讓本地推論性能跨越新門檻。LM Studio、Unsloth 同步跟進，兩個簡單 flag 即可啟用 +78% 速度提升。可預期未來更多模型（不只 Qwen）會釋出 MTP 版本。

### 2. 4-bit 量化打通消費級顯卡運行前沿模型的最後一哩路
從 Cohere Command A+（NVFP4 W4A4）、Qwen3.6-35B-A3B MTP GGUF，到 SANA 的 4-bit 壓縮版（8GB 以下可跑），本週最大共同主題是：量化讓「理論上跑不動的大模型」在家用硬體上成為現實。

### 3. ComfyUI 生態系向 3D 全面擴張
本週新增多個 3D 相關 ComfyUI 節點：Pixal3D（TencentARC）、LiTo（Apple）、LTX Director（影片）。加上 HY World 2.0 的開源，3D 生成工作流正快速從「研究 demo」轉向「ComfyUI 一鍵可用」。

### 4. 開源模型授權趨向開放，社群二創加速
Pixal3D 從閉源改為 MIT、Cohere Command A+ 採 ASL 2.0、Stable Audio 3.0 輸出版權歸用戶——本週多個重量級模型選擇更開放的授權，直接降低商業應用與衍生創作的門檻，預計將帶動更多社群節點與微調模型出現。
