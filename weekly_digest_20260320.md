週報內容已準備完畢。以下是本週完整週報，請確認後我可以儲存至 `reports/weekly_digest_20260320.md`：

---

# OpenCode 週報 - 2026.03.20

> 本週書籤精選：CLI-Anything 讓 AI 操控所有軟體、Hermes Agent 寫完整小說、本地 OCR/3D/影片工具全面爆發、Claude Skills 生態系加速成熟

---

## 🔥 本週焦點

### 1. CLI 化浪潮：AI Agent 直接操控所有桌面軟體

本週最具顛覆性的方向——**CLI-Anything**（HKUDS）讓任何現有軟體都能透過 CLI 被 AI Agent 操控，已在 GIMP、Blender、LibreOffice、OBS Studio 等 11 款應用驗證。同步推進的還有：

- **OpenCLI v1.0**（jackwener）：從 Playwright 完全切換為自研輕量 Daemon + Chrome Extension 架構，支援 CDP 遠端直連 WebSocket，更精簡穩健
- **tldraw desktop v1.1.1**：Mac arm 桌面版正式上線，可讓 Agent 透過 `curl localhost:7236` 建立/更新流程圖，再反向更新程式碼——圖即程式碼雙向同步
- **gstack**（garrytan）：Garry Tan 的 Claude Code 6 工具組合，MIT 授權，含 `/office-hours` skill 提供 YC 式創業思考框架

