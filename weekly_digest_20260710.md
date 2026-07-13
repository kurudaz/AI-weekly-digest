# OpenCode 週報 - 2026.07.10

本週書籤的重心從上週的 Krea 2 首爆，轉向一條全新主線：**Anima 動漫向 base 模型的 int8 量化潮**——一週內冒出 WAI-ANIMA、Sudachi、SmoothMix、Arthemy 等一大票派生模型，配合 int8 把 VRAM 砍半、商用整理與 Negpip 控制技巧同步成熟。Krea 2 這邊則從「首發爆量」進入「生態填坑」階段（Depth ControlNet、Edit 訓練、風格 LoRA 持續產出）。影片線上 **Seedance 2.0 Skill OS + LTX 2.3 int8 加速 + iPhone AR previs** 讓 AI 電影工作流更成熟；本地 LLM 則由 **Nemotron-Labs-3-Puzzle-75B** 與 **Qwen3.6 塞進消費級卡**接棒。另有兩條清晰支線：**2D 動畫/VTuber 工具潮**與 **iPhone Core AI 端側模型**。以下依主線拆解。

---

## 🔥 本週焦點

### 1. Anima 生態爆發：int8 量化把動漫向 base 模型門檻壓到 2.3GB

本週最明顯的新主線，是 **Anima**（動漫/半寫實向的開源 base 模型）在幾天內長出完整派生生態，核心推力是 **int8 量化**：

- **量化落地**：`Anima base int8` 上架 CivitAI（`civitai.com/models/2754368`），**保留約 90% 品質、VRAM 使用砍半**，容量從 ~4.1GB 降到 **~2.3GB**（省 ~1.8GB）；`WAI-ANIMA int8` 也以同樣「90% 品質 / 50% VRAM / NVIDIA 環境加速」的規格跟進。
- **派生模型海嘯**：一週內冒出 **Sudachi (Anima) v1.0**（明亮動漫調特化，支援 ComfyUI / Forge Neo）、**SmoothMix Ultimate「Anima」**（prompt 調整即可在動漫調與寫實調間切換）、**Arthemy Comics Anima v2.0**（西方漫畫風、手調全套自訂節點）。不可思議ちゃん更直接整理了一篇「**商用利用可能なANIMAモデルまとめ**」（note.com / Kindle 出版），把可商用的分支一次列清。
- **控制技巧成形**：Nobin 梳理了 ComfyUI/A1111 系的 Anima LoRA 分類（角色/畫風/衣裝/姿勢/部位滑桿），並點名少見的 **Anima-Edit LoRA**（`civitai.com/models/2650553`）；賢木イオ 則給了實用解法——Anima 常在沒指示時自己畫出**吹き出し、汗、水滴、motion lines**，用 **Haoming02 版 Negpip** 掛進 ForgeNeo，寫 `(speech bubble, text, wet, sweat, water drop:-1)` 即可乾淨排除。
- **微調配方**：社群實測 Anima 的 **Style LoRA 只訓 MLP 層、500 steps** 就有明顯效果（`LR=4e-5, Self/Cross-Attention LR=0, Rank=8, α=8, batch=2`，資料集用 SDXL 系生成 500 張）。エマノン 也回報 Anima 在 **1856×1024 近 FHD 尺寸幾乎不破綻、~40 秒/張**，意外實用。

> **判讀**：Anima 走的是和 Krea 2 不同的路線——**不是靠單模型強，而是靠「動漫 base + int8 + 可商用分支 + 成熟控制 hack」的組合拳**。int8 把 VRAM 砍半這件事，等於把動漫生圖的入場門檻直接壓到低階卡，於是派生模型與微調配方在幾天內全部長出來。對想做動漫圖的人，本週的實際結論是：**base 選 Anima（或其可商用分支），配 Negpip 排雜訊、MLP-only Style LoRA 快速調風格**，就是目前 CP 值最高的一套。

### 2. Krea 2 從首爆進入「生態填坑」：ControlNet、Edit 訓練與風格 LoRA

Krea 2 這週不再是新聞，而是把上週還缺的拼圖一塊塊補齊：

