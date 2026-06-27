# OpenCode 週報 — 2026.06.26

> 本週兩條主線：**GLM-5.2 把「前沿級開源模型跑在自家 Mac」變成現實**，以及 **Krea 2 開源後一週內長出完整生態**。其餘亮點集中在「本地化 / 去 Python 依賴」的工具鏈，以及影片生成的一致性控制終於補齊。

---

## 🔥 本週焦點

### 1. GLM-5.2 — 開源模型逼近閉源前沿，且真的能在家跑
Z.ai 發布 **GLM-5.2**（frontier intelligence、open weights、1M context、兩段 reasoning effort）。社群評價是「自 DeepSeek-R1 以來開源最大新聞」，效能對標 GPT-5.5 / Opus 4.8，成本約低 70-80%。

真正的看點不在發布本身，而在**社群一週內把它壓進消費級硬體**：
- **Unsloth GGUF**：2-bit 從 1.51TB 壓到 238GB（保留約 82% 精度），256GB Mac Studio M3 Ultra 約 21.6 tok/s；甚至有 1-bit 版本。
- **0xSero REAP 剪枝**：504B / 469B / 180B 多個版本，4× RTX PRO 6000（SM120）可服務，剪枝同時把 looping 問題降低 33%。
- **MLX 分佈式**：兩台 M3 Ultra 512GB 跑到 23.4 tok/s（單台 18.8）。Han Xiao 單台 M3 Ultra 煙霧測試 15 tps（記得 `sudo sysctl iogpu.wired_limit_mb=512000`）。
- **Anemll Flash-MoE-SSD** 已支援 GLM-5.2 sidecar，往 SSD offload 方向走。
- Baseten（Philip Kiely）端側做到 **280+ tok/s**，比 RTX 6000 快 4 倍（Nvidia Dynamo + 自訂 NVFP4 + 改良 MTP）。

> 連結：`huggingface.co/unsloth/GLM-5.2-GGUF`｜`github.com/0xSero/glm-5.2-sm120`

### 2. Krea 2 — 開源圖像模型，48 小時長出完整生態
Krea 開源 **Krea 2 Raw + Turbo**（12B，約等同 Flux.1 體量）。核心設計是 **「Raw 訓練、Turbo 生成」**：Raw 是未蒸餾的 mid-training 權重供 fine-tune，Turbo 是快速蒸餾版。RTX 5090 跑 NVFP4 Turbo，1280×1920 約 15 秒；最高可到 3000×3000 無明顯 artifact（2K 是甜蜜點）。

生態擴張速度是本週最誇張的：
- **Day-0 diffusers + LoRA 訓練/推理支援**；ComfyUI 官方模板（社群嫌官方 workflow 過度複雜，nomadoor 出了精簡版）。
- 一週內 LoRA 收藏成形：neondrip / vintagetarot / sunsetblur / darkbrush…
- **Ostris Turbo 訓練 adapter**：可直接在 Turbo 上訓 LoRA 而不破壞蒸餾（與他的 Z-Image turbo adapter 同機制）。
- 周邊已出現：VAE 替換（`qwen_image_clear_vae`）、filter bypass LoRA、256dim distill LoRA、GGUF 量化（realrebelai，已修 layer 問題並提升量化保真度）。

> 連結：`github.com/krea-ai/krea-2`｜`huggingface.co/Comfy-Org/Krea-2`｜技術報告 `krea.ai/blog/krea-2-technical-report`

### 3. Sakana Fugu vs. OpenFugu — multi-agent 一夜被逆向開源
Sakana AI 發布 **Fugu**：把多 agent 協作系統包成單一模型 API，`Fugu Ultra` 宣稱對標 Fable / Mythos，賣點是「不受出口管制」。重點是它本質上不是一個模型，而是 **一個 policy over models**——一個極小的協調器按 query 路由到不同模型。

社群當天就點破並逆向：**OpenFugu**（trotsky1997）是 Sakana Fugu 的可運行重現，作者實測 <9B 的小模型當 sub-agent 也能 work。這是本週「研究發布 → 社群當天開源化」最戲劇性的一例。

> 連結：`sakana.ai/fugu`｜`github.com/trotsky1997/openfugu`