來源：[@LangChainJP](https://x.com/LangChainJP)、[@jakevin7](https://x.com/jakevin7)、[@tldraw](https://x.com/tldraw)、[@garrytan](https://x.com/garrytan)

---

### 2. 本地 OCR / PDF 工具崛起，CPU-only 也能飛速處理

- **GLM-OCR**：僅需約 2GB VRAM，處理表格、數學公式，在 Mac Studio M2 Ultra 達 **260 tok/s**，可用 Ollama 直接運行
- **Folio-OCR**：開源批次 OCR 工作台，整合 Ollama + GLM-OCR + PP-DocLayoutV3，RTX 4090 約 **0.5 秒/頁**，是 ABBYY FineReader 的免費本地替代方案
- **PDF → Markdown 工具**（Laughing 分享）：100+ 頁/秒，純 CPU，可輸出帶座標 JSON / HTML，非常適合本地 RAG 知識庫建構

---

### 3. ComfyUI 影片生成多項突破

- **LTX 2.3 Motion Track LoRA**：在 ComfyUI 中畫 spline 曲線控制人物運動、鏡頭平移與縮放，告別隨機 AI 動態
- **LTX 2.3 NVFP4**（Lightricks）：Blackwell 架構（RTX 5000 系列）優化量化版，HuggingFace 已上架
- **Cosmos-Predict2.5-2B DMD2 LoRA**：LCM sampler 8 steps，生成時間從 77 秒降至 **26 秒**
- **ComfyUI-PromptEngine**：字典式提示詞組合節點，21 個類別結構化管理

---

### 4. AI Agent 自主能力新里程：Hermes Agent 寫完整長篇小說

**Nous Research** 的 Hermes Agent 完全自主撰寫 **79,456 字、19 章**的長篇小說《The Second Son of the House of Bells》，採用「修改→評估→保留/捨棄」pipeline 循環，GTC 活動已製作實體書分發。Ole Lehmann 同步分享了用 Karpathy autoresearch 方法自動優化 Claude Skills 成功率的完整方法論。

---

### 5. 本地 3D 生成新工具

- **Modly**（lightningpixel）：桌面版，圖片→3D 模型，本地 GPU，開源
- **MV-SAM3D**（devinli123）：多視角 SAM3D，解決原版單視角限制
- **InSpatio-World**：影片→可移動 3D 世界（可改變視角、時間軸），開源
- **InkForm**（Blender）：Grease Pencil 手繪線條立體化，保留筆壓

---

## 🛠️ 工具清單

### CLI-Anything
- **GitHub**：https://github.com/HKUDS/CLI-Anything
- **安裝**：`git clone https://github.com/HKUDS/CLI-Anything`
- **用途**：GIMP、Blender、LibreOffice 等 GUI 軟體 CLI 化，供 AI Agent 操控

### OpenCLI v1.0
- **GitHub**：https://github.com/jackwener/opencli
- **安裝**：Chrome Extension + Daemon（依 README）
- **用途**：任意網站變 CLI，支援 CDP 遠端直連

### gstack
- **GitHub**：https://github.com/garrytan/gstack
- **安裝**：依 README 複製配置
- **用途**：Garry Tan（YC）Claude Code 6 工具組，含 `/office-hours` skill，MIT 授權

### tldraw desktop
- **GitHub**：https://github.com/tldraw/tldraw-desktop（v1.1.1，Mac arm）
- **安裝**：下載執行檔
- **用途**：Agent 透過 `curl localhost:7236` 雙向同步圖與程式碼

### Modly（本地 3D 生成）
- **GitHub**：https://github.com/lightningpixel/modly
- **安裝**：桌面版下載
- **用途**：圖片→3D 模型，本地 GPU，支援任意模型

### InkForm（Blender add-on）
- **參考**：https://modelinghappy.com/archives/67688
- **用途**：Grease Pencil 線條立體化，保留筆壓，樹枝/電纜/植物建模

### digeo
- **文件**：https://digeo.readthedocs.io
- **安裝**：`pip install digeo`
- **用途**：3D Mesh 微分幾何 PyTorch 庫，AI 3D 研究基礎

### ComfyUI-PromptEngine
- **安裝**：ComfyUI Manager 搜尋安裝
- **用途**：21 類別字典式 prompt 節點，結構化、可複用

### Unsloth Studio
- **GitHub**：https://github.com/unslothai/unsloth
- **安裝**：`pip install unsloth`
- **用途**：開源 LLM 訓練 Web UI，2x 加速，70% 省 VRAM，支援 Mac/Win/Linux

### RemoveBG
- **GitHub**：https://github.com/AonekoSS/RemoveBG
- **安裝**：執行檔下載（首次自動下載 BiRefNet ~950MB）
- **用途**：本地圖片背景去除，無需網路

### Folio-OCR
- **GitHub**：https://github.com/vorojar/Folio-OCR
- **安裝**：依 README（需 Ollama + GLM-OCR）
- **用途**：本地批次 OCR，0.5 秒/頁，適合 RAG 知識庫

### MV-SAM3D
- **GitHub**：https://github.com/devinli123/MV-SAM3D
- **安裝**：`git clone`
- **用途**：多視角 SAM3D，解決單視角限制

### userinterface-wiki skill
- **安裝**：`npx skills add raphaelsalaja/userinterface-wiki`
- **用途**：119 條 UI 設計規則（動畫/時序/UX Laws/字體/音效等 11 類），Claude skill 直接使用

### Qwen3.5-4B 日文特化版
- **HuggingFace**：https://huggingface.co/dahara1/Qwen3.5-4B-UD-japanese-imatrix
- **安裝**：下載 Q4_K_XL（約 2.8GB）+ llama.cpp
- **用途**：日文特化本地 LLM Agent，可自主使用 grep 等工具

### LTX 2.3 NVFP4
- **HuggingFace**：https://huggingface.co/Lightricks/LTX-2.3-nvfp4
- **安裝**：ComfyUI 模型下載（Blackwell GPU 專用）
- **用途**：RTX 5000 系列優化的 LTX 影片生成量化版

### Mistral Small 4
- **官網**：https://mistral.ai
- **安裝**：`ollama pull mistral-small4` 或 API
- **用途**：Mistral 最新開源小模型，可本地運行

### Learn Claude Code
- **作者**：@baicai003（@dotey、@HiTw93 推薦）
- **用途**：12 節課從零建構類 Claude Code AI 代理，解析 coding agent 核心迴圈

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|---------|---------|------|
| CLI-Anything | AI Agent 框架 | git clone | GUI 軟體 CLI 化供 AI 操控 | GitHub HKUDS |
| OpenCLI v1.0 | 瀏覽器自動化 CLI | Chrome Extension | 任意網站變 CLI，AI-native | GitHub jackwener |
| gstack | Claude Code 工具組 | 複製配置 | YC 式 /office-hours skill | GitHub garrytan |
| tldraw desktop | 圖形工具 | 下載 v1.1.1（Mac arm） | 圖⇄程式碼雙向同步 | GitHub tldraw |
| Modly | 3D 生成 | 桌面版下載 | 圖片→3D 模型，本地 GPU | GitHub lightningpixel |
| InkForm | Blender add-on | add-on 安裝 | Grease Pencil 線條立體化 | modelinghappy.com |
| digeo | PyTorch 庫 | pip install digeo | 3D Mesh 微分幾何運算 | digeo.readthedocs.io |
| ComfyUI-PromptEngine | ComfyUI 節點 | Manager 安裝 | 結構化 prompt 管理 21 類別 | ComfyUI |
| Unsloth Studio | LLM 訓練 Web UI | pip install unsloth | 本地 LLM 訓練，2x 加速 | GitHub unslothai |
| RemoveBG | 背景去除工具 | 執行檔下載 | 本地 BiRefNet 背景去除 | GitHub AonekoSS |
| Folio-OCR | 批次 OCR | 依 README | 本地 OCR 工作台，0.5s/頁 | GitHub vorojar |
| MV-SAM3D | 3D 分割 | git clone | 多視角 SAM3D | GitHub devinli123 |
| userinterface-wiki | Claude Skill | npx skills add | 119 條 UI 設計規則 | npx skills |
| Qwen3.5-4B 日文版 | 本地 LLM | 下載 GGUF（2.8GB） | 日文特化，本地 Agent | HuggingFace dahara1 |
| LTX 2.3 NVFP4 | 影片生成模型 | ComfyUI 下載 | Blackwell GPU 優化影片生成 | HuggingFace Lightricks |
| Mistral Small 4 | 開源 LLM | ollama / API | 新一代輕量開源模型 | mistral.ai |
| Learn Claude Code | 教育專案 | GitHub | 12 課從零建構 coding agent | GitHub baicai003 |

---

## 🎯 本週趨勢

### 1. AI Agent 開始接管桌面工作流
CLI-Anything 和 OpenCLI v1.0 讓 AI Agent 從 IDE/Terminal 延伸到 GIMP、Blender 等 GUI 軟體。tldraw 的圖↔程式碼雙向同步是縮影。Rohit 點出核心：**決定 AI 上限的不是模型，是 harness（執行環境）**。

### 2. 本地端突破「好用門檻」
GLM-OCR 2GB VRAM 達 260 tok/s、Folio-OCR CPU-only 100+ 頁/秒、Modly 本地 3D 生成、Unsloth Studio 本地 LLM 訓練。**「本地=妥協」的時代正在結束**。

### 3. Claude Skills 生態系進入快速擴張期
gstack、userinterface-wiki skill、autoresearch 優化方法論、M-NEF 小說框架同週出現；Google Cloud Tech 整理 5 種 SKILL.md 設計模式，Thariq（609 萬追蹤）分享最佳實踐。**skill 正從個人工具演化為社群共享資產**。

### 4. 開源模型去審查與蒸餾成新常態
Qwen3.5 35B/27B/4B 均有去審查版（HauhauCS、huihui-ai）；Claude Opus 4.6 蒸餾版持續迭代。黃仁勳「每人配 100 個 AI Agent」的願景，正被開發者社群以本地化形式自行落實。

---

*生成時間：2026-03-20 | 來源：X/Twitter 書籤精選*

---

週報內容已完整呈現。如需儲存到 `reports/weekly_digest_20260320.md`，請允許檔案寫入權限，我會立即儲存。