- **結構控制**：Tanmay Patil 釋出 **Krea-2 Depth ControlNet-LoRA**（`huggingface.co/Patil/Krea-2-depth-controlnet`），**保住近乎完美的 3D 結構、同時讓 prompt 完全改圖**，且對 Turbo 也有效。
- **Edit 能力**：Ostris 在 **AI Toolkit** 加了「用參考圖訓 Krea2」的支援，實測**學 edit 概念的速度不輸真正的 edit 模型**；推論端需搭配 `ostris/ComfyUI-Krea2-Ostris-Edit` 節點正確處理 reference。
- **風格 LoRA 續攤**：`Danrisi/GrainScape_Krea2`（顆粒質感）、`Danrisi/AnalogCore_Krea2`（VHS 攝影機像素風）、`Komorebi1995/krea2-raw-jpaf-celpaint-lora`（乾淨賽璐珞上色）、こたじろう `Kotajiro/KTMix_LoRA`，以及 R@aiaicreate 的 `liquidmix_krea2_turbo_fp8 v0.28`（專攻**獸人等非人類角色與發光效果**的質感檢證 merge，實寫不擅長）。
- **工作流與節點**：HARUKI 更新 Krea 2 **workflow V3.0**（在 V2 上加 `Apply Krea2 NegPiP` 與 `ComfyUI-Krea2T-Enhancer`）；青龍聖者/nova452 推 `ComfyUI-Conditioning-Rebalance`（Krea 2 Encode Rebalance，注意構成變了要重接才不報錯）。訓練仍推 **sdbds 的 `musubi-tuner-scripts GUI`**（kohya 官方版照做仍卡錯，這版順跑，600 steps 約 25 分出一個 style LoRA）。
- **周邊**：`wikeeyang/Krea2-Turbo-HD-V1`（帶 Tune 版 VAE）、－TAKATO－ 的**動的ポーズ型プロンプト集（5 選）**、以及 Krea2 Uncensored + Image-to-Prompt + 4K Upscaler 的 **4K 生成流程**（Reddit 有完整 setup）。

> **判讀**：Krea 2 已經度過「能不能跑」的階段，本週的看點是**可控性補完**——Depth ControlNet 補結構、Ostris Edit 補改圖、NegPiP/Rebalance 補負向與條件平衡。這條線印證了上週的判斷：**開源圖像模型的壽命取決於生態填坑速度**，而 Krea 2 的填坑速度依舊是社群第一梯隊。

### 3. AI 電影工作流成熟：Seedance 2.0 Skill OS、LTX 2.3 int8 加速、iPhone AR previs

「Blender/iPhone 擋戲 → Seedance 2.0 生成」的流程本週被系統化與加速：

- **系統化**：鸟哥（藍鳥會）點名的 **Seedance 2.0 Skill OS**（GitHub 免費）把 Seedance 從「隨便糊提示詞」拉到專業影視工作流——**鏡頭、運鏡、節奏、分鏡、場景、動作、風格全拆好**，不用自己瞎摸。
- **previs 去 Blender 化**：Max Prokopp「討厭 Blender」，改用 **iPhone AR** 在真實 3D 空間走位捕捉手持晃動，再讓 Seedance 上戲（附開源連結）；kan:mi 直接請 **Codex** 現做同款 iOS 工具；kai+ 則示範 **Blender × Claude × Seedance 2**（用 JSON prompt，效果存疑）。Reid Hannaford 的 Blender previs 伏擊鏡頭仍是標準示範（MJ 起始幀 → Blender block out + 動畫 → Seedance）。
- **3D 導演台**：DynamicWang 預告 **AWPlanet**——把遊戲引擎的運鏡方法引入 3D 工作台，可切換控制角色或鏡頭，所見即所得編分鏡。
- **LTX 2.3 加速戰**：Heba AI 把 15s 1920×1088 從 **268s 壓到 209s**；GearMaster/Winnougan 的 **LTX-2.3-INT8**（`Winnougan/LTX-2.3-INT8`）檔案小 50%、生成再降到 **~140s**。LoRA 生態同步齊備：`Seamless-Equirectangular`（360° 全景，rank-128/15k steps）、`10-Eros`（NSFW）、`Sync-LoRA Edit`（首幀驅動影片編輯）、`LightX2V`（4 步、抑制手抖）；多 LoRA 混用要用 `hashu786/comfyui-hrnodes`（避免全域權重平均掉指令）。Linoy Tsaban 還把 LTX 官方 pre-production 指南做成 **Storyboard playground（ZeroGPU space）**，先快速迭代分鏡再生成。
- **輔助**：LOOPY 的 `aicameramovements.com`（相機移動 prompt 詞庫）、NOBU 指出 **Nano Banana 2 Lite 已足夠出繪コンテ**、Philipp Schmid 發佈 **Gemini Omni Flash 影片編輯 skill**（`npx skills add google-gemini/gemini-skills --skill gemini-omni-flash-api`，涵蓋 text/image-ref/first-frame to video）。

