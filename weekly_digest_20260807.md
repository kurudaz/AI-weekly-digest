# OpenCode 週報 - 2026.08.07

> 本週書籤幾乎被一件事佔滿：**MiniMax-H3 開源權重落地**。以下焦點按主線串接，非逐條羅列。部分書籤的原文連結在抓取時被截斷，凡無法確認完整路徑者已在表格中標注。

---

## 🔥 本週焦點

### 1. MiniMax-H3 權重釋出 → 社群在數天內補完整條加速鏈

官方權重上 Hugging Face（`MiniMaxAI/MiniMax-H3`）的同一天，ComfyUI 就宣布 **day-0 原生支援**：T2V、I2V、首尾幀控制、Reference-to-Video（可帶入主體、動作或聲音）全部原生節點。Comfy-Org 同步放出量化權重（`minimax_h3_ref2va_pruned_int8_convrot.safetensors`）。

真正值得記錄的是接下來 72 小時發生的事——加速手段一層層疊上去，而且是**可累乘**的：

| 手段 | 實測 | 來源 |
|---|---|---|
| EasyCache（Kijai 提到的「裏技」） | 約 1.67× 加速，劣化不明顯 | @umiyuki_ai |
| SageAttention 2.2.0（RTX 4090 48G） | 單段 425.6s → 322.0s，**提速 24.3%（首輪峰值 44%）**；連跑 13 段省約 22 分鐘 | @servasyy_ai |
| Sage 2.2 + Sol-Attn（RTX 5090） | 比無加速狀態**縮短 34.3%**，畫質下降有限，但背景形狀略有崩壞 | @kiyoshi_shin |
| Sol-Attn Patches（Blackwell 專用） | 峰值記憶體**降 37%**，40/50 系列取得更快更準的 int8 attention | `Saganaki22/ComfyUI-sol-attn` |
| Video VAE fp16 → `int8_convrot` | 76s → 71s（約 7%），需 pull 最新 ComfyUI | @ITOKtw |
| **Turbo LoRA（4-8 step 蒸餾）** | 1280×736 / 10s：**272s → 160s**，8 steps（實測 4-7 step 不可用） | `larryvrh/MiniMax-H3-Turbo-Lora` |
| 全套疊加 | 20 steps 160s → **8 steps 71s** | @riddi0908 |
| EasyCache + SageAttention | 1M 像素 / 5s：**6 分 → 2 分半** | @Rjhy2020 |

Turbo LoRA 有個實務坑：原版權重的型別不符合 ComfyUI，需用 repo 內 `Community > For ComfyUI users` 的程式碼轉換，且 diffusion model 與 text encoder 都必須是 `int8_convrot` 版本。社群很快出了兩個免轉換的重打包：`QrusherZA/H3_Turbo_ComfyUI` 與 `drbaph/MiniMax-H3-Turbo-Lora-ComfyUI`（後者專門處理 pruned / curve-form checkpoint 的相容性）。

架構層面也有人在挖：Kohya 發現 **TokenRefiner 不依賴 timestep**，可以在 denoising loop 前預先算好，算完 offload 掉能省約 **1.6GB VRAM**；另有人釐清了「影格數必須是 17n+5」的謎題——因為 VAE encode 以 17 frame 為單位（pixel 17F → latent 5F，decode 時 latent 1F → pixel 4F），其餘靠 trim 與 crossfade 銜接，**這代表長片段的 VRAM 用量是恆定的**。ComfyUI 端也修掉了 latent noise mask sampling 的 bug（#15322，commit `563b98e`）。

---

### 2. VRAM 門檻從 68GB 打到 6GB：量化矩陣與「比 API 便宜 4.5 倍」的部署帳

同一週內出現了幾乎所有精度的權重版本，這條線的意義在於**選型可以按顯卡對號入座**：

