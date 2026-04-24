# OpenCode 週報 - 2026.04.24

---

## 🔥 本週焦點

### 1. GPT Image 2 掀起創意工作流浪潮
OpenAI 的 GPT Image 2（又稱 ChatGPT Images 2.0）本週成為社群最熱話題。多位創作者分享了突破性用法：透明背景 PNG 輸出、角色三視圖設定資料自動生成、360° 等距圓柱全景圖、LINEスタンプ批次生成、漫畫→分鏡→動畫完整工作流。社群也發現過度「閃亮感」問題，解決方案是在提示詞末尾加上「滑らかな線と塗り + セルシェーディング」。
**來源**：@yachimat_manga、@852wa、@MatoToushi、@GlitterPixely

### 2. Qwen3.6 系列模型集中爆發
Qwen3.6 系列本週多個版本同步登場：
- **27B**：一張 RTX 3090 可達 85 TPS 持續輸出、125K context、視覺功能全開
- **35B-A3B**：NVFP4 精度量化版（@Ex0byt）、RYS-SABER GGUF 版（@DJLougen）、Claude 4.7 Opus Reasoning 蒸餾版（@support_huihui）
- **2.7B**：TurboQuant 輕量版（@coffeecup2020）

本地運行大型 MoE 模型的門檻正在快速降低。
**來源**：@malikwas1f、@Ex0byt、@DJLougen、@Italianclownz

### 3. ComfyUI 生態系本週重大更新
多個重要節點與功能齊發：
- **SUPIR 超解析度**已納入 ComfyUI 官方預設支援
- **LTX 2.3 Refocus IC LoRA**：讓失焦影片重新對焦
- **Arrow 1.1**（QuiverAI）：可輸出可編輯 SVG 向量圖的 Partner Node
- **Flux2Klein Ksampler**：修正色偏、彩度衰減問題
- **ComfyUI-NAG-Extended**：支援 Flux 2 Klein 與 Anima 的 NAG 負面提示加速
**來源**：@ComfyUI、@wildmindai、@SlipperyGem、@dx8152

### 4. 3D Gaussian Splatting 多線齊進
本週 Gaussian Splatting 在多個方向同時推進：Apple SHARP 模型可從單張 2D 圖像生成 Gaussian Splat 粒子動畫（@tjcages）；Tencent HunyuanWorld 2.0 在 HuggingFace Spaces 上線，支援 3D 重建與相機姿態估計；Lukas Radl 的 CoMe 研究提出純自監督的高保真 Unbounded Mesh 提取方案。
**來源**：@tjcages、@YeheLiu、@rafourdl、@prithivMLmods

### 5. LLM 推理加速新里程碑
`llama.cpp` 推出 `--ai-tune` 參數，讓 LLM 自動最佳化自身的推理設定，Qwen3.5-27B 速度從 18.5 tok/s 提升至 40.05 tok/s（+54%）。另有 lucebox-hub 用 C++/ggml 投機解碼（Speculative Decoding）搭配 DFlash block-diffusion draft，在單張 RTX 3090 上達到 **207.6 tok/s**（5.46× 加速）。
**來源**：@ai_hakase_、@pupposandro

---

## 🛠️ 工具清單

### ComfyUI-NAG-Extended
- **GitHub**：`github.com/BigStationW/ComfyUI-NAG-Extended`
- **安裝**：在 ComfyUI Manager 搜尋 `NAG-Extended` 或手動 git clone 至 custom_nodes
- **用途**：為 Flux 2 Klein、Anima 等 1.0 CFG 加速 LoRA 提供 NAG 負面提示支援

### Comfy Canvas v1.0
- **GitHub**：`github.com/Zlata-Salyukova/Comfy-Canvas`
- **安裝**：git clone 至 ComfyUI/custom_nodes 目錄
- **用途**：ComfyUI 內建的即時影像編輯器，支援本地 workflow tab 運行