> **判讀**：影片線的關鍵字從上週的「可控」進一步變成「**可系統化、可加速、可去專業軟體化**」。Skill OS 把運鏡知識 repo 化、iPhone AR 把 previs 從 Blender 手上搶下來、int8 把 LTX 出片時間砍到分鐘級——三件事合起來，AI 電影的門檻正在從「會渲染/會 Blender」滑向「**會擺鏡頭 + 有一支 iPhone**」。

### 4. 本地 LLM 量化延續：Nemotron Puzzle 75B、Qwen3.6 塞進消費級卡

上週的「巨模型上小盒子」本週換主角，重點轉向**中量級 MoE 塞進單卡/單 Spark**：

- **Nemotron-Labs-3-Puzzle-75B-A9B**：由 `Nemotron-3-Super-120B-A12B` **壓縮而來**的 interactive 特化衍生版——**75B total / 9.3B active、mamba+MoE、config 標 256K context**、NVFP4，**單台 DGX Spark 就裝得下還有餘裕**。同門還有 `nvidia/Nemotron-Labs-TwoTower-30B-A3B-Base-BF16`。
- **Qwen3.6 下放消費級卡**：A-Uta 用 **RTX 5060 Ti 16GB（約 7-8 萬日圓）** 跑 `Qwen3.6-35B-A3B` 達 **~105 tok/s**（`llama-cpp-turboquant + CUDA`）；`Qwen3.6-27B-NVFP4` 則被塞進 **RTX 5070Ti ×2、VRAM 20GB 以下**跑到 **29-31 tok/s**（Marlin kernel）。自架可用 `MiaAI-Lab/Qwen3.6-27B-NVFP4-vLLM`。
- **DGX Spark recipe**：Wësche 的 `spark-bench` 給出 `Qwen3.6-27B NVFP4 + DFlash` 單 Spark 配方——**262K context、34 tok/s、3.4s 延遲**，並找到 `k=10 + CUDA graphs` 為最佳 DFlash 設定。
- **量化與 agent 化**：`llama.cpp-tq3` 的 **TQ3_4S**（turbo-tan，Blackwell 專用、按誤差分配位元）；`Qwopus3.6-35B-A3B-Coder-MTP-GGUF`（agent lane 特化）與 `Qwen3.6-27B-Fable-MTP`（解除審查 + 學 agent 思考軌跡）；Huihui 續發 `DeepSeek-V4-Flash-abliterated-GGUF`（Q3_K_S，MXFP4）。0xSero 還發現讓 **REAP 對「自我懷疑」用詞加懲罰**可再減 30% 輸出 token。
- **省時小技**：Alok 提醒 `llama.cpp` 從源碼編譯若跑 20 分鐘是設錯了——限定 `CMAKE_CUDA_ARCHITECTURES` 只編自己的架構，**編譯快 5 倍**。

> **判讀**：這週把上週「200B+ 上雙 Spark」的敘事，補上了**中量級的甜蜜點**——75B/35B/27B 這一段正好卡在「單台 Spark 或一兩張消費級 50 系卡」的預算內，且 NVFP4 + Marlin/DFlash 讓 tok/s 站上實用線。對多數人而言，本週的選型訊號很明確：**要 agent/coding，Qwopus/Qwen3.6-Coder-MTP + 單 Spark 或 5070Ti×2 已經夠用**，不必追 GLM-5.2 那種 TB 級旗艦。

### 5. 兩條支流：2D 動畫/VTuber 工具潮，與 iPhone Core AI 端側模型

