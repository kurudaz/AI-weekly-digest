# OpenCode 週報 - 2026.08.21

> 本週書籤被單一事件徹底主導：**MiniMax-H3 權重公開**。約七成書籤直接或間接圍繞它，社群在不到一週內完成了「量化 → 加速 → 節點 → LoRA 訓練 → 成本核算」的完整閉環。以下焦點依主線重要性排序。

---

## 🔥 本週焦點

### 1. MiniMax-H3 開源後，社群一週把生成時間砍到 1/3

MiniMax 釋出 [MiniMaxAI/MiniMax-H3](https://huggingface.co/MiniMaxAI/MiniMax-H3) 權重，ComfyUI **day-0 原生支援**（[blog.comfy.org 公告](https://blog.comfy.org/p/minimax-h3-day-0-support-in-comfyui)），Comfy-Org 同步上架預量化權重 [Comfy-Org/MiniMax-H3](https://huggingface.co/Comfy-Org/MiniMax-H3)（關鍵檔案 `minimax_h3_ref2va_pruned_int8_convrot.safetensors`）。真正的故事是接下來這週的加速競賽：

| 手段 | 實測數字 | 硬體 |
|---|---|---|
| EasyCache（kijai 提出） | 約 **1.67×**，劣化不明顯 | — |
| EasyCache + SageAttention | 1M 像素 5 秒：**6 分 → 2 分半** | — |
| SageAttention 2.2.0 | 單段 **425.6s → 322.0s**（-24.3%，首輪峰值 44%）；連跑 13 段省約 22 分鐘 | RTX 4090 48G |
| Sage 2.2 + Sol-Attn | 較無優化 **-34.3%**，畫質掉幅小但背景形狀略崩 | RTX 5090 |
| Video VAE 換 `int8_convrot`（原 fp16） | **76s → 71s**（約 -7%） | — |
| Turbo LoRA（8 steps） | 1280×736 / 10s：**272s → 160s** | — |
| 全套疊加 | **20 steps 160s → 8 steps 71s** | — |
| ComfyUI-Spectrum-MiniMax-H3 | 6 分 03 秒 → **4 分 44 秒** | RTX 4060Ti 16GB |

Turbo LoRA 的細節很重要：[larryvrh/MiniMax-H3-Turbo-Lora](https://huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora) **只在 8 steps 有效，4–7 steps 會壞**，且 diffusion model 與 text encoder 都必須是 `int8_convrot`；原始權重的 LoRA 格式不吃 ComfyUI，需用 repo 內「For ComfyUI users」的轉換腳本，或直接抓社群轉好的 [drbaph/MiniMax-H3-Turbo-Lora-ComfyUI](https://huggingface.co/drbaph/MiniMax-H3-Turbo-Lora-ComfyUI)、[QrusherZA/H3_Turbo_ComfyUI](https://huggingface.co/QrusherZA/H3_Turbo_ComfyUI)。

底層也在被挖：Kohya 發現 **TokenRefiner 不依賴 timestep**，可在 denoising loop 前預算完再 offload，速度幾乎不變但 **省約 1.6GB VRAM**；另有人解出 H3 詭異的 **17n+5 幀數限制**來源——VAE encode 以 17 幀為單位（encoder 把 pixel 17F 壓成 latent 5F，decoder 把 latent 1F 展成 pixel 4F），意味著**長片段的 VRAM 佔用是恆定的**。ComfyUI 端也合併了 latent noise mask 取樣的 bug fix（PR #15322 / commit `563b98e`）。

---

### 2. 從 68GB 到 6GB：量化把 H3 推進消費級顯卡

原生跑法有多重？有人在 **RTX PRO 6000B** 上以 bf16 生成 7 秒 1376×768（1M 像素）花 **333 秒、佔用 68GB VRAM**；完整 Ref2VA 鏈的權重合計 **42.47GB**（H3 Ref2VA INT8 ConvRot 20.97GB + Qwen3-VL-32B NVFP4-AWQ 15.69GB + Video VAE FP16 5.21GB + Audio VAE FP32 0.61GB，跑在 ComfyUI 0.30.0）。

社群一週內把門檻壓垮：

- **Kijai** 的 [MiniMax-H3-experimental](https://huggingface.co/Kijai/MiniMax-H3-experimental)：權重壓到 **4-bit、運算走 8-bit**，用 `AsymW4A8Int8Layout` 佈局兼顧體積與精度；同 repo 也放了那個關鍵的 `minimax_h3_video_vae_int8_convrot.safetensors`。
- **cheaty** 的 [lilcheaty/MiniMax-H3-NVFP4](https://huggingface.co/lilcheaty/MiniMax-H3-NVFP4)：full 與 pruned 兩種變體、text encoder、全部 VAE 一次備齊，在 RTX Pro 6000 上端到端驗證過。
- **12–24GB 使用者的懶人包**：[Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot](https://huggingface.co/Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot) 蒐羅 INT4 / INT8 / Mixed / NVFP4。
- **[DmitryDB/MiniMax-H3-INT8-Lean-ConvRot](https://huggingface.co/DmitryDB/MiniMax-H3-INT8-Lean-ConvRot)**：在 24GB 上限內盡量讓更多層保持 BF16 換精度，代價是速度略降。
- **[WanGP v12.41](https://github.com/deepbeepmeep/Wan2GP)**（deepbeepmeep）：5 秒（124 幀）**只吃 5–6GB VRAM**，832×480 的 15 秒也只要 8–9GB。

實際跑起來的下限報告：**RTX 3060 6GB + 64GB RAM**，480p 5 秒 T2V 約 **4–5 分鐘**（EasyCache + SageAttention）；**RTX 4090 24GB** 可一口氣生成 15 秒帶音訊影片，且觸到訓練上限 **362 幀 = 15.08 秒**，耗時 9.6 分——但同一人一開始 5 秒要花 496 秒，關鍵在**「torch 必須是 cu130，cu128 會走不到 comfy_kitchen 的快路」**這類環境設定。

> 💡 選型提醒：`int8_convrot` 已經事實上成為 H3 生態的公分母——Turbo LoRA、Video VAE、text encoder 全部要求它。挑權重時請把**整條格式相容鏈**一起看，別只挑檔案最小的。

---

### 3. ComfyUI 從「節點圖」變成「導演台」

H3 原生支援 **CUT1 / CUT2 分鏡指令**與音畫同步生成，這件事直接改寫了工具形態——社群補上的不是節點，是**工作流層級的抽象**：

- **[ComfyUI_MiniMaxH3_Director](https://github.com/huangserva/ComfyUI_MiniMaxH3_Director)** / **[ComfyUI-MiniMaxH3-Director](https://github.com/seesee75-commits/ComfyUI-MiniMaxH3-Director)**：時間軸編輯器，拖進 ComfyUI 即涵蓋文生影、圖生影、首尾幀、影片換人。作者的一句話點題：「**模型是官方的，能直接開拍的流程是社群補的——這才是開源真正發生的地方**。」
- **多鏡頭串接**：[jlucasmcrell/ComfyUI-H3-Multishot](https://github.com/jlucasmcrell/ComfyUI-H3-Multishot)、[joeygambino/MiniMax-H3-Multishot-Workflow](https://huggingface.co/joeygambino/MiniMax-H3-Multishot-Workflow)，單節點串出 30 秒連續帶音影片。
- **「先糊後清」二段式**：huangserva 的 latent upscale 節點，先低解析生成，再把 **864×480 放大到 1296×720（1.5×）或 1728×960（2×）**由 H3 自己補細節——重點是它能處理普通 latent upscale 節點吃不下的 **H3「影像＋音訊」聯合 latent**。
- **把影片模型當圖像編輯器用**：とりにく 的 [ComfyUI-MiniMaxH3-SingleFrame](https://github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame)——利用最小 5 幀限制，只生成 5 幀取其中 1 張當編輯結果；另一種玩法是給首尾幀、生 22 幀後抽第 11 幀。同時 [musubi-tuner 已出現 H3 影像編輯任務的訓練程式碼](https://github.com/kohya-ss/musubi-tuner/pull/1018)（最短 5 幀訓練，音訊輸入餵 dummy）。
- 其他：[Burgstall-labs/ComfyUI-Seamless-Equirectangular](https://github.com/Burgstall-labs/ComfyUI-Seamless-Equirectangular)（360° 全景無縫）、[maDcaDDie2000/comfyui-video-tiler](https://github.com/maDcaDDie2000/comfyui-video-tiler)、[Kijai/MiniMax-H3-TAE](https://huggingface.co/Kijai/MiniMax-H3-TAE)（快速預覽用 TAE，需搭配 KJNodes 的 `ModelPreviewOverride`，效果勝過 latent2rgb）。

更值得注意的是**離開 GUI 的那一派**：有創作者本週整套流程「幾乎沒開 ComfyUI 畫面」——RunPod 有官方 MCP，於是**借 GPU → 生成 → 關機清理全交給 Claude Code 對話完成**；另一派用 [google-colab-cli](https://github.com/googlecolab/google-colab-cli) 讓 Codex / Claude 直接驅動 Colab 跑 H3，「深夜掛著跑，早上就有素材」。

---

### 4. 成本重算：本地 / Colab 比官方 API 便宜 4.5–8.6 倍

本週傳播最廣（37 萬觀看）的一則是這張成本表，以 **15 秒影片**為單位：

| 途徑 | 解析度 | 成本 | 倍率 |
|---|---|---|---|
| MiniMax H3 / Google Colab L4 | 約 720p | **€0.23** | 1.0× |
| MiniMax H3 官方 API | 768p | $1.20 ≈ €1.04 | 4.5× |
| Seedance 2.5 官方 API | 720p | ¥14.91 ≈ $2.28 ≈ €1.98 | 8.6× |

但同一串下面有一句非常清醒的反駁值得抄下來：「**價格對批次作業好看，但真正決定成本的是 accepted-shot rate——如果便宜的生成大多被丟掉，那就不便宜。**」這正好對上另一批實測：多輪 T2V/I2V 評測中 **Seedance 2.0 綜合最好、happyhorse 1.1 第二、MiniMax H3 第三**，差距集中在表演細膩度、夜景氛圍與關鍵細節完成度；且 H3 有自己的脾氣——多位創作者發現**提示詞寫太細反而崩**（「適用於即夢的寫法未必適用 H3，要留想像空間」），以及 **768p 的動作穩定度平均優於 2K**（尤其線稿部分的溶解）。

同一週 Higgsfield 把 95 分鐘 AI 長片《Hell Grind》**完整開源**（製作費 $500,000、坎城市場放映、WSJ / Variety / BBC 報導），**所有 prompts 與 assets 全公開**——比起片子本身，那份製作 Brief 的三個要點更該讀：角色一致性不只靠角色表、空間一致性要準備 GEO SPATIAL LAYOUT、演技要寫「目的 / 障礙 / 身體反應」。

---

### 5. 本地 LLM：DeepSeek-V4-Flash 與 Kimi K3 的量化 / abliterated 潮

影片之外的另一條主線。**[deepseek-ai/DeepSeek-V4-Flash-0731](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731)** 官方權重釋出後，量化與去審查版本在 **24 小時內**鋪滿 HF：[unsloth GGUF](https://huggingface.co/unsloth/DeepSeek-V4-Flash-0731-GGUF)、[huihui-ai abliterated GGUF (Q2_K)](https://huggingface.co/huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF)（同時支援 llama.cpp 與 ds4，且**所有 expert 模組未被 ablate**）、[heath0xFF REAP GGUF](https://huggingface.co/heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF)、[apetersson Abliterated-DS4-Headroom128](https://huggingface.co/apetersson/DeepSeek-V4-Flash-0731-Abliterated-DS4-Headroom128)（實測「無審查版對 base **無劣化**」）、[Rednalreden dwarfstar-q2-gguf](https://huggingface.co/Rednalreden/DeepSeek-V4-Flash-0731-dwarfstar-q2-gguf)（在既有引擎上預期 **49 TPS**），連 antirez 也放了 [antirez/deepseek-v4-gguf](https://huggingface.co/antirez/deepseek-v4-gguf)。**Kimi K3** 同步出現 [Uniboshi/Kimi-K3-Abliterated-V1](https://huggingface.co/Uniboshi/Kimi-K3-Abliterated-V1) 與 [Q2_K GGUF](https://huggingface.co/GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF)。

---

## 🛠️ 工具清單

### MiniMax-H3 權重與量化

| 工具 | 連結 | 用途 |
|---|---|---|
| MiniMax-H3（官方） | `huggingface.co/MiniMaxAI/MiniMax-H3` | 原始權重 |
| Comfy-Org/MiniMax-H3 | `huggingface.co/Comfy-Org/MiniMax-H3` | ComfyUI 官方預量化包（int8_convrot pruned） |
| Kijai/MiniMax-H3-experimental | `huggingface.co/Kijai/MiniMax-H3-experimental` | W4A8 量化、fl2va pruned、int8_convrot Video VAE |
| lilcheaty/MiniMax-H3-NVFP4 | `huggingface.co/lilcheaty/MiniMax-H3-NVFP4` | NVFP4 全套（full/pruned + TE + VAE） |
| Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot | `huggingface.co/Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot` | 12–24GB VRAM 量化合輯 |
| DmitryDB/MiniMax-H3-INT8-Lean-ConvRot | `huggingface.co/DmitryDB/MiniMax-H3-INT8-Lean-ConvRot` | 24GB 內保留更多 BF16 換精度 |
| MiniMax H3 INT4 ConvRot | HF（更新版已釋出） | 12GB VRAM 級門檻 |
| ethanfel/Qwen3-VL-32B-Ultra-Heretic-…-INT8-ConvRot | `huggingface.co/ethanfel/Qwen3-VL-32B-Ultra-Heretic-MiniMax-H3-ComfyUI-INT8-ConvRot` | 去限制版 text encoder |

### 加速（Attention / Cache / LoRA）

| 工具 | 連結 | 用途 |
|---|---|---|
| MiniMax-H3-Turbo-Lora | `huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora` | 4–8 step 蒸餾 LoRA（實務 8 steps） |
| Turbo LoRA ComfyUI 轉換版 | `huggingface.co/drbaph/MiniMax-H3-Turbo-Lora-ComfyUI`、`huggingface.co/QrusherZA/H3_Turbo_ComfyUI` | 免手動轉格式 |
| ComfyUI-sol-attn | `github.com/Saganaki22/ComfyUI-sol-attn` | Blackwell/40·50 系 int8 attention，峰值記憶體 -37% |
| ComfyUI_sol-attn_Blackwell | `github.com/KingGore/ComfyUI_sol-attn_Blackwell` | 5090 專用（作者稱優於 EasyCache） |
| ComfyUI-SolAttn_triton | `github.com/kijai/ComfyUI-SolAttn_triton` | Kijai 版 Patch Sol Attention |
| comfyui-minimax-h3-blockcache-T8 | `github.com/T8mars/comfyui-minimax-h3-blockcache-T8` | 速度接近 EasyCache、劣化更小 |
| ComfyUI-Spectrum-MiniMax-H3 | 見 aiaicreate.com 教學 | 加一個節點即加速 |
| Kijai/MiniMax-H3-TAE | `huggingface.co/Kijai/MiniMax-H3-TAE` | 快速預覽 VAE（需 KJNodes） |

### ComfyUI 節點與工作流

| 工具 | 連結 | 用途 |
|---|---|---|
| ComfyUI_MiniMaxH3_Director | `github.com/huangserva/ComfyUI_MiniMaxH3_Director` | 導演台工作流：T2V/I2V/首尾幀/換人 |
| ComfyUI-MiniMaxH3-Director | `github.com/seesee75-commits/ComfyUI-MiniMaxH3-Director` | H3 時間軸編輯器 |
| ComfyUI-H3-Multishot | `github.com/jlucasmcrell/ComfyUI-H3-Multishot` | 多鏡頭串接 |
| MiniMax-H3-Multishot-Workflow | `huggingface.co/joeygambino/MiniMax-H3-Multishot-Workflow` | 單節點串成 30 秒帶音影片 |
| ComfyUI-MiniMaxH3-SingleFrame | `github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame` | 用 H3 做單張影像編輯 |
| ComfyUI-Seamless-Equirectangular | `github.com/Burgstall-labs/ComfyUI-Seamless-Equirectangular` | 360° 無縫全景 |
| comfyui-video-tiler | `github.com/maDcaDDie2000/comfyui-video-tiler` | 影片分塊處理 |
| minimax-h3-hybrid-cond | `github.com/kitsune123150/minimax-h3-hybrid-cond` | 混合條件輸入 |
| ComfyUI-Fantastic-MiniMaxH3-PromptBuilder | GitHub（原文未附連結） | 提示詞編輯 + 參考素材管理 |
| MiniMax-H3 Tools | GitHub（原文未附連結） | 8 項補強功能、支援 NF4 試驗性載入 |
| awesome-minimax-h3-prompts | `github.com/BeatAPI/awesome-minimax-h3-prompts` | H3 提示詞精選集 |
| Stable Audio 3 + ACE-Step 1.5 XL 工作流 | ComfyUI | 由短動機自動生成完整純音樂 |

### 其他模型 / LoRA

| 工具 | 連結 | 用途 |
|---|---|---|
| InfiniSplat（SIGGRAPH Asia 2026） | `huggingface.co/spaces/PLUS-WAVE/InfiniSplat`、`zju3dv.github.io/InfiniSplat` | 單張圖 **< 1 秒**生成可導覽 3DGS 場景，室內品質勝 SHARP |
| Krea 2-Pose ControlNet | `huggingface.co/thedeoxen/Krea-2-pose-controlnet`、Space: `hugging-apps/krea2-pose-controlnet` | 照片→骨架→換角色同姿勢，也可手繪骨架 |
| lodestones/Kroma | `huggingface.co/lodestones/Kroma` | Krea 2 × Chroma 融合 LoRA，rank 256 / alpha 256，含完整微調 normalization |
| Muscriptor（Mirelo × Kyutai） | Space: `hugging-apps/muscriptor-music-transcription` | 音訊 → **分樂器 MIDI 音軌**轉譯 |
| Irodori-TTS-v4-Small | `huggingface.co/Aratako/Irodori-TTS-v4-Small` | 600M 參數 TTS，text encoder 換為 SoftBank 版本，最長 120 秒 |
| Core ML Model Zoo（46 模型） | `huggingface.co/collections/mlboydaisuke/core-ml-model-zoo-…` | 深度/偵測/分割/摳像/超解析/VLM/TTS，解壓 `.mlpackage` 拖進 Xcode 即用 |
| clark-swin2sr-lightweight-x2-1.58bit | `huggingface.co/clark-labs/clark-swin2sr-lightweight-x2-1.58bit` | 1.58bit 超輕量 2× 超解析 |
| Anima ControlNet-LLLite v3 | kohya-ss PR（含 ComfyUI 節點與範例權重） | 語意編輯 semantic trunk；另有精準 BoldLoRA（-5～+5 調線條粗細） |
| ThisIsIllustrious v4.0 / animd-Krea2Turbo | Civitai（後者 `civitai.com/models/2784060`） | Illustrious 系插畫、Krea 2 Turbo 動漫化 |

### 開發者 CLI / Agent 工具

| 工具 | 連結 | 用途 |
|---|---|---|
| pdf-inspector | `github.com/firecrawl/pdf-inspector` | Rust 寫的本地 PDF 處理：**~20ms 分類、200 份 2.8 秒**，表格與圖表擷取品質佳，讓 agent 不必等 OCR |
| google-colab-cli | `github.com/googlecolab/google-colab-cli` | 從 Codex / Claude Code 驅動 Colab 跑生成（免費方案可行，付費更順） |
| Director Skills | GitHub（Agent Skills 格式） | 讓 Agent 像導演一樣串起腳本→生成→剪輯，支援 Claude Code / Codex |
| RunPod 官方 MCP | RunPod | 對話式借 GPU→生成→清理，全程不開 GUI |
| hbg-life-simulation | `github.com/Mr-funny/hbg-life-simulation` | 把 Codex 反代成生圖 API，突破其串行限制 → **10 路並發** |
| jungle-trail | `github.com/StarKnightt/jungle-trail` | 全程序化 Three.js 第一人稱叢林，零素材，附完整 prompt |
| USOMAP | `amix-design.com/asoboad/tools/d-usomap/` | 幻想世界地圖生成器，**不使用生成式 AI**，純地理行為模擬（水源附近生成聚落、自動生成國界地名） |
| SiroinoSotai | `siroinoworks.booth.pm/items/8268676` | VRChat 共用素體，PC △16,704 / Mobile △3,500，**CC0**、商用改變再散布皆可 |
| Blender VFX Toolkit 1.0 / easy-print | superhivemarket / cgbox.jp | 100+ VFX 資產與預設 / 3D 列印分割與自動接件 |

---

## 📊 快速參考卡

只列本週最可能直接派上用場的項目。

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| **Comfy-Org/MiniMax-H3** | 影片模型權重 | HF 下載 → `models/diffusion_models` | H3 入門首選，int8_convrot pruned，與整個生態相容 | Hugging Face |
| **MiniMax-H3-Turbo-Lora** | 蒸餾 LoRA | HF 下載（需 ComfyUI 轉換或抓轉好版） | 8 steps 提速約 2×；**4–7 steps 無效** | larryvrh / drbaph / QrusherZA |
| **Abiray 量化合輯** | 量化權重 | HF 下載 | 12–24GB VRAM 用戶一站式挑格式 | Hugging Face |
| **ComfyUI-sol-attn** | ComfyUI 節點 | git clone → custom_nodes | 40/50 系 int8 attention，峰值記憶體 -37% | GitHub |
| **EasyCache**（內建） | 取樣加速 | ComfyUI 原生節點 | 約 1.67×，搭 SageAttention 可到 6 分→2.5 分 | ComfyUI |
| **comfyui-minimax-h3-blockcache-T8** | ComfyUI 節點 | git clone | 想要 EasyCache 的速度但更少劣化時的替代 | GitHub |
| **ComfyUI_MiniMaxH3_Director** | 工作流 | 拖入 ComfyUI 即用 | T2V / I2V / 首尾幀 / 影片換人一次覆蓋 | GitHub |
| **ComfyUI-MiniMaxH3-SingleFrame** | ComfyUI 節點 | git clone | 把影片模型當圖像編輯器（生 5 幀取 1 張） | GitHub |
| **Kijai/MiniMax-H3-TAE** | 預覽 VAE | HF 下載 + KJNodes | 生成中即時預覽，遠勝 latent2rgb | Hugging Face |
| **WanGP v12.41** | 本地執行器 | GitHub 安裝 | **5–6GB VRAM** 跑 5 秒、8–9GB 跑 15 秒 832×480 | deepbeepmeep |
| **InfiniSplat** | 3DGS 模型 | HF Space 直接試 | 單圖 <1 秒生成可導覽 3D 場景 | HF Space / zju3dv |
| **Krea 2-Pose ControlNet** | ControlNet | HF Space / 下載權重 | 姿勢遷移，可跳過照片直接畫骨架 | thedeoxen |
| **Muscriptor** | 音訊模型 | HF Space | 錄音 → 可編輯的分樂器 MIDI | Mirelo × Kyutai |
| **pdf-inspector** | Rust CLI | `git clone` / cargo | Agent 用的本地 PDF → Markdown，200 份 2.8 秒 | firecrawl |
| **google-colab-cli** | 開發者 CLI | `pip` / GitHub | 從 Codex / Claude 驅動 Colab 跑 H3 | googlecolab |
| **DeepSeek-V4-Flash-0731 GGUF** | 本地 LLM | HF 下載 → llama.cpp | 本週最熱本地模型，量化/去審查版本齊全 | unsloth / huihui-ai / antirez 等 |
| **Hell Grind（開源長片）** | 素材 + 方法論 | higgsfield.ai | 95 分鐘、$500k 製作的全套 prompts 與 assets | Higgsfield |

---

## 🎯 本週趨勢

### 1. 「Day-0 生態」已成為開源模型的預設節奏——首日評測沒有參考價值

從權重公開到「量化 4 種格式 + 3 種 attention 加速 + 蒸餾 LoRA + 時間軸節點 + LoRA 訓練程式碼」全部齊備，這次只花了不到一週，而 VRAM 需求從 68GB 掉到 5–6GB、生成時間掉到約 1/3。**實際意義**：如果你在模型發布當天測完就下結論「跑不動 / 太慢」，那個結論一週後就作廢了。合理做法是發布日先確認**輸出品質上限**，等 7–10 天再評估**部署可行性**，兩者現在已經是完全脫鉤的兩個問題。

### 2. 量化格式正在從「壓縮技巧」變成「生態鎖定」

`int8_convrot` 這週的地位變化值得警惕：它不只是一種量化，而是 Turbo LoRA、Video VAE、text encoder、甚至第三方 GGUF 打包者都要對齊的介面。已經有 GGUF 作者公開說「確認了 int8 convrot，我們 GGUF 用戶接下來能吃得很好」——換句話說，**選錯量化分支，你會被整條加速鏈拋下**。同時 Kijai 的 `AsymW4A8Int8Layout`（4-bit 存、8-bit 算）指出了方向：純粹追求檔案小已經不是重點，**存算分離的混合精度**才是消費級顯卡的正解，而 DmitryDB 那種「24GB 內盡量塞 BF16 換精度」的反向操作也證明了這條軸上還有取捨空間。**部署建議**：先定 VRAM 上限，再選量化格式，最後才選加速節點——順序反了會反覆重下權重。

### 3. 生成成本的瓶頸已從 GPU 轉移到「可用鏡頭率」

Colab L4 比官方 API 便宜 4.5 倍、比 Seedance 2.5 API 便宜 8.6 倍，這個數字很誘人，但本週最有價值的一句評論是「accepted-shot rate matters more」。搭配另外兩則觀察就更清楚了：H3 在多輪盲測中排在 Seedance 2.0 與 happyhorse 1.1 之後（差在表演與細節完成度）；而且它對過度細緻的提示詞有反效果，多位創作者的結論是「**提示詞粗一點，留想像空間**」。**判斷**：如果你的用途是**大量素材、後期再挑**（B-roll、動態背景、MV 分鏡），本地 H3 的成本優勢是真的；如果需要**一次到位的關鍵鏡頭**，抽卡次數會把成本差距吃光，付費 API 反而划算。另一個被低估的變數是**環境設定**——同一張 4090，torch cu130 vs cu128 的差距是 496 秒 vs 9.6 分跑 3 倍長度的片段。

### 4. 音畫同步 + 原生分鏡，把 ComfyUI 的抽象層從「節點」推到「導演台」

H3 能用 `[Shot 3] At 00:04.000, the camera cuts to an extreme close-up of her eye` 這種指令精確控制剪點，而且 BGM 與環境音是與畫面**同時生成**而非後製貼上。這造成兩個結構性後果：其一，社群補的東西不再是單一功能節點，而是 **timeline editor、multishot chain、二段式 latent 重繪**這類流程級抽象；其二，也是更值得注意的——**GUI 開始被繞過**。本週有人靠 RunPod MCP + Claude Code 完成「借 GPU→生成→清理」全流程而幾乎沒開 ComfyUI 畫面，也有人把 Codex 反代成生圖 API 突破串行限制做到 10 路並發。**趨勢判斷**：ComfyUI 正在從「創作者的工作介面」變成「Agent 的執行後端」，接下來值得投資的是**能被 CLI/MCP 驅動的工作流描述格式**，而不是更漂亮的節點圖。

### 5. 提示詞正在資產化，而審美同質化的反噬同步到來

中文社群本週被「天宮 / 東方巨物美學」淹沒（單則最高 536 萬觀看），主流做法是 MJ 墊圖 + Seedance / Kling 生成，並演化出**矩陣式提示詞**——刻意不具象化場景，只給總意境（「近處有巨物，遠處有天境；靜處顯莊嚴，動處生仙氣」），再要求一次輸出 9 張互不重複的獨立成品，**主動利用模型隨機性當創意來源**。這是很成熟的提示詞工程思路。但同一週也出現了尖銳的反面聲音：批評這批作品是「宇宙級殯儀館——宏大、空洞，把權力秩序當成東方美學，只有一個等人朝拜的空殼」。也有人指出跑出圈的作品靠的不是場景宏大，而是構圖與色彩技巧。**實際意義**：當提示詞可以整包複製傳播，**提示詞本身就不再是護城河**；區隔會回到選片、節奏與構圖判斷——這也正是 Higgsfield 那份製作 Brief 強調「角色一致性不只靠角色表、演技要寫目的/障礙/身體反應」的原因。

### 6. Abliterated + GGUF 已成為開源 LLM 發布的標準尾隨動作

DeepSeek-V4-Flash-0731 與 Kimi K3 官方權重釋出後，去審查版與各式量化在 24 小時內鋪滿 HF，且品質論述越來越專業——不再是「解除了」而是「**32/32 全層、對 base 無劣化、所有 expert 模組未 ablate、與 llama.cpp 及 ds4 雙相容**」。搭配 DwarfStar 引擎 49 TPS 的預期值，本地推理的可用性門檻本週又降了一階。**選型意義**：評估本地 LLM 時，官方權重釋出當下的量化生態成熟度，現在幾乎和模型本身的 benchmark 同等重要。

---

## 📌 其他值得記一筆

- **Seedance 2.5**：支援單段 **30 秒一鏡到底**、最多 **30–50 個參考素材**（人物、道具、環境、姿態），日式 2D 動畫複現能力明顯提升；有人用約 20 張參考圖一次生出 25 秒預告片。
- **frame budget 冷知識**：H3 的訓練上限是 **362 幀 = 15.08 秒**，超過就不保證。
- **muse-spark-1.2**：生成 3D 都市場景**成本約 20 日圓**，輸出 Three.js 景觀 app，可自選角度截圖後再送 GPT-Image2 / H3 做素材化——比開 Blender MCP 更輕的前置流程。
- **EU AI Act**：AI 生成內容標示義務自 **8/2 起適用**；「違反罰 65 億日圓」的報導有明顯誤導成分，實際適用條件需另行確認。
- **Marcos Hernanz** 在跑完約 **60B tokens** 後公開了他的完整 `AGENTS.md`（195 萬觀看），值得對照自己的設定檔。

---

*註：部分連結由書籤中被截斷的 URL 依 repo 名還原；`ComfyUI-Fantastic-MiniMaxH3-PromptBuilder` 與 `MiniMax-H3 Tools` 原文僅提及名稱（「URL はリプ」），未附完整位址。*