### n8ao-webgpu
- **GitHub**：`github.com/marioandf/n8ao-webgpu`
- **安裝**：npm 套件或直接引入
- **用途**：WebGPU 環境的 Ambient Occlusion 解決方案，提升 Three.js 場景真實感

### AI4AnimationPy
- **GitHub**：`github.com/facebookresearch/ai4animationpy`
- **Web Demo**：`paulstarke-ai4animationpy.hf.space`
- **安裝**：git clone + pip install（支援瀏覽器執行）
- **用途**：風格化雙足與四足動物步態控制 AI 動畫，可於手機瀏覽器運行

### vLLM v0.19.1
- **安裝**：`pip install vllm==0.19.1`
- **GitHub**：`github.com/vllm-project/vllm`
- **用途**：LLM 推理伺服器，本版支援 Gemma 4、GLM 5.1、Kimi K2.5，升級 Transformers v5

### lucebox-hub
- **GitHub**：`github.com/Luce-Org/lucebox-hub`
- **安裝**：git clone，參考 README 的 C++ 構建指引
- **用途**：手工調優的 LLM 推理引擎，內建 speculative decoding，單張 3090 達 207 tok/s

### Anima-LoRA-Factory（Linux Fork）
- **GitHub**：`github.com/robustonian/Anima-LoRA-Factory`
- **安裝**：`git clone && bash start.sh`
- **用途**：Anima 模型專用 LoRA 訓練工具，RTX 5090 上 25 張素材 10 epoch 約 25 分鐘完成

### Mini Tokyo 3D
- **GitHub**：（透過 @wsl8297 貼文可找到）
- **安裝**：Web 應用，可直接瀏覽器使用
- **用途**：東京公共交通 3D 即時視覺化，支援地鐵地下模式與班次追蹤

### MotionPNGTuber_Player
- **GitHub**：`github.com/rotejin/MotionPNGTuber_Player`
- **安裝**：git clone，瀏覽器版可直接使用
- **用途**：支援多資產切換的 PNGTuber 播放器

### Fun-ASR1.5
- **HuggingFace**：`FunASR/SenseVoiceSmall`（系列）
- **安裝**：`pip install funasr`
- **用途**：多語言語音辨識，支援語言切換與生產用文字輸出，Tongyi Lab 出品

### StepAudio 2.5 TTS
- **API**：Step Fun Pay-as-you-go API
- **用途**：自然語言控制語音情緒、節奏、停頓，支援 zero-shot 聲音克隆，無需特殊標籤

### Irodori-TTS LoRA
- **下載**：`note.com/852wa/n/n7a4955dc6754`（免費）
- **HuggingFace**：`Aratako/Irodori-TTS-500M-v2`
- **用途**：Irodori TTS 的 LoRA 擴充，可在本地語音合成流程中使用

### ComfyUI-SAM3DBody_utills
- **用途**：從單張圖像提取人體姿態，支援身形全客製化（身高/體型滑桿控制），輸出 Unity/UE 相容的 Rigged FBX

### html-in-canvas
- **GitHub**：`github.com/fimbox/html-in-canvas`（plugins/html-cloth.mjs）
- **用途**：在 WebGL Canvas 中直接渲染 HTML 作為 3D 材質，Chrome 實驗性功能