### 4. 影片生成補齊「一致性控制」這塊拼圖
過去一年影片生成最大痛點是跨鏡頭一致性，本週多管齊下：
- **LTX 2.3 IC-LoRA Ingredients**：一張 Reference Sheet 打包角色 / 道具 / 場景，雙角色不串色、道具環境鎖定；另有 In/Outpainting IC-LoRA。社群評「一年前要 VACE 整個模型才能做的事，現在一個 IC-LoRA 就更好」。fal 已上線。
- **Bernini I2V**：多參考圖跨鏡頭一致性 workflow（含 audio）。
- **Mankyu / NOBU 的 mannequin 流派**：用 Gemini Omni 把參考影片「假人化」再丟給 Seedance 2 做 motion reference，避免風格細節被過度模仿——本週被多人引用學習。
- **Mickmumpitz**：在 ComfyUI + LTX 用本地 AI 渲染整部 CG 短片（熊經營的汽車旅館），附完整免費 workflow + 教學。

> 連結：`huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-Ingredients`

### 5. 一條暗線：模型推理正在「去 Python / 去框架」
本週好幾個工具不約而同走純 C++/ggml 路線，意義是部署門檻與依賴地獄被進一步拆掉：
- **Depth Anything 3** 跑成純 C++/ggml 單一 GGUF，無 Python / PyTorch / CUDA toolkit，CPU 上**比 PyTorch 還快**，GPU 打平。
- **Meta SAM 3** 透過 Apple Core AI 完全在裝置端跑（open-vocabulary 分割），附 iOS/macOS app。
- **privacy-filter.cpp**：OpenAI 隱私 NER 模型架構的 C++ 重實作。

---

## 🛠️ 工具清單

### LLM / 推理
| 工具 | 連結 | 用途 |
|------|------|------|
| GLM-5.2 GGUF | `huggingface.co/unsloth/GLM-5.2-GGUF` | 前沿級開源 LLM，本機可跑 |
| GLM-5.2 REAP (SM120) | `github.com/0xSero/glm-5.2-sm120` | 剪枝版，4× RTX PRO 6000 服務 |
| Anemll Flash-MoE-SSD | `github.com/Anemll/anemll-flash-llama.cpp` | GLM-5.2 往 SSD offload |
| LFM2.5-230M | `huggingface.co/LiquidAI/LFM2.5-230M` | 230M 邊緣模型，19T tokens / 32K ctx |
| Qwen3.6-35B-A3B-NVFP4-vLLM | `github.com/MiaAI-Lab/...` | 自架 vLLM 推理 repo |
| OpenFugu | `github.com/trotsky1997/openfugu` | multi-agent 協調器（Fugu 開源重現） |

### 圖像生成
| 工具 | 連結 | 用途 |
|------|------|------|
| Krea 2 (ComfyUI) | `huggingface.co/Comfy-Org/Krea-2` | 12B 開源圖像，Raw/Turbo |
| Krea 2 GGUF | `huggingface.co/realrebelai/KREA-2_GGUFs` | 量化版（已修 layer） |
| Krea2 Turbo 訓練 adapter | `huggingface.co/ostris/krea2_turbo_training_adapter` | Turbo 直訓 LoRA 不破蒸餾 |
| Boogu-Image GGUFs | `huggingface.co/realrebelai/Boogu-Image-Turbo_GGUFs` | Apache 授權新圖像模型 |
| one-node-flux-2-klein | `github.com/yanokusnir-ai/one-node-flux-2-klein` | ComfyUI 單節點 FLUX.2 全功能 |
| i1 (text-to-image) | 學界全開放 recipe（公開資料集） | 小團隊可重現的 T2I 配方 |
| Musubi Tuner | `github.com/kohya-ss/musubi-tuner` | 新增 Ideogram 4 訓練支援 |

### 影片 / 3D
| 工具 | 連結 | 用途 |
|------|------|------|
| LTX 2.3 IC-LoRA Ingredients | `huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-Ingredients` | Reference Sheet 一致性影片 |
| LTX 2.3 In/Outpainting LoRA | `huggingface.co/Lightricks/LTX-2.3-22b-IC-LoRA-In-Outpainting` | 影片內外補繪 |
| QuickMaker (Blender) | `modelinghappy.com/archives/69177` | 單圖生 3D + 分鏡 + 動畫 |
| 3DGS → UE5 pipeline | @Stefan_3D_AI（教學） | 本地生成 Gaussian Splat 並在 UE5 渲染 |
| KIRI Guided Denoising | `github.com/bldeng/GuidedDenoising` | 老牌 mesh 法線濾波，快又銳利 |