- **原生 bf16**：RTX PRO 6000B 上 7 秒 1376×768（1M 像素）耗時 333 秒，**VRAM 用掉 68GB**
- **INT8 ConvRot**：目前的實務主力。完整 R2V 鏈權重合計 **42.47GB**（主模型 20.97GB + Qwen3-VL-32B NVFP4-AWQ encoder 15.69GB + Video VAE FP16 5.21GB + Audio VAE FP32 0.61GB）
- **W4A8（Kijai）**：`AsymW4A8Int8Layout` — 權重壓到 4-bit 省空間，運算走 8-bit 保速度與精度
- **NVFP4**（`lilcheaty/MiniMax-H3-NVFP4`，含 full / pruned / text encoder / 全部 VAE，RTX Pro 6000 端到端驗證過）
- **INT8-Lean-ConvRot**：在 24GB 內盡量讓更多層保留 BF16 換精度，代價是速度略降
- **INT4 ConvRot**：明確瞄準 12GB VRAM 等受限環境
- **WanGP v12.41**：極致優化路線，5 秒（124 frames）只吃 **5-6GB VRAM**，832×480 的 15 秒也只要 8-9GB

於是最低配的實證出現了：**RTX 3060 6GB + 64GB RAM，5 秒 480p T2V 約 4-5 分鐘**（EasyCache + SageAttention）。RTX 4090 24GB 則跑出**完整 15 秒帶音訊、達到模型訓練上限 362 frames（15.08 秒），耗時 9.6 分鐘**——注意該作者提到 torch **必須用 cu130**，cu128 會撞上 `comfy_kitchen` 問題，且優化前同樣環境跑 5 秒要 496 秒。

成本帳是這條線最有說服力的部分（以 15 秒計）：

| 方案 | 解析度 | 成本 | 倍率 |
|---|---|---|---|
| MiniMax H3 / Google Colab L4 | 約 720p | **約 €0.23** | 1.0× |
| MiniMax H3 / 官方 API | 768p | $1.20 ≈ €1.04 | 約 4.5× |
| Seedance 2.5 / 官方 API | 720p | ≈ $2.28 ≈ €1.98 | 約 8.6× |

配合 `googlecolab/google-colab-cli`（Colab 可透過 CLI / MCP 連線），等於能讓 Codex 或 Claude Code 直接呼叫 Colab 生成——有人形容「深夜讓 agent 跑一整晚，早上收素材」。當然，@Eric_Kangg 的提醒也值得記：批次成本再低，**真正決定經濟性的是 accepted-shot rate**，廢片率高的話便宜也是白搭。

---

### 3. 社群把 H3 從「影片模型」改造成導演台、圖像編輯器與 LoRA 素材工廠

這是本週最有意思的一條線：官方給的是模型，**能直接開拍的流程幾乎全是社群補的**。

**多鏡頭與時間軸控制。** H3 原生支援用 `[Shot 3] At 00:04.000, the camera cuts to an extreme close-up of her eye` 這類指令指定分鏡，連「元圖裡不存在的瞳孔特寫」都能跳出來；音訊（BGM + 環境音）是與畫面同時生成而非後製。社群據此做出時間軸編輯器 `ComfyUI_MiniMaxH3_Director`（拖進 ComfyUI 即用，涵蓋文生視頻、圖生視頻、首尾幀、影片換人）與 `jlucasmcrell/ComfyUI-H3-Multishot`、`joeygambino/MiniMax-H3-Multishot-Workflow`（串接多個 shot 成 30 秒連續帶音影片）。

**當成圖像模型 / 圖像編輯器用。** 因為最小生成單位是 5 frames，@tori29umai0123 做了 `ComfyUI-MiniMaxH3-SingleFrame`：給起始幀與結束幀、生成 22 frames 只抽第 11 幀當作編輯結果，效果不錯但要抽卡；他接著參考 `kohya-ss/musubi-tuner` PR #1018 寫了 **H3 圖像編輯任務的訓練程式碼（最短 5 frames，音訊餵 dummy）**，開始跑 LoRA。另有人用「角色原地旋轉 360 度」的提示詞讓 H3 輸出四面圖當作 **LoRA 訓練素材**——但發現眼型需要靠提示詞額外強化才守得住，表情最好另拍一支影片。