- **2D 動畫 / VTuber**：這週冒出一整叢輕量工具——852話 的 `nakawari.jp`（**ラフ画 AI 中割り**服務）、852wa 的 **Anime2.5DRig**（OSS，把分好圖層的 PSD 拖進瀏覽器，髮/眼/口即時會動，`852wa.github.io/Anime2.5DRig`）、rotejin 的 **PuruPuruPNGTuber**（表情差分 PNG + 前髪後ろ髪 的本地 Web app）、1mm_module 的**「1 張 PNG → 自動分層 + 補完眨眼/口パク」**瀏覽器工具、animede 的 `charsheet`（Diffusers 角色表生成、含去背）。852wa 還把減色/簡易點陣/glitch/色收差等**簡易畫像加工工具**集中到 `852wa.com/tools.html`。
- **iPhone Core AI 端側模型**：MLBoy 連發兩發——**MinerU2.5-Pro（1.2B doc OCR）在 iPhone 17 Pro 全程本機**把頁面照片轉成有標題/段落/表格的結構化文字（Apache-2.0、Core AI）、以及 **FLUX.2-klein-4B** 在 Apple Silicon 上做**多參考圖編輯**（一張抽主體、一張抽場景、合成輸出）。kgonia7 則把 `FLUX.2-9B-klein` 的 **text encoder 砍掉 38%**（8.19B→5.10B），讓 768×768 生成**整個塞進 16GB VRAM 免 offload**。

> **判讀**：這兩條支流看似無關，其實共享同一個方向——**把生成/動畫能力縮小到「一台裝置就能跑」**。2D VTuber 工具全是瀏覽器/本地 app、不需雲端；Core AI 模型則把 OCR、多圖編輯直接搬進 iPhone。當大模型還在拼「塞進單機」時，這一端已經在拼「塞進口袋」，是值得長期追蹤的邊緣化趨勢。

---

## 🛠️ 工具清單

### 動漫向圖像模型（Anima 生態）
| 工具 | 連結 | 用途 |
|---|---|---|
| Anima base int8 | `civitai.com/models/2754368` | Anima int8 量化，90% 品質 / 50% VRAM（~2.3GB） |
| WAI-ANIMA int8 | `civitai.com` | WAI-ANIMA 的 int8 量化版 |
| Sudachi (Anima) v1.0 | `civitai.com` | 明亮動漫調特化派生（ComfyUI / Forge Neo） |
| SmoothMix Ultimate「Anima」 | `civitai.com` | prompt 切換動漫調／寫實調 |
| Arthemy Comics Anima v2.0 | `civitai.com` | 西方漫畫風，手調自訂節點 |
| Anima-Edit LoRA | `civitai.com/models/2650553` | Anima 的 edit 系 LoRA（少見） |
| 商用可 ANIMA 模型整理 | `note.com`（不可思議ちゃん） | 可商用 Anima 分支彙整 |

### Krea 2 生態（續攤）
| 工具 | 連結 | 用途 |
|---|---|---|
| Krea-2 Depth ControlNet-LoRA | `huggingface.co/Patil/Krea-2-depth-controlnet` | 保 3D 結構、prompt 完全改圖（Turbo 可用） |
| ComfyUI-Krea2-Ostris-Edit | `github.com/ostris/ComfyUI-Krea2-Ostris-Edit` | AI Toolkit 訓的 Krea2 edit LoRA 推論節點 |
| krea2-raw-jpaf-celpaint-lora | `huggingface.co/Komorebi1995/krea2-raw-jpaf-celpaint-lora` | 賽璐珞乾淨上色風 |
| GrainScape / AnalogCore Krea2 | `huggingface.co/Danrisi/...` | 顆粒質感 / VHS 攝影機像素風 LoRA |
| liquidmix_krea2_turbo_fp8 v0.28 | `huggingface.co`（R@aiaicreate） | 獸人等非人類角色 + 發光效果質感 merge |
| Krea2-Turbo-HD-V1 | `huggingface.co/wikeeyang/Krea2-Turbo-HD-V1` | 帶 Tune 版 VAE 的 checkpoint |
| ComfyUI-Conditioning-Rebalance | `github.com/nova452/ComfyUI-Conditioning-Rebalance` | Krea 2 Encode Rebalance / 條件優化 |
| musubi-tuner-scripts GUI | `github.com`（sdbds） | Krea2 LoRA 訓練，600 steps ~25 分 |

