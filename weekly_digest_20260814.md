# OpenCode 週報 - 2026.08.14

> 本週的書籤幾乎被同一件事佔滿：**MiniMax-H3 開源權重落地**。從權重上架到社群把它塞進 12GB 顯卡、補出整套「導演台」工作流，前後不到一週。以下把散落的項目串成幾條主線。

---

## 🔥 本週焦點

### 1. MiniMax-H3 權重釋出 → 社群在 72 小時內把它壓進消費級顯卡

`MiniMaxAI/MiniMax-H3` 上架 Hugging Face，**ComfyUI 同日（day 0）原生支援**，`Comfy-Org/MiniMax-H3` 直接提供 `minimax_h3_ref2va_pruned_int8_convrot.safetensors` 等預轉權重。真正的看點不是模型本身，而是接下來三天的量化競賽：

- **Kijai** 的 `MiniMax-H3-experimental`：把權重壓到 **4-bit、運算走 8-bit**，使用 `AsymW4A8Int8Layout` 佈局 —— 省空間又不掉速。同 repo 還有 `minimax_h3_video_vae_int8_convrot.safetensors`，光是把 Video VAE 從 fp16 換成 int8_convrot，生成時間 **76 秒 → 71 秒（約 7%）**。
- **cheaty** 的 `lilcheaty/MiniMax-H3-NVFP4`：full + pruned 變體、text encoder、全部 VAE 一次到位，在 RTX Pro 6000 上端到端測過。
- **`Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot`**：社群彙整的 INT4 / INT8 / Mixed / NVFP4 全家桶，明確標榜給 **12GB～24GB VRAM** 使用者。
- **`DmitryDB/MiniMax-H3-INT8-Lean-ConvRot`**：反向操作 —— 在 24GB 上界內盡量多留 BF16 層來換精度，代價是速度略降。

門檻掉得有多快，看實測就知道：

| 硬體 | 設定 | 生成時間 |
|---|---|---|
| RTX 3060 6GB + 64GB RAM | 480p / 5s T2V（EasyCache + SageAttention） | 4–5 分 |
| RTX 4060Ti 16GB | 加 `ComfyUI-Spectrum-MiniMax-H3` 一個節點 | 6分03秒 → 4分44秒 |
| RTX 4090 24GB | **15 秒有聲影片一次到底**（362 frames = 訓練上限 15.08s） | 9.6 分 |
| RTX 5090 | 640×640 / 24fps / 5s i2v | 約 80 秒 |
| RTX 5090 | 1M 像素（1184×896）/ 5s i2v | 6–7 分 |
| RTX PRO 6000B | 1376×768 / 7s / bf16 R2V（VRAM 68GB） | 333 秒 |

**WanGP v12.41** 更進一步：5 秒（124 frames）只吃 **5–6GB VRAM**，832×480 的 15 秒只要 **8–9GB**。有人給出完整本地權重帳單：H3 Ref2VA INT8 ConvRot 20.97GB + Qwen3-VL-32B NVFP4-AWQ 15.69GB + Video VAE FP16 5.21GB + Audio VAE FP32 0.61GB = **42.47GB**。

而成本對比才是真正的引爆點（15 秒影片）：

| 方案 | 解析度 | 成本 | 倍率 |
|---|---|---|---|
| MiniMax H3 / Google Colab L4 | ~720p | 約 **€0.23** | 1.0× |
| MiniMax H3 官方 API | 768p | $1.20 ≈ €1.04 | 4.5× |
| Seedance 2.5 官方 API | 720p | ≈ $2.28 ≈ €1.98 | 8.6× |

搭配 `googlecolab/google-colab-cli`（Colab 支援 CLI / MCP 連線），已經有人用 Codex / Claude Code 在深夜掛機批次跑素材。