**外部 3D 管線接進來。** 用 Blender 做的分鏡（9 人偶像團體）餵給 H3 參考，**橫排的 9 人順序完全一致**，圓陣稍有失控但陣型與運鏡基本照設計走；也有人拿 Unreal Engine 的房間截圖當參考做寫實編輯，或用 After Effects 的 shape animation 當參考影片來**操控 AI 圖像的打光**。R2V 上限是 9 張參考圖 + 3 支影片。

**其他實用節點：** `T8mars/comfyui-minimax-h3-blockcache-T8`（加速幅度接近 EasyCache 但劣化更小）、`Kijai/MiniMax-H3-TAE`（快速預覽用的 2D tiny VAE，效果勝過 latent2rgb，需搭 ComfyUI-KJNodes 的 ModelPreviewOverride）、`Burgstall-labs/ComfyUI-Seamless-Equirectangular`（無縫全景）、latent 兩段式高解析重繪（864×480 → 1296×720 或 1728×960，專門處理普通 latent upscale 節點吃不下的「影片＋音訊」聯合 latent）。

**一個實用的反直覺經驗：** 多位使用者回報 **768P 的動態表現反而比 2K 穩定**（不是畫質，是線稿部分的動作溶解問題）；另有中文圈作者總結「提示詞不要寫太長太複雜，給模型留想像空間」——這套在即夢上有效的細寫法，在 H3 上反而會毀片。

---

### 4. 「天宮 / 東方巨物美學」病毒式擴散，以及一次值得記下的反駁

本週中文 AI 圈最大的風格浪潮。典型工具鏈是 **MJ 墊圖 + Seedance / Kling 生成**，也有人走 GPT-Image / Dreamina / MiniMax H3。真正被抄最多的是 @hx831126 的**矩陣式提示詞**方法論：不具象描述天宮長什麼樣，而是給模型一個總意境（「天上之宮，不落人間；人如一筆，宮闕無涯；近處有巨物，遠處有天境」），再要求**一次輸出 9 張彼此獨立、可單獨使用的 9:16 影像**，共享世界觀與物理質感但各自變化建築關係、人物距離、天象、地面材質——刻意榨取模型的隨機性。其他人補上了模組化的分層提示詞（【柔和天光】【仙界動態層】）。

值得注意的是 @Hoody 的技術判斷：跑出圈的作品**不只是場景宏大，構圖與色彩技巧才是分水嶺**——這也解釋了為什麼同一套提示詞多數人做出來只是平庸的大場面。

同時，@liyue_ai 給了一段少見的、直接的批評：這些天宮作品「宏大、空洞，彌漫人的卑微、秩序的高高在上……把權力秩序當成東方美學，是這些作品共同的問題」。在一片跟風中，這條值得作為對照保留——風格趨同時，審美判斷才是稀缺品。

---

### 5. DeepSeek-V4-Flash-0731 與 Kimi K3：abliteration 與量化的競速

影片線之外，本地 LLM 這邊同樣是「官方權重一落地、社群立刻分叉」的模式。`deepseek-ai/DeepSeek-V4-Flash-0731` 官方權重釋出後 24 小時內出現：

- **Abliterated 版**：`drowzeys/keys-...-Dspark-Abliterated-32-32`（32/32 全層，宣稱 100% 相容 DSpark）、`huihui-ai/Huihui-...-abliterated-GGUF`（Q2_K，支援 llama.cpp 與 ds4，所有 expert 模組未被 ablate）、`apetersson/...-Abliterated-DS4-Headroom128`（有人實測「相對 base 無劣化」）
- **GGUF 量化**：`unsloth/DeepSeek-V4-Flash-0731-GGUF`、`heath0xFF/...-REAP-GGUF`、antirez 本人的 `antirez/deepseek-v4-gguf`
- **推論引擎適配**：`Rednalreden/DeepSeek-V4-Flash-0731-dwarfstar-q2-gguf`，若現有引擎直接吃得下，預期可跑到 **49 TPS**

