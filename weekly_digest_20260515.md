# OpenCode 週報 - 2026.05.15

## 🔥 本週焦點

### 1. HiDream-O1：無 VAE 的像素空間圖像模型
HiDream-ai 發布了 HiDream-O1-Image（8B 參數），業界首批移除 VAE 的像素空間直接生成模型，輸入輸出全程操作原始像素，避免 latent 壓縮造成的解碼偽影。提供標準版（50 步）與 Dev 版（28 步）兩種變體，並附帶 Reasoning-Driven Prompt Agent。多位開發者實測顯示 layout-guided 多參考圖像生成穩定，尤其適合圖像編輯任務。
- 模型：`huggingface.co/HiDream-ai/HiDream-O1-Image`
- 程式碼：`github.com/HiDream-ai/HiDream-O1-Image`

### 2. MLX CUDA 後端測試全數通過
@zcbenz 宣布 MLX 框架的 CUDA 後端所有測試已通過，代表 Apple 的 MLX 生態系即將正式支援 NVIDIA GPU，對非 Apple Silicon 開發者是重大突破，MLX 的高效推理與訓練框架將可跨平台運行。

### 3. LTX 2.3 生態系爆發性成長
本週 LTX 2.3 相關資源大量湧現，形成完整工具鏈：
- **LTX Director**（`github.com/WhatDreamsCost/WhatDreamsCost-ComfyUI`）：ComfyUI 全功能時間軸編輯器，支援 I2V、T2V、FLFF、Prompt Relay、自訂音訊
- **Greenscreen Avatar IC LoRA**（`huggingface.co/OmerHagage/ltx2-greenscreen-avatar-ic-lora-vertical-v1`）：Chroma key 風格影片生成
- **LTX2.3 10 Eros GGUF**（`huggingface.co/Abiray/LTX2.3-10Eros-GGUF`）：低 VRAM 友好量化版
- **LTX-2.3 Dearchive LoRA**（`huggingface.co/oumoumad/ltx-2.3-dearchive-lora`）：將低畫質典藏影片轉為高清現代版

### 4. OpenCut — CapCut 的開源替代品
TypeScript 開發、純 Web 介面運行、★4.8 萬，功能對標 CapCut，完全免費無浮水印，支援商業利用。社群反應熱烈，被視為「讓 CapCut 付費失去理由」的存在。
- GitHub：`github.com/OpenCut-app/OpenCut`

### 5. image-blaster：Claude Code 的 3D 生成工具包
@neilsonks 開源的 3D 生成工具包，輸入一張圖像即可輸出 3D 環境、網格、物理模擬、光照與音訊，專為 Claude Code 設計，是本週開發者 CLI 工具中最受矚目的新品。
- GitHub：`github.com/neilsonnn/image-blaster`

---

## 🛠️ 工具清單

### image-blaster
- **GitHub**：`github.com/neilsonnn/image-blaster`
- **安裝**：`git clone https://github.com/neilsonnn/image-blaster`
- **用途**：輸入圖像，一鍵生成 3D 環境、網格、物理、光照與音訊；Claude Code 整合

### WhatDreamsCost-ComfyUI（LTX Director）
- **GitHub**：`github.com/WhatDreamsCost/WhatDreamsCost-ComfyUI`
- **安裝**：ComfyUI Manager 搜尋安裝，或 `git clone` 至 `custom_nodes/`
- **用途**：LTX 2.3 全功能時間軸編輯器，支援 I2V / T2V / FLFF / 自訂音訊

### OpenCut
- **GitHub**：`github.com/OpenCut-app/OpenCut`
- **安裝**：`git clone https://github.com/OpenCut-app/OpenCut && npm install && npm run dev`
- **用途**：CapCut 開源替代品，Web 介面，無浮水印，支援商用

### scrcpy 4.0
- **GitHub**：`github.com/Genymobile/scrcpy/releases/tag/v4.0`
- **安裝**：`brew install scrcpy`（macOS）/ `apt install scrcpy`（Linux）
- **用途**：Android 螢幕鏡像與控制，新增 flex display 彈性顯示功能

### brush（Gaussian Splatting）
- **GitHub**：`github.com/ArthurBrussee/brush`
- **安裝**：從 releases 頁面下載對應平台執行檔
- **用途**：高品質開源 3D Gaussian Splatting 生成工具