### llama.cpp --ai-tune
- **GitHub**：`github.com/DJLougen/llama.cpp`（含 patch）
- **安裝**：標準 llama.cpp 構建，加上 `--ai-tune` 參數
- **用途**：AI 自動最佳化 llama.cpp 推理參數，無需手動調整即可提速 50%+

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| ComfyUI-NAG-Extended | ComfyUI 節點 | git clone → custom_nodes | Flux Klein/Anima NAG 支援 | @SlipperyGem |
| Comfy Canvas v1.0 | ComfyUI 節點 | git clone → custom_nodes | 內嵌影像編輯器 | @Zlata_Salyukova |
| n8ao-webgpu | JS 函式庫 | npm / 直接引入 | WebGPU Ambient Occlusion | @AndrewFlowersMD |
| AI4AnimationPy | Python/Web | git clone + pip | AI 步態動畫控制 | @ps_starke |
| vLLM v0.19.1 | Python 套件 | pip install vllm | LLM 推理伺服器 | @vllm_project |
| lucebox-hub | C++ 工具 | git clone + 手動編譯 | 高速 LLM 推理（207 tok/s） | @pupposandro |
| Anima-LoRA-Factory | Shell 腳本 | bash start.sh | Anima LoRA 訓練工具 | @gosrum |
| Mini Tokyo 3D | Web App | 瀏覽器直接使用 | 東京交通 3D 即時視覺化 | @wsl8297 |
| MotionPNGTuber_Player | Web App | git clone | PNGTuber 多資產播放器 | @rotejin |
| Fun-ASR1.5 | Python 套件 | pip install funasr | 多語言語音辨識 | @Ali_TongyiLab |
| StepAudio 2.5 TTS | 雲端 API | Pay-as-you-go | 自然語言控制語音合成 | @StepFun_ai |
| Irodori-TTS LoRA | LoRA 權重 | 免費下載（note.com） | 本地 TTS LoRA 擴充 | @8co28 |
| ComfyUI-SAM3DBody | ComfyUI 節點 | git clone → custom_nodes | 單圖人體姿態 → FBX | （原作者帳號） |
| html-in-canvas | JS 插件 | git clone（Chrome 實驗性） | HTML 渲染為 3D 材質 | @tokufxug |
| llama.cpp --ai-tune | CLI 工具 | 標準 llama.cpp 構建 | AI 自動推理參數最佳化 | @ai_hakase_ |
| Qwen3.6-27B-TQ3_4S | HF 模型 | HuggingFace 下載 | 單張 3090 本地推理 27B | @coffeecup2020 |
| Wan2.2 INT4 AutoRound | HF 模型 | HuggingFace 下載 | 量化版影像生成模型 | @HaihaoShen |
| HY-World-2.0 | HF Spaces | 瀏覽器直接使用 | 3D Gaussian Splatting 重建 | @prithivMLmods |
| Animateur | Web App | 公開 beta | Vibe 動畫編輯器，可從 YT 提取動作 | @OP__Nico |
| Arrow 1.1 ComfyUI | ComfyUI Partner Node | ComfyUI Manager | 可編輯 SVG 向量圖生成 | （QuiverAI） |

---

## 🎯 本週趨勢

### 1. 「一張消費級顯卡跑大模型」成為主流敘事
Qwen3.6-27B 在單張 RTX 3090 達到 85 TPS，lucebox-hub 在同一張卡上達到 207 tok/s，Carlo 用 RTX 3060 12GB 跑 Qwen3.6-35B MoE——本週大量案例表明，本地推理的效能門檻正在快速下降，過去需要多卡機器的工作流現在正在被單張中階消費級 GPU 取代。

### 2. GPT Image 2 正在重塑創意生產流程
不只是圖像生成，GPT Image 2 本週被玩家用在：透明背景 logo 設計、角色設定資料自動化、全景圖生成、LINEスタンプ批次生產、漫畫→動畫 pipeline。提示詞工程社群已形成大量可複用模板，「一行提示詞完成複雜視覺任務」的工作流正在快速標準化。

### 3. ComfyUI 向「全功能影像工作室」演進
本週 ComfyUI 新增 SUPIR 官方支援、Comfy Canvas 內嵌編輯器、Refocus IC LoRA、NAG-Extended、Flux2Klein Ksampler 色彩修正、360° Panorama Stickers 更新……ComfyUI 已不只是生成工具，而是一個完整的影像製作環境，每週的節點更新速度仍在加快。

### 4. 語音合成進入「描述即控制」時代
StepAudio 2.5 TTS 允許用自然語言描述語音的情緒、停頓、語速，無需任何特殊標籤。搭配本週出現的 Irodori TTS LoRA 等本地化工具，語音合成正從「填參數」走向「描述你要的聲音」，與圖像生成的提示詞化趨勢高度一致。