Kimi K3 這邊，`Uniboshi/Kimi-K3-Abliterated-V1` 出現後隨即有 Q2_K GGUF 跟進（`GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF`，作者坦言自研新方法效果不佳、暫時改用直接量化版）。

---

## 🛠️ 工具清單

### MiniMax-H3 權重與量化

| 工具 | 連結 | 用途 |
|---|---|---|
| MiniMax-H3（官方權重） | `huggingface.co/MiniMaxAI/MiniMax-H3` | T2V / I2V / 首尾幀 / R2V，原生同步音訊 |
| Comfy-Org/MiniMax-H3 | `huggingface.co/Comfy-Org/MiniMax-H3` | ComfyUI 官方打包，含 `ref2va_pruned_int8_convrot` |
| Kijai/MiniMax-H3-experimental | `huggingface.co/Kijai/MiniMax-H3-experimental` | W4A8（`AsymW4A8Int8Layout`）pruned 量化、`video_vae_int8_convrot` |
| lilcheaty/MiniMax-H3-NVFP4 | `huggingface.co/lilcheaty/MiniMax-H3-NVFP4` | NVFP4 完整＋pruned、text encoder、全部 VAE |
| Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot | `huggingface.co/Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot` | 12-24GB VRAM 用的量化/剪枝權重合集 |
| DmitryDB/MiniMax-H3-INT8-Lean-ConvRot | `huggingface.co/DmitryDB/MiniMax-H3-INT8-Lean-ConvRot` | 24GB 內盡量保留 BF16 換精度 |
| MiniMax H3 INT4 ConvRot（更新版） | 書籤未附連結 | 12GB VRAM 等受限環境 |
| Qwen3-VL-32B Ultra-Heretic（H3 用） | `huggingface.co/ethanfel/Qwen3-VL-32B-Ultra-Heretic-MiniMax-H3-ComfyUI-INT8-ConvRot` | 內容限制較少的 text/vision encoder 替換 |

### MiniMax-H3 加速（LoRA / Attention / Cache / VAE）

| 工具 | 連結 | 用途 |
|---|---|---|
| larryvrh/MiniMax-H3-Turbo-Lora | `huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora` | 4-8 step 蒸餾 LoRA（實測 8 step 才穩） |
| QrusherZA/H3_Turbo_ComfyUI | `huggingface.co/QrusherZA/H3_Turbo_ComfyUI` | Turbo LoRA 的 ComfyUI 免轉換版 |
| drbaph/MiniMax-H3-Turbo-Lora-ComfyUI | `huggingface.co/drbaph/MiniMax-H3-Turbo-Lora-ComfyUI` | pruned / curve-form checkpoint 相容轉換 |
| ComfyUI-sol-attn | `github.com/Saganaki22/ComfyUI-sol-attn` | Blackwell 啟用 Sol-Attn，峰值記憶體 −37% |
| ComfyUI_sol-attn_Blackwell | `github.com/KingGore/ComfyUI_sol-attn_Blackwell` | 5090 專用外掛（作者稱勝過 EasyCache） |
| ComfyUI-SolAttn_triton | `github.com/kijai/ComfyUI-SolAttn_triton` | Patch Sol Attention（Kijai） |
| comfyui-minimax-h3-blockcache-T8 | `github.com/T8mars/comfyui-minimax-h3-blockcache-T8` | 加速接近 EasyCache、劣化更小 |
| ComfyUI-Spectrum-MiniMax-H3 | 教學：`aiaicreate.com` | 加一個節點即加速；4060Ti 16GB 實測 6:03 → 4:44 |
| Kijai/MiniMax-H3-TAE | `huggingface.co/Kijai/MiniMax-H3-TAE` | 快速預覽 tiny VAE，需 ComfyUI-KJNodes |

### MiniMax-H3 ComfyUI 節點與工作流