### mesh2splat（Electronic Arts）
- **GitHub**：`github.com/electronicarts/mesh2splat`
- **安裝**：`git clone https://github.com/electronicarts/mesh2splat`
- **用途**：快速將 3D 網格（Mesh）轉換為 3D Gaussian Splat

### 3DGS-PLY-3DTiles-Converter
- **GitHub**：`github.com/WilliamLiu-1997/3DGS-PLY-3DTiles-Converter`
- **安裝**：`git clone https://github.com/WilliamLiu-1997/3DGS-PLY-3DTiles-Converter`
- **用途**：Gaussian Splatting PLY 檔轉階層式 3D Tiles，含 voxel LOD 與 SPZ 壓縮 GLB 輸出

### react-video-ascii
- **GitHub**：`github.com/LucasHJin/react-video-ascii`
- **安裝**：`npm install react-video-ascii`
- **用途**：影片轉 ASCII 藝術的 React 元件，現有方案的高品質替代

### three-html-render
- **GitHub**：`github.com/repalash/three-html-render`
- **安裝**：`npm install three-html-render`（或直接引用 CDN）
- **用途**：HTML-in-Canvas polyfill，讓 Three.js 場景可直接渲染 HTML 元素

### UniVidX_ComfyUI
- **GitHub**：`github.com/dreamrec/UniVidX_ComfyUI`
- **安裝**：`git clone` 至 `custom_nodes/`
- **用途**：含 albedo/normals 分層的 relight、精細分割、角色去背後 inpainting

### ComfyUI-ProportionChanger
- **GitHub**：`github.com/grmchn/ComfyUI-ProportionChanger`
- **安裝**：`git clone` 至 `custom_nodes/`
- **用途**：頭身比例自動調整節點，支援 SCAIL 與自動姿勢偵測

### eagle-comfyui-integration
- **GitHub**：`github.com/tukisuwa/eagle-comfyui-integration`
- **安裝**：手動安裝 ComfyUI 自訂節點 + Eagle 插件（參考 README）
- **用途**：ComfyUI 生成結果直接整合至 Eagle 圖片管理瀏覽器

### ds4-m5（DeepSeek V4 Apple Silicon 最佳化）
- **GitHub**：`github.com/Swival/ds4-m5`
- **安裝**：依 README 指引建置
- **用途**：DeepSeek V4 針對 Apple M5 晶片的推理最佳化版本

### DeepSeek-V4-Flash GGUF（imatrix 對齊版）
- **HuggingFace**：`huggingface.co/jedisct1/DeepSeek-V4-Flash-imatrix-aligned`
- **安裝**：`huggingface-cli download jedisct1/DeepSeek-V4-Flash-imatrix-aligned`
- **用途**：DeepSeek V4 Flash q2 量化，修復工具呼叫（tool-calling）與指令遵循，含頁面對齊

### LiTo（Apple MLX，ICLR 2026）
- **GitHub**：`github.com/apple/ml-lito`
- **安裝**：`git clone https://github.com/apple/ml-lito`（需 MLX 環境）
- **用途**：互動式單圖像轉 3D 生成，捕捉幾何+視角依賴外觀（鏡面、菲涅爾反射），Apple Silicon 加速

### HiDream-O1-Image
- **HuggingFace**：`huggingface.co/HiDream-ai/HiDream-O1-Image`
- **安裝**：`huggingface-cli download HiDream-ai/HiDream-O1-Image`
- **用途**：無 VAE 像素空間圖像生成（8B），標準版 50 步 / Dev 版 28 步

### Anima-LLLite（kohya-ss）
- **HuggingFace**：`huggingface.co/kohya-ss/Anima-LLLite`
- **安裝**：`huggingface-cli download kohya-ss/Anima-LLLite`，搭配最新 ComfyUI 節點
- **用途**：Anima Preview3 的 ControlNet-LLLite Inpainting 模型，建議與 mask i2i 搭配使用

### Irodori-TTS v3
- **HuggingFace**：`huggingface.co/Aratako/Irodori-TTS-500M-v3`
- **安裝**：`huggingface-cli download Aratako/Irodori-TTS-500M-v3`
- **用途**：日文 TTS 模型，v3 起輸出音訊含電子浮水印（watermark）