### 開發 / CLI / 演算法
| 工具 | 連結 | 用途 |
|------|------|------|
| Depth Anything 3 (ggml) | C++/ggml 單一 GGUF | 深度估測，CPU 比 PyTorch 快 |
| SAM 3 on-device | `huggingface.co/mlboydaisuke/sam3-CoreAI-official` | iOS/macOS 離線分割 |
| Flash-KMeans | `github.com/svg-project/flash-kmeans` | IO-aware 精確 KMeans，比 cuML/FAISS 快 200x |
| Gorest | `github.com/NO6KIKO/gorest-2d-animation-spritesheet-generator` | 瀏覽器內本地 2D spritesheet 生成 |
| privacy-filter.cpp | `github.com/localai-org/privacy-filter.cpp` | OpenAI NER 隱私過濾 C++ 重實作 |
| Anthropic frontend-design skill | `github.com/anthropics/skills` | UI 設計 2-Pass 框架（本週大更新） |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得 | 主要用途 | 來源 |
|------|------|------|----------|------|
| GLM-5.2 | 開源 LLM | GGUF / API | 推理 · coding · agent · 1M ctx | Z.ai / Unsloth |
| Krea 2 Raw/Turbo | 圖像生成 | HF / ComfyUI | T2I、fine-tune | Krea |
| OpenFugu | Multi-agent | GitHub | 多模型協調 | trotsky1997 |
| LTX 2.3 IC-LoRA | 影片生成 | HF / fal | 跨鏡頭一致性 | Lightricks |
| LFM2.5-230M | 輕量 LLM | HF | 手機/機器人邊緣推理 | Liquid AI |
| Depth Anything 3 | 深度估測 | GGUF | 純 C++ 推理 | localai-org |
| SAM 3 CoreAI | 分割 | HF | iOS 離線分割 | mlboydaisuke |
| Flash-KMeans | 演算法 | GitHub | GPU 聚類 200x | svg-project |
| Boogu-Image | 圖像生成 | HF GGUF | Apache 授權替代模型 | realrebelai |
| Gorest | 2D 動畫 | GitHub | 本地 spritesheet | NO6KIKO |
| QuickMaker | Blender addon | 官網 | 圖生 3D + 動畫 | ModelingHappy |

---

## 🎯 本週趨勢

1. **「開源前沿模型 + 量化魔法」已成固定流程**：GLM-5.2 一發布，48 小時內 GGUF / NVFP4 / REAP 剪枝 / MLX 分佈式 / SSD offload 全套就位。重點不再是「開源模型多強」，而是**社群把它塞進你手上硬體的速度有多快**——這條供應鏈本身比任何單一模型都更值得關注。

2. **發布即被逆向開源化**：Sakana Fugu 當天被 OpenFugu 重現、Google Workspace CLI 作者被開除後社群繼續養（gogcli.sh）。閉源/受控發布想靠「藏架構」建護城河越來越難，真正的稀缺資源是權重與資料，不是 orchestration 的點子。

3. **推理層正在拋棄 Python 框架**：Depth Anything 3、SAM 3、privacy-filter.cpp 都走純 C++/ggml/CoreAI，且效能不輸甚至超越 PyTorch。對「想把模型塞進 app / 邊緣裝置」的開發者，部署複雜度正在系統性下降。

4. **影片生成的競爭點從「畫質」轉向「可控一致性」**：Midjourney 在畫質上仍被推崇，但本週的話題全在 LTX IC-LoRA references、Bernini 多參考圖、mannequin → Seedance motion 等**控制方法**上。誰能讓角色/道具跨鏡頭穩定，誰就贏下一輪，而且 6GB VRAM 就能玩。

5. **「圍繞單一開源模型的 LoRA 經濟」再次驗證**：繼 Flux、Anima 之後，Krea 2 一週內 LoRA / VAE / adapter / GGUF / workflow 全面開花。基礎模型一旦開源且可訓，社群會在數天內把生態補滿——這對選型有實際意義：押注「開源 + 可 fine-tune」的模型，工具鏈紅利幾乎是保證的。