| 工具 | 連結 | 用途 |
|---|---|---|
| ComfyUI MiniMaxH3 Director | 推文寫 `github.com/huangserva/ComfyUI_MiniMaxH3_Director`；卡片顯示 `seesee75-commits/ComfyUI-MiniMaxH3-Director`（來源不一致） | 時間軸/導演台工作流，涵蓋 T2V、I2V、首尾幀、影片換人 |
| ComfyUI-H3-Multishot | `github.com/jlucasmcrell/ComfyUI-H3-Multishot` | 多鏡頭串接 |
| MiniMax-H3-Multishot-Workflow | `huggingface.co/joeygambino/MiniMax-H3-Multishot-Workflow` | 單節點串多 shot 成 30 秒帶音影片 |
| ComfyUI-MiniMaxH3-SingleFrame | `github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame` | 把 H3 當圖像編輯器（5 frames 抽 1 張） |
| ComfyUI-Seamless-Equirectangular | `github.com/Burgstall-labs/ComfyUI-Seamless-Equirectangular` | 無縫全景（equirectangular）輸出 |
| comfyui-video-tiler | `github.com/maDcaDDie2000/comfyui-video-tiler` | 影片分塊處理 |
| minimax-h3-hybrid-cond | `github.com/kitsune123150/minimax-h3-hybrid-cond` | 混合條件控制 |
| ComfyUI-Fantastic-MiniMaxH3-PromptBuilder | 書籤未附連結 | 提示詞編輯與參考媒體管理 |
| MiniMax-H3 Tools | 書籤未附連結 | 8 項補缺功能：配對資料處理、保畫面重生音訊、提示詞驗證、NF4 試驗性載入 |
| H3 自動提示詞節點 | 書籤未附連結（@aiaicreate） | 影像分析與提示詞構築分離，壓低 VLM 呼叫成本 |
| awesome-minimax-h3-prompts | `github.com/BeatAPI/awesome-minimax-h3-prompts` | 精選 H3 提示詞集 |
| ComfyUI H3 day-0 說明 | `blog.comfy.org/p/minimax-h3-day-0-support-in-comfyui` | 官方 T2V/I2V 範本工作流 |
| musubi-tuner PR #1018 | `github.com/kohya-ss/musubi-tuner/pull/1018` | H3 圖像編輯任務 LoRA 訓練基礎 |

### 本地 LLM

| 工具 | 連結 | 用途 |
|---|---|---|
| DeepSeek-V4-Flash-0731 | `huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731` | 官方權重 |
| unsloth GGUF | `huggingface.co/unsloth/DeepSeek-V4-Flash-0731-GGUF` | 通用 GGUF |
| antirez/deepseek-v4-gguf | `huggingface.co/antirez/deepseek-v4-gguf` | DwarfStar 作者本人的 DS4 GGUF |
| Rednalreden dwarfstar-q2-gguf | `huggingface.co/Rednalreden/DeepSeek-V4-Flash-0731-dwarfstar-q2-gguf` | DwarfStar 引擎適配，預期 49 TPS |
| huihui-ai abliterated GGUF | `huggingface.co/huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF` | Q2_K，expert 模組未 ablate |
| heath0xFF REAP GGUF | `huggingface.co/heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF` | REAP 剪枝後的 GGUF |
| apetersson Headroom128 | `huggingface.co/apetersson/DeepSeek-V4-Flash-0731-Abliterated-DS4-Headroom128` | 實測相對 base 無劣化 |
| Uniboshi/Kimi-K3-Abliterated-V1 | `huggingface.co/Uniboshi/Kimi-K3-Abliterated-V1` | Kimi K3 解除審查版 |
| GrEarl Kimi-K3 Q2_K GGUF | `huggingface.co/GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF` | 上者的量化版 |

### 影像 / 3D / 音訊模型