### 影片生成 / AI 電影工作流
| 工具 | 連結 | 用途 |
|---|---|---|
| Seedance 2.0 Skill OS | GitHub（鸟哥/藍鳥會，免費） | 把 Seedance 系統化為影視工作流（運鏡/分鏡/節奏） |
| AWPlanet | （DynamicWang，即將發布） | 遊戲引擎運鏡的 3D 導演工作台 |
| iPhone AR handheld previs | （Max Prokopp，開源連結於串） | 手機 AR 捕捉手持運鏡 → Seedance |
| LTX-2.3-INT8 | `huggingface.co/Winnougan/LTX-2.3-INT8` | LTX 2.3 int8，檔案 -50%、~140s 出片 |
| LTX 2.3 LoRA 群 | `huggingface.co`（TheBurgstall / adrepale / SagiPolaczek） | 360° 全景 / 10-Eros NSFW / 首幀驅動編輯 |
| comfyui-hrnodes | `github.com/hashu786/comfyui-hrnodes` | 多 LTX2 IC-LoRA 同時使用不互相稀釋 |
| LTX 2.3 Storyboard playground | `LTX.io`（Linoy Tsaban ZeroGPU space） | 生成前快速迭代分鏡 |
| gemini-omni-flash-api skill | `npx skills add google-gemini/gemini-skills` | Omni Flash 影片編輯 agent skill |
| aicameramovements.com | `aicameramovements.com` | 相機移動 prompt 詞庫 |

### 本地 LLM / 量化與推論
| 工具 | 連結 | 用途 |
|---|---|---|
| Nemotron-Labs-3-Puzzle-75B-A9B | `huggingface.co/nvidia/...`（NVFP4） | 75B/9.3B active、mamba+MoE、單 Spark 可跑 |
| Nemotron-Labs-TwoTower-30B-A3B | `huggingface.co/nvidia/Nemotron-Labs-TwoTower-30B-A3B-Base-BF16` | 30B/3B active base 模型 |
| Qwen3.6-27B-NVFP4-vLLM | `github.com/MiaAI-Lab/Qwen3.6-27B-NVFP4-vLLM` | 自架 vLLM NVFP4 推論 recipe |
| spark-bench（qwen36 + DFlash） | `github.com/Weschera/spark-bench` | 單 Spark：262K context / 34 tok/s |
| llama.cpp-tq3（TQ3_4S） | `github.com/turbo-tan/llama.cpp-tq3` | Blackwell 專用、按誤差分配位元 |
| Qwopus3.6-35B-A3B-Coder-MTP-GGUF | `huggingface.co/Jackrong/Qwopus3.6-35B-A3B-Coder-MTP-GGUF` | agent/coding 特化 |
| DeepSeek-V4-Flash-abliterated-GGUF | `huggingface.co/huihui-ai/...` | DeepSeek V4-Flash 無審查（MXFP4） |
| 2Wild Vision Mode | `github.com/tonyd2wild/2Wild-Vision-Mode` | 2GB VRAM 給純文字模型「加上眼睛」（Qwen 3.5） |

### 2D 動畫 / VTuber / On-device
| 工具 | 連結 | 用途 |
|---|---|---|
| Anime2.5DRig | `852wa.github.io/Anime2.5DRig`（OSS） | PSD 拖入瀏覽器即時做 2.5D VTuber |
| PuruPuruPNGTuber | `github.com/rotejin/PuruPuruPNGTuber` | 表情差分 PNG + 前後髮的進階 PNGTuber |
| nakawari（中割り） | `nakawari.jp` | ラフ画 AI 中割り服務（beta） |
| charsheet | `github.com/animede/charsheet` | Diffusers 角色表生成（含去背） |
| MinerU2.5-Pro CoreAI | `huggingface.co/mlboydaisuke/MinerU2.5-Pro-CoreAI` | iPhone 端 1.2B 文件 OCR（Apache-2.0） |
| FLUX.2-klein-4B CoreAI | `huggingface.co/mlboydaisuke/FLUX.2-klein-4B-CoreAI` | Apple Silicon 多參考圖編輯 |