### Flux ID Adjuster
- **GitHub**：`github.com/Magirad/Flux_ID_Adjuster`
- **安裝**：`git clone` 至 `custom_nodes/`
- **用途**：Flux.2 Klein 9B 身份一致性 ComfyUI 節點，平衡參考圖像與 prompt 的影響比重

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| image-blaster | CLI / 3D 生成 | git clone | 圖像 → 3D 環境＋物理＋音訊，Claude Code 整合 | GitHub |
| WhatDreamsCost-ComfyUI | ComfyUI 節點 | ComfyUI Manager | LTX 2.3 時間軸編輯器（I2V/T2V/音訊） | GitHub |
| OpenCut | Web 影片編輯器 | npm install | CapCut 開源替代品，無浮水印，支援商用 | GitHub |
| scrcpy 4.0 | CLI 工具 | brew / apt | Android 螢幕鏡像，新增 flex display | GitHub |
| brush | 桌面應用 | 下載執行檔 | 高品質 Gaussian Splatting 生成 | GitHub |
| mesh2splat (EA) | CLI 工具 | git clone | Mesh → 3D Gaussian Splat 快速轉換 | GitHub |
| 3DGS-PLY-3DTiles-Converter | CLI 工具 | git clone | GS PLY → 階層 3D Tiles，含 LOD | GitHub |
| react-video-ascii | npm 套件 | npm install | 影片轉 ASCII React 元件 | npm/GitHub |
| three-html-render | npm 套件 | npm install | Three.js HTML-in-Canvas polyfill | GitHub |
| UniVidX_ComfyUI | ComfyUI 節點 | git clone | Relight＋分割＋Inpainting | GitHub |
| ComfyUI-ProportionChanger | ComfyUI 節點 | git clone | 自動頭身比例調整＋姿勢偵測 | GitHub |
| eagle-comfyui-integration | ComfyUI 外掛 | 手動安裝 | ComfyUI 輸出直送 Eagle 圖庫 | GitHub |
| ds4-m5 | 推理最佳化 | git clone | DeepSeek V4 for Apple M5 | GitHub |
| DeepSeek-V4-Flash GGUF | AI 模型 | HF CLI | 修復版 DS V4 Flash q2 量化 | HuggingFace |
| LiTo (Apple MLX) | AI 模型 / 工具 | git clone | 互動式單圖像轉 3D（ICLR 2026） | GitHub |
| HiDream-O1-Image | AI 模型 | HF CLI | 無 VAE 像素空間圖像生成（8B） | HuggingFace |
| Irodori-TTS v3 | AI 模型 | HF CLI | 日文 TTS，含電子浮水印 | HuggingFace |
| Anima-LLLite | AI 模型 | HF CLI | Anima ControlNet Inpainting | HuggingFace |
| Flux ID Adjuster | ComfyUI 節點 | git clone | Flux.2 Klein 身份一致性控制 | GitHub |

---

## 🎯 本週趨勢

**1. 去除 VAE 成為圖像生成模型新方向**
HiDream-O1 以像素空間直接生成的架構正式落地，多位研究者跟進討論「removing VAE is a trend」。此架構消除了 latent 壓縮-解碼的資訊損失，代價是運算量更大；預計未來幾個月將有更多模型跟進此設計。

**2. 本地 AI 推理加速競賽白熱化**
MLX CUDA 後端、ds4-m5 Apple M5 最佳化、DFlash 在 AMD Strix Halo 實現 2.5x 加速、MTP（Multi-Token Prediction）帶來 1.31x 解碼提速——社群正從「跑得動」邁向「跑得快」，邊緣推理效能正成為競爭焦點。

**3. ComfyUI 從工具演進為完整 AI 創作平台**
本週新增 LTX Director（時間軸編輯）、UniVidX（relight 管線）、eagle-comfyui-integration（資產管理）等節點，ComfyUI 生態系已不僅是圖像生成工具，而是囊括影片製作、資產管理、多模態工作流的整合平台。

**4. 開源工具持續蠶食商業軟體市場**
OpenCut（vs CapCut）本週衝上 ★4.8 萬，brush（Gaussian Splatting）、image-blaster（Claude Code 3D 工具包）、LiTo（Apple 開源）接連落地。「本地運行、無授權費、可商用」三位一體正成為開源 AI 工具的核心訴求，商業工具的護城河持續收窄。