| 工具 | 連結 | 用途 |
|---|---|---|
| InfiniSplat（SIGGRAPH Asia 2026） | `huggingface.co/spaces/PLUS-WAVE/InfiniSplat`／`zju3dv.github.io/InfiniSplat/` | 單張圖 **<1 秒**生成可導覽 3DGS 場景；室內合成顯著勝過 SHARP |
| Krea 2-Pose ControlNet | `huggingface.co/thedeoxen/Krea-2-pose-controlnet`／Space：`hugging-apps/krea2-pose-controlnet` | 照片→骨架→新角色同姿勢，也可直接手繪骨架 |
| Kroma（Krea 2 × Chroma LoRA） | `huggingface.co/lodestones/Kroma` | rank 256 / alpha 256，含完整微調 normalization |
| Muscriptor（Mirelo × Kyutai） | `huggingface.co/spaces/hugging-apps/muscriptor-music-transcription`（推測完整路徑） | 音訊→**分樂器 MIDI 音軌**轉譯 |
| Irodori-TTS v4-Small | `huggingface.co/Aratako/Irodori-TTS-v4-Small` | 600M 參數 TTS，text encoder 換成 SoftBank 版，最長 120 秒 |
| Core ML Model Zoo（46 個） | `huggingface.co/collections/mlboydaisuke/core-ml-model-zoo-6a7078dc888e7b13efd35631` | Depth/偵測/分割/matting/超解析/inpainting/VLM/TTS/LLM，解壓即拖入 Xcode |
| clark-swin2sr-lightweight x2 1.58bit | `huggingface.co/clark-labs/clark-swin2sr-lightweight-x2-1.58bit` | 極低位元超解析 |
| ThisIsIllustrious v4.0 | Civitai（未附完整連結） | Illustrious 系奇幻/SF 風格 checkpoint |
| animd-Krea2Turbo | `civitai.com/models/2784060` | Krea 2 Turbo 動漫向，可做日文漫畫 |
| Higgsfield《Hell Grind》 | `higgsfield.ai` | 95 分鐘 AI 長片全開源（見快速參考卡） |

### 開發者 CLI / Agent / 生產力