### 3D / Remesh / 開發者工具
| 工具 | 連結 | 用途 |
|---|---|---|
| AutoRemesher 1.0 | `github.com/huxingyi/autoremesher` | MIT 授權高速四邊形 remesh（可商用） |
| AutoRemesher Blender Bridge | `github.com/adriflex/autoremesher-blender-bridge` | AutoRemesher 的 Blender 橋接 addon |
| MarkItDown | （Rachel 推薦） | 先把 PDF 轉 Markdown 再餵 AI，省 token 少翻車 |
| comfyui_qwen_edit_pixel_perfect | `github.com/oron1208/comfyui_qwen_edit_pixel_perfect` | 修 Qwen-Image-Edit-2511 的 pixel shift |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Anima base int8 | 動漫圖像模型 | CivitAI | 90% 品質 / 50% VRAM，~2.3GB 入場 | civitai `2754368` |
| Krea-2 Depth ControlNet | 圖像 ControlNet | HF | 鎖 3D 結構 + prompt 改圖，Turbo 可用 | @TanmayPatil79 |
| Seedance 2.0 Skill OS | 影片工作流 | GitHub（免費） | 運鏡/分鏡/節奏系統化 | @NFTCPS |
| LTX-2.3-INT8 | 影片模型量化 | HF | 檔案 -50%、~140s 出 15s 影片 | @GearmasterGear |
| Nemotron-3-Puzzle-75B | LLM (75B/9.3B active) | HF NVFP4 | mamba+MoE、256K context，單 Spark | @MiaAI_lab |
| Qwen3.6-35B-A3B | LLM (35B/3B active) | HF GGUF | RTX 5060Ti 16GB ~105 tok/s | @UtaAoya |
| Qwen3.6-27B + DFlash | LLM recipe | spark-bench | 單 Spark 262K context / 34 tok/s | Wesche |
| Anime2.5DRig | 2.5D VTuber 工具 | GitHub（OSS） | PSD 拖入瀏覽器即時動嘴/眼 | @852wa（8co28 團隊） |
| MinerU2.5-Pro CoreAI | 端側 doc OCR (1.2B) | HF | iPhone 全本機文件結構化 | @JackdeS11 |
| FLUX.2-klein-4B CoreAI | 端側圖像編輯 (4B) | HF | Apple Silicon 多參考圖合成 | @JackdeS11 |
| AutoRemesher 1.0 | 3D remesh 工具 | GitHub（MIT） | 可商用高速四邊形重拓樸 | @jeremyhu2016 |

---

## 🎯 本週趨勢

1. **int8 成為「動漫向 base 模型」平民化的關鍵鑰匙。** Anima 這波證明了：**當一個 base 模型能靠 int8 把 VRAM 砍半（品質只掉 ~10%），派生模型與微調配方就會在幾天內湧現**。這和上週 Krea 2 靠 NVFP4/AWQ 壓門檻是同一套邏輯，只是換到動漫賽道。選模型時該問的已不是「原始畫質多好」，而是「**有沒有 int8/量化分支 + 可商用 + 成熟的 Negpip/LoRA 控制生態**」。

2. **Krea 2 進入「填坑期」，可控性補完取代跑分競賽。** 本週 Krea 2 的新聞全是 Depth ControlNet、Edit 訓練、NegPiP、Conditioning Rebalance——**沒有一則在比誰畫質高，全在補結構/改圖/負向/條件平衡的控制拼圖**。這是開源模型從「爆紅」走向「長壽」的健康訊號：生態填坑速度，比首發驚豔更決定模型能活多久。

3. **AI 電影的門檻從「會軟體」滑向「會擺鏡頭 + 一支手機」。** Seedance Skill OS 把運鏡知識 repo 化、iPhone AR 把 previs 從 Blender 手上搶走、LTX 2.3 int8 把出片壓到分鐘級——三件事疊起來，**previs 正在去專業軟體化**。競爭焦點徹底從「單鏡頭好不好看」轉向「鏡頭語言可不可控、能不能快速迭代」。

4. **LLM 選型的甜蜜點正在下移到 75B/35B/27B。** 上週還在拼 200B-1T 上雙 Spark，本週主角換成 **Nemotron Puzzle 75B（單 Spark）與 Qwen3.6-27B/35B（消費級 50 系卡）**。NVFP4 + Marlin/DFlash 讓這一段的 tok/s 站上實用線，配 Coder-MTP 系列做 agent/coding 已綽綽有餘。**對多數人，中量級 MoE + 單盒子，已是比追旗艦更務實的選擇。**

5. **「塞進口袋」開始與「塞進單機」並行成形。** 一邊是 2D VTuber/中割り工具全走瀏覽器與本地 app、零雲端；另一邊是 MinerU/FLUX.2-klein 把 OCR、多圖編輯直接搬進 iPhone Core AI。當旗艦模型還在比「單機裝得下多大」，端側這一路已經在比「一台裝置能跑多完整的生成/動畫流程」——這條邊緣化路線值得長期盯著。

---

需要的話，我可以把某條主線（例如 Anima int8 派生模型的實測比較、或 Seedance 2.0 Skill OS 的完整運鏡流程）展開成更完整的操作筆記，或補上各工具的安裝指令。