🔗 [MiniMaxAI/MiniMax-H3](https://huggingface.co/MiniMaxAI/MiniMax-H3) ・ [Comfy-Org/MiniMax-H3](https://huggingface.co/Comfy-Org/MiniMax-H3) ・ [Kijai/MiniMax-H3-experimental](https://huggingface.co/Kijai/MiniMax-H3-experimental) ・ [ComfyUI day-0 公告](https://blog.comfy.org/p/minimax-h3-day-0-support-in-comfyui)

---

### 2. 加速堆疊三段式：注意力核心 + Cache + 蒸餾 LoRA，收益可乘

這週的加速討論已經收斂成一套可複製的組合拳，而且每一段都有獨立的實測數字：

**① 注意力核心**
- `SageAttention 2.2.0`：RTX 4090 48G 實測單段 **425.6 秒 → 322.0 秒（24.3%）**，首輪極值測到 44%；連跑 13 段約省 22 分鐘。
- `Sol-Attn`（Blackwell 專用）：`Saganaki22/ComfyUI-sol-attn` 宣稱 **峰值記憶體降 37%**，為 40/50 系提供更快更準的 int8 attention。另有 `KingGore/ComfyUI_sol-attn_Blackwell` 與 Kijai 的 `ComfyUI-SolAttn_triton`。
- 新清士的 5090 實測：**Sage 2.2 + Sol 相較裸跑縮短 34.3%**，畫質掉得不多，但背景形狀會略微崩壞。

**② Cache**
- `EasyCache`：Kijai 提到約 **1.67 倍**加速；有人把 1M 像素 / 5 秒從 6 分壓到 **2 分半**（配 Sage Attention）。
- `T8mars/comfyui-minimax-h3-blockcache-T8`：號稱加速幅度接近 EasyCache，但**劣化更少**。5090 用戶則普遍回報 Sol-Attn 比 EasyCache 更划算。

**③ 蒸餾 LoRA**
- `larryvrh/MiniMax-H3-Turbo-Lora`：4–8 step 蒸餾 LoRA。實測 **1280×736 / 10s 從 272 秒降到 160 秒**，但**只有 8 steps 可用，4–7 steps 會崩**。需要 diffusion model 與 text encoder 都是 int8_convrot，且原始 LoRA 型別不合 ComfyUI，要用 repo 內「For ComfyUI users」的轉換碼。
- ComfyUI 現成轉換版：`drbaph/MiniMax-H3-Turbo-Lora-ComfyUI`、`QrusherZA/H3_Turbo_ComfyUI`。

**三段疊起來**的極端案例：Turbo LoRA + Sage Attention + Sol-Attn + int8_convrot VAE，把 **20 steps 160 秒壓到 8 steps 71 秒**。

另一條低調但重要的線：**Kohya** 指出 H3 的 `TokenRefiner` 不依賴 timestep，可以在 denoising loop 前預先計算完再 offload —— 速度幾乎不變，但**省下約 1.6GB VRAM**。同時社群也解開了 `17n+5` 幀數限制之謎：VAE encoder 以 17 frames 為單位（pixel `[1,4,4,4,4]` 的 17F → latent 5F），decoder 則是 latent 1F → pixel 4F，其餘靠 trim 和 crossfade 處理，**所以長片段的 VRAM 用量是固定的**。

🔗 [larryvrh/MiniMax-H3-Turbo-Lora](https://huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora) ・ [Saganaki22/ComfyUI-sol-attn](https://github.com/Saganaki22/ComfyUI-sol-attn) ・ [T8mars/comfyui-minimax-h3-blockcache-T8](https://github.com/T8mars/comfyui-minimax-h3-blockcache-T8)

---

### 3. 從「生成影片」到「導演台」：H3 的分鏡指令催生了一整套節點生態

H3 真正拉開差距的能力不是畫質，是**可控性**：T2V / I2V / 首尾幀 / **R2V（Reference-to-Video，最多 9 張參考圖）**，以及**原生同步生成音訊**（BGM、環境音、台詞一起出，不是後製配上去的）。

最關鍵的是它接受**時間軸級的分鏡指令**：

```
[Shot 3] At 00:04.000, the camera cuts to an extreme close-up of her eye
```

即使原圖沒有那個畫面，模型也會生出「瞳孔大特寫」。也有人用 `CUT1` / `CUT2` 的形式直接指定分鏡。社群立刻把這個能力包成工具：

- **`huangserva/ComfyUI_MiniMaxH3_Director`**：拖進 ComfyUI 就能用的導演台工作流，涵蓋文生影片、圖生影片、首尾幀、影片換人。作者同時放出**專治 H3「影片＋音訊」聯合 latent** 的二次重繪節點 —— 先低解析度生成，再把 latent 放大重繪，864×480 → 1296×720（1.5×）或 1728×960（2×）。普通 latent upscale 節點處理不了這種聯合 latent。
- **`seesee75-commits/ComfyUI-MiniMaxH3-Director`**：H3 專用的時間軸編輯器。
- **`jlucasmcrell/ComfyUI-H3-Multishot`** ＋ `joeygambino/MiniMax-H3-Multishot-Workflow`：把多個 shot 串成單一連續影片（含音訊），可做到 30 秒。
- **`tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame`**：把影片模型當**圖像編輯器**用 —— 生成最小合法的 5 frames，只取其中 1 張當輸出。同一位作者已參考 `kohya-ss/musubi-tuner` [PR #1018](https://github.com/kohya-ss/musubi-tuner/pull/1018) 開始跑 H3 圖像編輯任務的 LoRA 訓練（最短 5 frames，音訊輸入餵 dummy）。
- **`Burgstall-labs/ComfyUI-Seamless-Equirectangular`**：360 全景無縫輸出，在模型發布第一天就published。
- **`Kijai/MiniMax-H3-TAE`**：快速訓練的 2D tiny VAE，專門用於預覽 —— 效果勝過標準 latent2rgb，需搭配 `ComfyUI-KJNodes` 的 `ModelPreviewOverride` 節點。

Comfy 本體也在修：`Fix MiniMax H3 latent noise mask sampling`（#15322）已合併。

實務上湧現的玩法比工具還多：用 **Blender 分鏡** 讓 H3 參照，9 個偶像橫排的**站位順序完全一致**（圓陣稍有失控，但運鏡幾乎照設計走）；用 **Unreal Engine 房間截圖** 當參考做寫實編輯；用 **After Effects shape 動畫**當參考來操控 AI 圖像的打光；讓角色原地 360 度旋轉 22 幀輸出四面圖，當作 LoRA 訓練素材。有人乾脆整場**沒開 ComfyUI 介面** —— RunPod 有官方 MCP，租 GPU → 生成 → 收工全交給 Claude Code。

🔗 [huangserva/ComfyUI_MiniMaxH3_Director](https://github.com/huangserva/ComfyUI_MiniMaxH3_Director) ・ [tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame](https://github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame) ・ [Kijai/MiniMax-H3-TAE](https://huggingface.co/Kijai/MiniMax-H3-TAE)

---

### 4. 「天宮／東方巨物美學」洗版，以及一場關於它的爭論

本週中文 AI 圈的視覺主旋律：雲海之上的仙界宮闕、巨物尺度、九重天。單一貼文衝到 **536 萬次瀏覽**。技術路徑大多是 **MJ 墊圖 + Seedance / Kling 生成**，或直接 MiniMax H3 出片。

真正值得存下來的是**方法論**。虎小象提出的「矩陣式提示詞」思路很清楚：**不具象化描述天宮長什麼樣，而是給模型一個總意境，然後刻意利用模型的隨機性一次輸出 9 張互不重複的圖**：

> 天上之宮，不落人間；人如一筆，宮闕無涯；近處有巨物，遠處有天境；靜處顯莊嚴，動處生仙氣；空而不寂，滿而不塞。

再拆成可替換模組：**【柔和天光】**（低位太陽藏在畫外、天空當成巨大柔光幕、溫潤暖黃色溫）、**【仙界動態層】**（雲瀑越過露台傾瀉、近乎無色的天水從高層平台垂落）。Hoody 的觀察補上另一半：跑出圈的作品不只是場景宏大，更靠構圖與色彩技巧。

另一條實用經驗來自 MiniMax H3 使用者：**提示詞不要寫太長太複雜**。同一套細膩寫法在即夢上有效，在 H3 上反而連續翻車 —— 「粗糙一點，給模型留一點想像空間」。

而 @liyue_ai 給出了本週最尖銳的反論：這些天宮視覺「就是個宇宙級殯儀館，宏大、空洞，瀰漫人的卑微、秩序的高高在上……把權力秩序當成東方美學」。這條批評本身也收穫大量傳播——**風格紅利期通常也是審美疲勞的起點**。

---

### 5. 製作方法論本身正在被開源

Higgsfield 把耗資 **50 萬美元、95 分鐘**、在坎城市場展放映過的 AI 長片 **《Hell Grind》完全開源** —— **所有提示詞、所有素材公開**。有人讀完製作 Brief 後整理出三個要點，比片子本身更有價值：

- 角色一致性**不只靠 character sheet**
- 空間一致性要準備 **GEO SPATIAL LAYOUT**
- 表演要寫「**目的 · 障礙 · 身體反應**」

同期出現的 **Director Skills** 是同一個方向的工程化：開源的 Agent Skills，讓 Agent 像導演一樣建構「腳本 → 影像生成 → 剪輯」的完整工作流，可在 Claude Code / Codex 等支援 Agent Skills 的工具上運行。

🔗 [Higgsfield 開源專案](https://higgsfield.ai)

---

## 🛠️ 工具清單

### MiniMax-H3 權重與量化

| 工具 | 連結 | 用途 |
|---|---|---|
| MiniMaxAI/MiniMax-H3 | [HF](https://huggingface.co/MiniMaxAI/MiniMax-H3) | 官方原始權重 |
| Comfy-Org/MiniMax-H3 | [HF](https://huggingface.co/Comfy-Org/MiniMax-H3) | ComfyUI 官方預轉權重（含 int8_convrot 系列） |
| Kijai/MiniMax-H3-experimental | [HF](https://huggingface.co/Kijai/MiniMax-H3-experimental) | W4A8（`AsymW4A8Int8Layout`）壓縮權重 + int8_convrot Video VAE |
| lilcheaty/MiniMax-H3-NVFP4 | [HF](https://huggingface.co/lilcheaty/MiniMax-H3-NVFP4) | NVFP4 量化：full/pruned/text encoder/全 VAE |
| Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot | [HF](https://huggingface.co/Abiray/Minimax-H3-nvfp4-INT4-INT8-Convrot) | 社群彙整量化包，鎖定 12–24GB VRAM |
| DmitryDB/MiniMax-H3-INT8-Lean-ConvRot | [HF](https://huggingface.co/DmitryDB/MiniMax-H3-INT8-Lean-ConvRot) | 24GB 內盡量保留 BF16 換精度 |
| MiniMax H3 INT4 ConvRot（更新版） | — | 結構保留的 INT4 量化，目標 12GB VRAM |
| ethanfel/…-Ultra-Heretic-MiniMax-H3-…-INT8-ConvRot | [HF](https://huggingface.co/ethanfel/Qwen3-VL-32B-Ultra-Heretic-MiniMax-H3-ComfyUI-INT8-ConvRot) | 去審查版 Qwen3-VL-32B text encoder |
| Kijai/MiniMax-H3-TAE | [HF](https://huggingface.co/Kijai/MiniMax-H3-TAE) | 快速預覽用 tiny VAE（需 KJNodes） |

### MiniMax-H3 加速

| 工具 | 連結 | 用途 |
|---|---|---|
| larryvrh/MiniMax-H3-Turbo-Lora | [HF](https://huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora) | 4–8 step 蒸餾 LoRA（實測只有 8 step 穩定） |
| drbaph/MiniMax-H3-Turbo-Lora-ComfyUI | [HF](https://huggingface.co/drbaph/MiniMax-H3-Turbo-Lora-ComfyUI) | Turbo LoRA 的 ComfyUI 相容轉換版 |
| QrusherZA/H3_Turbo_ComfyUI | [HF](https://huggingface.co/QrusherZA/H3_Turbo_ComfyUI) | 同上，另一個轉換版本 |
| ComfyUI-sol-attn | [GitHub](https://github.com/Saganaki22/ComfyUI-sol-attn) | Blackwell 用 int8 attention，峰值記憶體 −37% |
| ComfyUI_sol-attn_Blackwell | [GitHub](https://github.com/KingGore/ComfyUI_sol-attn_Blackwell) | 5090 專用 Sol-Attn plugin |
| ComfyUI-SolAttn_triton | [GitHub](https://github.com/kijai/ComfyUI-SolAttn_triton) | Kijai 的 Sol Attention patch 節點 |
| comfyui-minimax-h3-blockcache-T8 | [GitHub](https://github.com/T8mars/comfyui-minimax-h3-blockcache-T8) | 接近 EasyCache 的加速、更低劣化 |
| ComfyUI-Spectrum-MiniMax-H3 | — | 加一個節點即加速（4060Ti：6分03秒→4分44秒） |

### ComfyUI 節點與工作流

| 工具 | 連結 | 用途 |
|---|---|---|
| ComfyUI_MiniMaxH3_Director | [GitHub](https://github.com/huangserva/ComfyUI_MiniMaxH3_Director) | 導演台工作流：T2V/I2V/首尾幀/換人一站式 |
| ComfyUI-MiniMaxH3-Director | [GitHub](https://github.com/seesee75-commits/ComfyUI-MiniMaxH3-Director) | H3 專用時間軸編輯器 |
| ComfyUI-H3-Multishot | [GitHub](https://github.com/jlucasmcrell/ComfyUI-H3-Multishot) | 多鏡頭串接 |
| MiniMax-H3-Multishot-Workflow | [HF](https://huggingface.co/joeygambino/MiniMax-H3-Multishot-Workflow) | 單節點串接多 shot 成 30 秒含音訊長片 |
| ComfyUI-MiniMaxH3-SingleFrame | [GitHub](https://github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame) | 把 H3 當圖像編輯模型（5 frames 取 1） |
| ComfyUI-Seamless-Equirectangular | [GitHub](https://github.com/Burgstall-labs/ComfyUI-Seamless-Equirectangular) | 360 全景無縫輸出 |
| minimax-h3-hybrid-cond | [GitHub](https://github.com/kitsune123150/minimax-h3-hybrid-cond) | 混合條件輸入 |
| comfyui-video-tiler | [GitHub](https://github.com/maDcaDDie2000/comfyui-video-tiler) | 影片分塊處理 |
| ComfyUI-Fantastic-MiniMaxH3-PromptBuilder | — | H3 提示詞編輯與參考媒體管理 |
| MiniMax-H3 Tools | — | 8 項補完機能（配對資料處理、保畫面重生音訊、NF4 試驗載入） |
| awesome-minimax-h3-prompts | [GitHub](https://github.com/BeatAPI/awesome-minimax-h3-prompts) | 精選 H3 提示詞合集 |
| musubi-tuner PR #1018 | [GitHub](https://github.com/kohya-ss/musubi-tuner/pull/1018) | H3 圖像編輯任務 LoRA 訓練碼 |

### 本地 LLM（GGUF / Abliterated）

| 工具 | 連結 | 用途 |
|---|---|---|
| deepseek-ai/DeepSeek-V4-Flash-0731 | [HF](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731) | 官方權重 |
| unsloth/DeepSeek-V4-Flash-0731-GGUF | [HF](https://huggingface.co/unsloth/DeepSeek-V4-Flash-0731-GGUF) | 標準 GGUF 量化 |
| antirez/deepseek-v4-gguf | [HF](https://huggingface.co/antirez/deepseek-v4-gguf) | DwarfStar 作者本人的 DS4 版 GGUF |
| Rednalreden/…-dwarfstar-q2-gguf | [HF](https://huggingface.co/Rednalreden/DeepSeek-V4-Flash-0731-dwarfstar-q2-gguf) | DwarfStar 對應 q2，宣稱可跑到 49 TPS |
| huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF | [HF](https://huggingface.co/huihui-ai/Huihui-DeepSeek-V4-Flash-0731-abliterated-GGUF) | 去審查 GGUF（保留全部 expert 模組） |
| apetersson/…-Abliterated-DS4-Headroom128 | [HF](https://huggingface.co/apetersson/DeepSeek-V4-Flash-0731-Abliterated-DS4-Headroom128) | 經檢驗「對 base 無劣化」的去審查版 |
| heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF | [HF](https://huggingface.co/heath0xFF/DeepSeek-V4-Flash-0731-REAP-GGUF) | REAP 剪枝版 GGUF |
| Uniboshi/Kimi-K3-Abliterated-V1 | [HF](https://huggingface.co/Uniboshi/Kimi-K3-Abliterated-V1) | Kimi K3 去審查版 |
| GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF | [HF](https://huggingface.co/GrEarl/Kimi-K3-Abliterated-V1-Q2_K-GGUF) | 上者的 Q2_K 直接量化 |

### 3D / 影像 / 音訊

| 工具 | 連結 | 用途 |
|---|---|---|
| InfiniSplat（SIGGRAPH Asia 2026） | [Space](https://huggingface.co/spaces/PLUS-WAVE/InfiniSplat) ・ [專案頁](https://zju3dv.github.io/InfiniSplat/) | 單張圖 **<1 秒**生成可導覽 3DGS 場景，室內合成品質勝過 SHARP |
| Krea-2-pose-controlnet | [HF](https://huggingface.co/thedeoxen/Krea-2-pose-controlnet) ・ [Space](https://huggingface.co/spaces/hugging-apps/krea2-pose-controlnet) | 照片→骨架→新角色套用該姿勢，也可直接手繪骨架 |
| Muscriptor（Mirelo × Kyutai） | [Space](https://huggingface.co/spaces/hugging-apps/muscriptor-music-transcription) | 首個能把音訊轉成**逐樂器 MIDI 音軌**的模型 |
| lodestones/Kroma | [HF](https://huggingface.co/lodestones/Kroma) | Krea 2 Kroma v0.1 LoRA，rank 256 / alpha 256，含完整微調的 normalization |
| Aratako/Irodori-TTS-v4-Small | [HF](https://huggingface.co/Aratako/Irodori-TTS-v4-Small) | 600M 參數日語 TTS，換用新 text encoder，最長 120 秒 |
| clark-swin2sr-lightweight-x2-1.58bit | [HF](https://huggingface.co/clark-labs/clark-swin2sr-lightweight-x2-1.58bit) | 1.58bit 超輕量 2× 超解析度 |
| Core ML Model Zoo | [HF Collection](https://huggingface.co/collections/mlboydaisuke/core-ml-model-zoo-6a7078dc888e7b13efd35631) | 46 個 Core ML 模型（深度/偵測/分割/摳像/超解析/VLM/TTS/LLM），解壓 `.mlpackage` 拖進 Xcode |

### 開發者 CLI / Agent 工具

| 工具 | 連結 | 用途 |
|---|---|---|
| firecrawl/pdf-inspector | [GitHub](https://github.com/firecrawl/pdf-inspector) | Rust 寫的本地 PDF→Markdown，**~20ms 分類、200 份 PDF 只要 2.8 秒**，表格與圖表擷取品質佳 |
| googlecolab/google-colab-cli | [GitHub](https://github.com/googlecolab/google-colab-cli) | 用 CLI / MCP 從 Codex 或 Claude 直接驅動 Colab 跑生成 |
| Director Skills | — | 開源 Agent Skills，讓 Agent 像導演一樣建構影片製作工作流（Claude Code / Codex） |
| Mr-funny/hbg-life-simulation | [GitHub](https://github.com/Mr-funny/hbg-life-simulation) | 把 Codex 反代成生圖 API，突破串行限制做到 **10 路並發** |
| StarKnightt/jungle-trail | [GitHub](https://github.com/StarKnightt/jungle-trail) | 全程序化 Three.js 第一人稱叢林，附提示詞與程式碼 |

### 素材與周邊

| 工具 | 連結 | 用途 |
|---|---|---|
| SiroinoSotai | [BOOTH](https://siroinoworks.booth.pm/items/8268676) | VRChat 共通素體，**CC0 免費**，PC △16,704 / Mobile △3,500，商用改變再散布皆可、免標註 |
| animd-Krea2Turbo | [Civitai](https://civitai.com/models/2784060) | Krea 2 Turbo 動漫向 checkpoint，用於日文漫畫生成 |
| ThisIsIllustrious v4.0 | Civitai | Illustrious 系，奇幻/SF 世界觀 + 人物描寫融合 |
| Manga Vision `anima v1` | Civitai | Anima 基底，**免 trigger word** 產出黑白漫畫墨線與陰影 |
| Anima ControlNet-LLLite v3 | kohya-ss PR | 語意編輯用 semantic trunk 實驗性實作，附 ComfyUI 節點 |
| Blender VFX Toolkit 1.0 | [Superhive](https://superhivemarket.com/products/vfx) | 100+ VFX 素材與預設：手繪動畫特效、程序化 VFX、合成器特效 |
| USOMAP | [連結](https://amix-design.com/asoboad/tools/d-usomap/) | 免費幻想世界地圖產生器，**不使用生成式 AI**，靠地理行為模擬（水源附近生城鎮、自動生成國界地名） |
| 《Hell Grind》 | [Higgsfield](https://higgsfield.ai) | 95 分鐘 AI 長片全開源，含所有提示詞與素材 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| MiniMax-H3 | 影音生成模型（開源權重） | HF / ComfyUI 原生 | T2V・I2V・首尾幀・R2V（最多 9 參考圖）＋原生同步音訊 | [MiniMaxAI/MiniMax-H3](https://huggingface.co/MiniMaxAI/MiniMax-H3) |
| Comfy-Org/MiniMax-H3 | 預轉權重 | HF 下載 | ComfyUI 直用的 pruned / int8_convrot 權重 | [HF](https://huggingface.co/Comfy-Org/MiniMax-H3) |
| Kijai/MiniMax-H3-experimental | W4A8 量化權重 | HF 下載 | 4-bit 存 8-bit 算，省 VRAM 不掉速 | [HF](https://huggingface.co/Kijai/MiniMax-H3-experimental) |
| MiniMax-H3-Turbo-Lora | 蒸餾 LoRA | HF 下載（需轉換） | 8 steps 生成，10 秒片段 272s→160s | [HF](https://huggingface.co/larryvrh/MiniMax-H3-Turbo-Lora) |
| ComfyUI-sol-attn | ComfyUI 節點 | GitHub clone | Blackwell int8 attention，峰值記憶體 −37% | [GitHub](https://github.com/Saganaki22/ComfyUI-sol-attn) |
| blockcache-T8 | ComfyUI 節點 | GitHub clone | 低劣化 cache 加速，替代 EasyCache | [GitHub](https://github.com/T8mars/comfyui-minimax-h3-blockcache-T8) |
| ComfyUI_MiniMaxH3_Director | ComfyUI 工作流 | GitHub clone | 拖進 ComfyUI 即用的 H3 導演台 | [GitHub](https://github.com/huangserva/ComfyUI_MiniMaxH3_Director) |
| ComfyUI-MiniMaxH3-SingleFrame | ComfyUI 節點 | GitHub clone | 把影片模型當圖像編輯器 | [GitHub](https://github.com/tori29umai0123/ComfyUI-MiniMaxH3-SingleFrame) |
| WanGP v12.41 | 本地推論實作 | 專案安裝 | H3 極致 VRAM 優化：5s 僅 5–6GB | 作者 @deepbeepmeep |
| InfiniSplat | 3D 重建模型 | HF Space / 專案頁 | 單圖 <1 秒 → 可導覽 3DGS 場景 | [Space](https://huggingface.co/spaces/PLUS-WAVE/InfiniSplat) |
| Muscriptor | 音訊轉錄模型 | HF Space | 音訊 → 逐樂器 MIDI note track | [Space](https://huggingface.co/spaces/hugging-apps/muscriptor-music-transcription) |
| Krea-2-pose-controlnet | ControlNet | HF / Space | 姿勢骨架控制新角色生成 | [HF](https://huggingface.co/thedeoxen/Krea-2-pose-controlnet) |
| pdf-inspector | Rust CLI / 函式庫 | GitHub（開源） | Agent 本地處理 PDF，免等 OCR，200 份 / 2.8s | [GitHub](https://github.com/firecrawl/pdf-inspector) |
| google-colab-cli | 開發者 CLI | GitHub | 從 Codex / Claude 驅動 Colab 跑 H3 生成 | [GitHub](https://github.com/googlecolab/google-colab-cli) |
| DeepSeek-V4-Flash-0731 | 本地 LLM | HF（官方＋多方 GGUF） | 本地推論，dwarfstar q2 可達 ~49 TPS | [HF](https://huggingface.co/deepseek-ai/DeepSeek-V4-Flash-0731) |
| Core ML Model Zoo | 模型集（46 個） | HF Collection | Apple 平台端側部署，拖進 Xcode 即用 | [HF](https://huggingface.co/collections/mlboydaisuke/core-ml-model-zoo-6a7078dc888e7b13efd35631) |
| SiroinoSotai | 3D 素體 | BOOTH 免費（CC0） | VRChat 及 VRC 外的遊戲/影像製作 | [BOOTH](https://siroinoworks.booth.pm/items/8268676) |

---

## 🎯 本週趨勢

### 1. 影片模型的競爭軸線已經從「畫質」移到「單位成本 × 可重複性」

岚叔的兩輪橫評結論很明確：**Seedance 2.0 綜合最好、happyhorse 1.1 次之、MiniMax H3 第三**，差距集中在表演細膩度、夜景氛圍與關鍵細節完成度。但 H3 在本週壓倒性洗版，原因不是它贏了畫質，而是 **15 秒成本 €0.23 vs Seedance 2.5 的 €1.98（8.6 倍）**。

**選型建議很直接**：需要大量素材、需要抽卡、需要反覆迭代的環節（分鏡驗證、風格探索、LoRA 素材、B-roll）全部走本地或 Colab 的 H3；只有最終成片的關鍵鏡頭才值得付 Seedance 2.5 的價格。Eric Kang 那句提醒仍然成立 —— **「便宜」只有在採用率夠高時才是真便宜**，如果大部分廉價生成都被丟棄，帳其實沒省下來。

### 2. 量化格式正在分裂成「硬體世代對照表」，而不是收斂到單一最佳解

本週同時活躍的格式：**NVFP4**（Blackwell）、**INT4 ConvRot**（12GB 卡）、**INT8 ConvRot**（主流）、**INT8 Lean ConvRot**（24GB 保精度）、**W4A8 / AsymW4A8Int8Layout**（Kijai）、**NF4**（試驗中）、**GGUF 混合精度**。

這不是暫時的混亂，而是新常態：**先確定你的卡，再選權重，不要無腦追「檔案最小的那個」**。INT4 在 12GB 卡上是唯一選擇，但在 24GB 卡上它換來的速度提升，遠不如 INT8 Lean 保留 BF16 帶來的品質提升值得。另外注意 `convrot` 已經成為事實上的相容性前綴 —— Turbo LoRA 明確要求 diffusion model 與 text encoder 都是 int8_convrot，混搭會直接失敗。

### 3. 加速堆疊已經標準化，但每一層都有隱藏的失效條件

「注意力核心 + Cache + 蒸餾 LoRA」三段疊加確實能把 20 steps 160 秒壓到 8 steps 71 秒。但實際部署時每一層都有坑：

- **Sol-Attn 限 Blackwell**（40/50 系），舊卡無效
- **Turbo LoRA 只在 8 steps 可用，4–7 steps 會崩**
- **EasyCache 的加速幅度隨機**，多位使用者都提到「省多少看運氣」
- **Sage 2.2 + Sol 雖然只降 34.3%，但背景形狀會輕微崩壞**
- **PyTorch 版本是硬條件**：有人卡在 5 秒生成 496 秒，換 `cu130`（cu128 不行）後才正常

務實做法：**先量出你的基線，一次只加一層並記錄數字**，否則你不會知道慢下來的是哪一段。另外 Kohya 那個 TokenRefiner offload 省 1.6GB 的發現屬於「零成本收益」，值得優先合入。

### 4. 影片模型正在吞併相鄰任務，pipeline 變短但抽卡成本轉移到 prompt

這週最有信號價值的一批實驗，全都是「拿影片模型做非影片的事」：生 5 幀取 1 幀當**圖像編輯器**、原地旋轉 22 幀輸出**四面圖**當 LoRA 素材、原生輸出**同步 BGM 與環境音**取代 TTS＋配樂、無縫**360 全景**、把 AE shape 動畫當**打光參考**。

對工具鏈的意義：以前需要「圖像模型 + ControlNet + TTS + 音效庫 + 剪輯」的組合，現在有機會壓成一個模型加一組提示詞。**但省下的複雜度轉移到了 prompt 工程上** —— 幾乎所有人都提到 seed 抽卡，而且 H3 的最佳實踐（提示詞不要太細，留想像空間）**與即夢等模型完全相反**。跨模型遷移提示詞正在變成一項需要重新學習的技能，而不是複製貼上。

### 5. 「開源」的重心正在從權重移向工作流與資產

huangserva 的一句話總結了這週：**「模型是官方的，能直接開拍的流程是社群補的 —— 這才是開源真正發生的地方。」**

證據鏈很完整：權重釋出當天就有 day-0 ComfyUI 支援、第一天有人發布全景節點、幾天內出現導演台、多鏡頭串接、latent 二次重繪、提示詞建構器、prompt 精選集、LoRA 訓練碼。同樣的模式也出現在 LLM 側 —— **DeepSeek-V4-Flash-0731 官方權重發布隔天，abliterated 版、多種 GGUF 量化、REAP 剪枝版、DwarfStar 相容版全部到位**，Kimi K3 亦然。社群的響應延遲已經壓縮到**小時級**。

同時 Higgsfield 開源 50 萬美元長片的全部提示詞與素材、Director Skills 把導演流程封裝成 Agent Skills、有人用 RunPod MCP 讓 Claude Code 全程代跑 —— **下一階段的「開源」單位不是模型檔案，是可執行的製作流程**。對開發者而言，這意味著現在做工具的窗口期極短：模型發布後 48 小時內能補上生態缺口的專案，才拿得到注意力。

---

> 順帶一提，EU AI Act 關於「AI 生成內容標示義務」的規定已於 **8 月 2 日起適用**。網傳「違反罰 65 億日圓」的說法被指出相當誤導 —— 該金額對應的是禁止行為的制裁上限，並非標示義務本身，實務上仍需看後續執行細則。