| 工具 | 連結 | 用途 |
|---|---|---|
| pdf-inspector（Firecrawl） | `github.com/firecrawl/pdf-inspector` | Rust 寫的本地 PDF 處理：~20ms 分類、200 份 2.8 秒、表格與圖表擷取品質高，免等 OCR |
| google-colab-cli | `github.com/googlecolab/google-colab-cli` | Colab 支援 CLI / MCP，讓 Codex 或 Claude Code 直接叫 GPU |
| Director Skills | GitHub（書籤未附連結） | 開源 Agent Skills，讓 agent 像導演一樣串起腳本→生成→剪輯，支援 Claude Code / Codex |
| hbg-life-simulation | `github.com/Mr-funny/hbg-life-simulation` | Codex 生圖反代成 API，**10 路並發**破解串行瓶頸 |
| jungle-trail | `github.com/StarKnightt/jungle-trail` | 全程序生成的 Three.js 第一人稱叢林，含提示詞與程式碼 |
| USOMAP | `amix-design.com/asoboad/tools/d-usomap/` | 免費奇幻世界地圖生成，**不使用生成式 AI**，靠地理行為計算（水源附近生城鎮、自動生國界地名） |
| VFX Toolkit 1.0（Blender） | `superhivemarket.com/products/vfx` | 100+ VFX 資產與預設：手繪動畫特效、程序化 VFX、合成器特效 |
| SiroinoSotai（VRChat 共通素體） | `siroinoworks.booth.pm/items/8268676` | CC0 免費，PC 版 △16,704 / Mobile 版 △3,500，商用改變再散布皆可、免標註 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| **MiniMax-H3** | 開源影片模型（權重公開） | `MiniMaxAI/MiniMax-H3` / Comfy-Org 量化版 | T2V/I2V/首尾幀/R2V＋原生同步音訊；影格數須為 17n+5，上限 362 frames（15.08s） | ComfyUI 官方、@MiniMax_AI |
| **Turbo LoRA** | 蒸餾加速 LoRA | `larryvrh/MiniMax-H3-Turbo-Lora`（或 ComfyUI 重打包版） | 20 → 8 steps，1280×736/10s 從 272s 降到 160s | @Machinedelusion |
| **SageAttention 2.2.0** | Attention 核心 | `pip install`（開源） | 4090 48G 上單段 425.6s→322.0s（+24.3%） | @servasyy_ai |
| **Sol-Attn（Blackwell）** | Attention patch | `Saganaki22/ComfyUI-sol-attn` | 40/50 系 int8 attention，峰值記憶體 −37% | @Saganaki22 |
| **EasyCache** | 推論快取 | ComfyUI 節點 | 約 1.67× 加速，與 Sage 疊加可讓 1M/5s 從 6 分降到 2 分半 | Kijai / @umiyuki_ai |
| **WanGP v12.41** | 本地部署框架 | 開源 | H3 只吃 5-6GB VRAM 跑 5s、8-9GB 跑 832×480/15s | @deepbeepmeep |
| **MiniMax-H3 NVFP4 / INT4 / W4A8** | 量化權重矩陣 | HF（lilcheaty / Abiray / Kijai / DmitryDB） | 依 12GB→24GB→48GB+ 顯卡對號入座 | 多位社群成員 |
| **ComfyUI MiniMaxH3 Director** | ComfyUI 工作流 | GitHub（見上表註記） | 拖進去即可用的「導演台」，涵蓋四種生成模式 | @NFTCPS / @servasyy_ai |
| **ComfyUI-MiniMaxH3-SingleFrame** | ComfyUI 節點 | `tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame` | 把影片模型當圖像編輯器用（需抽卡，LoRA 訓練中） | @tori29umai |
| **Blockcache-T8** | 加速節點 | `T8mars/comfyui-minimax-h3-blockcache-T8` | 加速幅度近 EasyCache、畫質劣化更小 | @umiyuki_ai |
| **InfiniSplat** | 3DGS 生成 | HF Space（PLUS-WAVE） | 單張圖 <1 秒生成可漫遊場景，室內品質勝 SHARP | @pengsida |
| **Krea 2-Pose ControlNet** | ControlNet | `thedeoxen/Krea-2-pose-controlnet` | 姿勢遷移，可直接手繪骨架 | @HuggingApps / @toyxyz3 |
| **Muscriptor** | 音訊轉譜模型 | HF Space | 首個能把樂團 jam 準確轉成**分樂器 MIDI** 的模型 | @HuggingApps |
| **pdf-inspector** | 開發者 CLI（Rust） | `firecrawl/pdf-inspector` | Agent 免 OCR 處理 PDF，200 份 / 2.8 秒 | @nickscamara_ |
| **DeepSeek-V4-Flash-0731** | 開源 LLM | HF 官方權重＋多路 GGUF/abliterated 分叉 | 本地推論，DwarfStar 路線預期 49 TPS | @MiaAI_lab 等 |
| **Higgsfield《Hell Grind》** | 開源電影素材庫 | `higgsfield.ai` | 95 分鐘 AI 長片、**製作費 $500,000**、坎城影展市場展放映，**全部提示詞與 assets 公開** | @higgsfield |
| **Colab L4 跑 H3** | 部署方案 | `googlecolab/google-colab-cli` ＋公開 notebook | 15 秒 720p 約 €0.23，比官方 API 便宜 4.5× | @aigeboku |

---

## 🎯 本週趨勢

### 1. 開源影片模型的真正勝負手不在模型，而在「社群補完流程的速度」

