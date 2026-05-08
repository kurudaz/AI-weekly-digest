# OpenCode 週報 - 2026.05.08

---

## 🔥 本週焦點

### 1. LTX 2.3 生態系全面爆發
本週 LTX 2.3 相關的 LoRA 和節點呈現爆炸式增長。**Camera Man IC-LoRA** 首次實現精確的電影鏡頭控制（Zoom、Pan、Orbit），重點是提示詞要簡短——細節描述反而會鎖死鏡頭。**Obscura Remova LoRA** 可移除前景遮蔽物（煙霧、物件），強度需設在 1.5 左右才有效果。**ReStyle IC-LoRA** 則支援風格轉移，適合平塗、賽璐璐、單色線稿，但複雜材質效果有限。
- 來源：[@wildmindai](https://x.com/wildmindai)、[@DeepForest_Inc](https://x.com/DeepForest_Inc)、[@sunbaolong_2001](https://x.com/sunbaolong_2001)

### 2. 3D Gaussian Splatting 工具鏈成熟化
本週多個 3DGS 工具同步更新。**PlayCanvas SplatTransform 2.0** 新增全自動碰撞體生成（Voxel/Mesh 雙格式）；**3DGS-PLY-3DTiles-Converter** 將 PLY 轉為階層式 3D Tiles 並支援 SPZ 壓縮 GLB 輸出；另有開發者用 60 million Gaussian splats 在瀏覽器中即時渲染完整暗黑奇幻世界。
- 來源：[@playcanvas](https://x.com/playcanvas)、[@berttemme](https://x.com/berttemme)、[@XRarchitect](https://x.com/XRarchitect)

### 3. 本地推論加速三連擊
- **MTP（Multi-Token Prediction）**：使用 RDson/Qwen3.6-27B 在單張 RTX 3090 上實現 1.31x 速度提升
- **Luce DFlash**：純 C++ 引擎，零 Python，27B 模型在 24GB VRAM 跑出 130 tok/s，比標準自回歸解碼快 3.4x
- **Gemma 4 草稿模型**：31B Dense 搭配草稿模型可達 3x 加速，僅多佔 1GB 顯存
- 來源：[@mr_r0b0t](https://x.com/mr_r0b0t)、[@fahdmirza](https://x.com/fahdmirza)、[@karminski3](https://x.com/karminski3)

### 4. Blender MCP 官方開源
Blender 官方在 `projects.blender.org` 開源了 **Blender MCP**，讓任何 LLM 都能透過 MCP 協議操控 Blender，是本週開發工具整合的重要里程碑。
- 來源：[@msurguy](https://x.com/msurguy)

### 5. 單眼 MoCap 大躍進：MoCapAnything V2
從單眼影片直接推估任意骨架旋轉，無需 Mesh，支援任意骨架定義，推論速度比前版快 **20 倍**，且視頻到姿態與姿態到旋轉為端對端聯合訓練。
- 來源：[@taziku_co](https://x.com/taziku_co)

---

## 🛠️ 工具清單

### PlayCanvas SplatTransform 2.0
- **GitHub**：（官方 PlayCanvas 組織，開源 CLI）
- **用途**：3DGS 處理 CLI，自動生成 Voxel/Mesh 碰撞體，適合遊戲引擎整合
- **安裝**：參考 [@playcanvas](https://x.com/playcanvas) 發文連結

### 3DGS-PLY-3DTiles-Converter
- **GitHub**：`github.com/WilliamLiu-1997/3DGS-PLY-3DTiles-Converter`
- **用途**：將 Gaussian Splatting PLY 轉換為階層式 3D Tiles，支援 LOD 與 SPZ 壓縮 GLB 輸出
- **安裝**：`git clone https://github.com/WilliamLiu-1997/3DGS-PLY-3DTiles-Converter`

### react-video-ascii
- **GitHub**：`github.com/LucasHJin/react-video-ascii`
- **NPM**：`npm install react-video-ascii`
- **Demo**：`video-ascii-demo.vercel.app`
- **用途**：React 元件，將影片即時轉為 ASCII 字元動畫

### Higgsfield CLI + Skills
- **安裝**：`npx skills add higgsfield-ai/skills`
- **用途**：行銷影片生成 CLI，整合 Codex、Claude Code、Openclaw，減少 token 消耗並保持創意品質
- **來源**：[@higgsfield](https://x.com/higgsfield)

### comfyui-deno-custom-nodes（LTX 2.3 專用）
- **GitHub**：`github.com/Deno2026/comfyui-deno-custom-nodes`
- **用途**：LTX 2.3 生產工作流節點集，包含 Multi Image Loader、LTX Sequencer、Multi LoRA Loader、GGUF/KJ split VAE 載入

### ComfyUI-Sapiens2-Easy
- **GitHub**：`github.com/Bogyie/ComfyUI-Sapiens2-Easy`
- **用途**：Meta Sapiens2 的 ComfyUI 節點，支援分割、法線貼圖等

### comfy-mecha-extensions-anima
- **GitHub**：`github.com/blue-pen5805/comfy-mecha-extensions-anima`
- **用途**：讓 comfy-mecha 支援 Anima 模型及 LoRA

### MoCapAnything V2
- **專案頁**：`animotionlab.github.io/MoCapAnythingV2/`
- **GitHub**：`github.com/animotionlab26/MocapAnything`
- **用途**：從單眼影片端對端推估任意骨架動作，無需 Mesh，推論快 20x

### COLMAP 4.1 GPU 預編譯版
- **GitHub Releases**：`github.com/lyehe/build_gpu_colmap`
- **用途**：Windows/Ubuntu GPU 版 COLMAP 二進位包，支援 Python 3.10–3.14，省去自行編譯的麻煩

### Phosphene（Apple Silicon 本地影片生成）
- **安裝**：透過 Pinokio 一鍵安裝
- **用途**：Mac Apple Silicon 免費本地影片＋音訊生成器，基於 LTX 2.3 + MLX
- **來源**：[@AIBizarrothe](https://x.com/AIBizarrothe)

### Chro-NOS（CLIP Studio Paint 備份工具）
- **網站**：`cohistudio-lab.com`
- **用途**：CLIP Studio Paint 作業自動記錄歷程，可隨時還原任意繪製狀態，支援多語言

### cuimao-translator（PDF 翻譯 Claude Code Skill）
- **GitHub**：`github.com/Cuimao777/cuimao-translator`
- **用途**：一鍵將英文 PDF 翻譯為流暢中文的 Claude Code 技能

### Uber-Stylized-Water（Unity 水面 Shader）
- **GitHub**：`github.com/MatrixRex/Uber-Stylized-Water`
- **用途**：Unity 各類風格化水面 Shader 合集

### Map2World（文字轉 3D 世界）
- **專案頁**：`robot0321.github.io/Map2World/index.html`
- **用途**：從文字描述或草圖生成完整 3D 世界，基於 SLAT latent space multi-diffusion

### ClothFX（Blender 布料特效）
- **下載**：`superhivemarket.com/products/clothfx`、`albertofx.gumroad.com/l/iHFuk`、`payhip.com/b/5FPqR`
- **用途**：Blender 免費 Add-on，秒建破壞性布料特效

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| SplatTransform 2.0 | CLI / Library | GitHub 下載 | 3DGS 碰撞體生成 | @playcanvas |
| 3DGS-PLY-3DTiles-Converter | CLI | git clone | PLY → 3D Tiles 轉換 | @berttemme |
| react-video-ascii | npm 套件 | `npm i react-video-ascii` | 影片轉 ASCII React 元件 | @lucashjin |
| Higgsfield CLI | CLI | `npx skills add higgsfield-ai/skills` | AI 行銷影片生成 | @higgsfield |
| comfyui-deno-custom-nodes | ComfyUI 節點 | git clone | LTX 2.3 生產節點集 | @Deno2026 |
| ComfyUI-Sapiens2-Easy | ComfyUI 節點 | git clone | Meta Sapiens2 分割/法線 | @Bogyie |
| comfy-mecha-extensions-anima | ComfyUI 節點 | git clone | Anima 模型支援擴充 | @blue_pen5805 |
| MoCapAnything V2 | 研究專案 | git clone | 單眼影片任意骨架 MoCap | @taziku_co |
| COLMAP 4.1 GPU Build | 二進位工具 | GitHub Releases 下載 | 3D 重建 / SfM | @YeheLiu |
| Phosphene | 桌面 App | Pinokio 一鍵安裝 | Mac 本地 AI 影片生成 | @AIBizarrothe |
| Chro-NOS | 桌面 App | 官網下載 | CSP 繪圖歷程備份 | @COHI27151463 |
| cuimao-translator | Claude Code Skill | git clone | 英文 PDF → 中文翻譯 | @CuiMao |
| Uber-Stylized-Water | Unity Package | git clone | Unity 風格化水面 Shader | @VFX_Therapy |
| Map2World | 研究專案 | 官網 Demo | 文字/草圖 → 3D 世界 | — |
| ClothFX | Blender Add-on | Gumroad / Payhip（免費） | Blender 布料破壞特效 | @AFX_LAB |
| Blender MCP | MCP Server | projects.blender.org | LLM 操控 Blender | @msurguy |
| LTX 2.3 Camera Man LoRA | HuggingFace LoRA | HF 下載 + ComfyUI | AI 影片電影鏡頭控制 | @sunbaolong_2001 |
| Obscura Remova LoRA | HuggingFace LoRA | civitai.com/models/2590144 | LTX 2.3 前景遮蔽物移除 | @109 |
| LTX2.3 ReStyle IC-LoRA | HuggingFace LoRA | HF: Cseti/LTX2.3-22B_ReStyle | 風格轉移 I2V | @wildmindai |
| Kohya Anima-LLLite | HuggingFace LoRA | HF: kohya-ss/Anima-LLLite | Anima anytest 控制網路 | @kohya_tech |
| tori29umai 漫畫標注模型 | HuggingFace Model | HF: tori29umai/rtdetrv4-x-manga109s | 漫畫格框/人物/台詞偵測 | @tori29umai |
| VoxCPM2 | HuggingFace TTS | OpenBMB 官方釋出 | 2B 參數 48kHz 多語言 TTS | @Honcia13 |

---

## 🎯 本週趨勢

### 1. LTX 2.3 成為開源影片 LoRA 的主戰場
短短一週內出現 Camera Man、Obscura Remova、ReStyle、10 Eros 等多款 IC-LoRA，加上 ComfyUI 自定義節點的配套，LTX 2.3 的可控性已明顯超越其他開源影片模型，形成類似 Stable Diffusion 的生態系快速膨脹態勢。

### 2. 3DGS 從研究工具走向生產管線
SplatTransform 2.0 自動碰撞體、3D Tiles 轉換器、LichtFeld Studio 整合、甚至 60 million splats 的瀏覽器即時渲染——這些訊號指向 3DGS 開始被當作正式的遊戲/XR 生產資產格式，而非只是研究展示。

### 3. 本地推論加速進入「多路並行」時代
MTP、Speculative Decoding、純 C++ 推論引擎（Luce DFlash）同週爆發，且都不依賴 vLLM 或 llama.cpp 的主流框架。消費級 GPU（RTX 3090）跑 27B 模型達 130 tok/s 正在成為現實，推論加速的技術路線從單一變為多元競爭。

### 4. MCP 整合滲透到垂直領域工具
Blender 官方 MCP、Spector.js（WebGL 剖析）MCP 相繼出現，不再只是通用開發工具。這預示著 MCP 協議正在成為創意/3D 工具與 LLM 溝通的標準介面，各垂直工具往後都可能陸續推出官方 MCP Server。