@servasyy_ai 的一句話最準確：「模型是官方的，能直接開拍的流程是社群補的——這才是開源真正發生的地方。」H3 從權重釋出到出現導演台工作流、圖像編輯節點、多路量化、蒸餾 LoRA、訓練程式碼，是**以天為單位**。這對選型的實際意義是：評估一個開源模型時，「有沒有 day-0 的 ComfyUI 原生支援」以及「Kijai / kohya 這批人有沒有進場」，比 benchmark 分數更能預測它三個月後好不好用。閉源的 Seedance 2.5 在單段 30 秒直出、大量參考素材（社群貼文有 30 張與 50 個兩種說法）與表演細膩度上仍領先——三方對比測評中 Seedance 2.0 綜合第一、happyhorse 1.1 第二、H3 第三——但 H3 拿到的是**可改造性**，這是 API 給不了的。

### 2. 量化不再是「省 VRAM 的妥協」，而是預設的發行格式

值得注意的細節：本週幾乎所有加速手段都**要求**主模型是 `int8_convrot` 才能用——Turbo LoRA 如此，text encoder 也如此。也就是說，量化版不再是 bf16 原版的降級選項，而是**生態的預設基準**，跑原生 bf16 反而會被排除在加速鏈之外（68GB VRAM 換來的 333 秒，還比不上 4090 上疊滿優化的成績）。同時 Kijai 的 W4A8（4-bit 存、8-bit 算）指出方向：權重精度與運算精度解耦，是接下來消費級部署的主要優化空間。**選型建議：直接從 `int8_convrot` 起步，除非你在做需要對比基準的研究。**

### 3. GUI 正在從創作流程裡消失，Agent 接管了節點連線

@jAlpha_create 的說法有代表性：「這次幾乎沒開 ComfyUI 畫面。RunPod 有官方 MCP，借 GPU → 生成 → 清理，全部交給 Claude Code，不用連節點也不用找設定。」同樣模式反覆出現：用 Codex 寫並發生圖反代、用 ChatGPT Sol 實作 I2V 節點、用 Codex 一次生出全部歌詞圖、用 codex 生成深度影片腳本、用 Claude Code 驅動 Colab。**這意味著 ComfyUI 的價值正在從「視覺化編輯器」轉向「可被 agent 呼叫的執行後端」**，而 Director Skills 這類 Agent Skills 會成為新的分發單位。附帶的實務提醒：那位作者建議「第一支先用 GUI 走通比較安心」——agent 適合重複，不適合冷啟動除錯。

### 4. 風格病毒化的速度已經超過審美形成的速度

「天宮 / 巨物美學」這一波把問題暴露得很清楚：提示詞矩陣一放出，幾天內全網同質化，而多數作品只複製了「宏大」，沒複製構圖與色彩。@liyue_ai 的批評（把權力秩序當東方美學）不見得所有人同意，但它指向一個真實的結構性後果——**當生成成本降到每 15 秒 €0.23，稀缺的不再是產能而是判斷力**。實務上有兩條可操作的線索：其一，多位使用者發現 H3 上**提示詞寫得越細反而越容易毀片**（與即夢的經驗相反），該給模型留隨機空間；其二，@Eric_Kangg 提的 accepted-shot rate 才是真成本——批次便宜但廢片率高，省下來的錢會在篩選時間上還回去。

### 5. 開源 LLM 的「發布—解禁—量化」已經是流水線，而合規壓力同時在收緊

DeepSeek-V4-Flash-0731 與 Kimi K3 兩條線幾乎同構：官方權重落地 → 24 小時內 abliterated 版 → 立刻出 Q2_K / REAP / 引擎專用 GGUF → 有人做劣化驗證（「相對 base 無劣化」）。這條流水線的成熟度已經高到**社群開始比較不同 abliteration 手法的品質**，而不只是「有沒有解禁版」。與此同時，EU AI Act 針對 AI 生成內容的**識別標示義務已於 8 月 2 日起適用**（日媒報導的「違規罰 65 億日圓」被指有誤導成分，實際條件需看條文）。本地無審查模型的可得性與內容標示的法定義務同時上升，兩者的張力會是接下來幾個月的主要議題。

---

*本週書籤高度集中於 MiniMax-H3；報告中的所有效能數字均來自書籤內原作者的實測回報，硬體與設定各異，不構成橫向可比的 benchmark。*
