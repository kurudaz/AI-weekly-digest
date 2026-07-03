# OpenCode 週報 - 2026.07.03

本週書籤幾乎被兩條主線佔滿：**Krea 2 開源**引爆的本地圖像生成大爆發，以及 **DeepSeek V4 / GLM-5.2 等巨型 MoE** 在 DGX Spark、Mac Studio 上「塞進單機」的量化競賽。另外一條清晰的支線是 **Blender previs + Seedance 2.0** 正式成為 AI 電影工作流的主流做法。以下依主線重要性拆解。

---

## 🔥 本週焦點

### 1. Krea 2 開源：一週內長出完整生態系

Krea 正式釋出 **Krea 2 開放權重**，一次給了兩個版本 —— **Krea 2 Raw**（未蒸餾、來自 mid-training，專為 fine-tuning 設計）與 **Krea 2 Turbo**（蒸餾後的快速版，用於推論）。官方定調就是一句話：**train on Raw, generate with Turbo**。模型約 **12B**（與 Flux.1 同級），在 **RTX 5090** 上跑 krea2-turbo(nvfp4)，**1280×1920 約 15 秒**出圖；Raw 版則是重量級 —— 光模型 24GB、text encoder 另外 8GB，5090 幾乎是踩線硬撐。畫質方面社群實測**可到 3000×3000 不出 artifact**，2K 是背景細節的甜蜜點。

- 官方 repo：`github.com/krea-ai/krea-2`，技術報告涵蓋架構與 RAW-to-Turbo 設計（`krea.ai/blog/krea-2-technical-report`）
- Hugging Face day-0 上架，附 diffusers 支援 + LoRA 訓練/推論；ComfyOrg 也放出 `Comfy-Org/Krea-2`（含 DistillLoRA）

真正的看點是**生態系在幾天內全部長出來**：
- **量化落地到消費級卡**：`blue-pen5805/krea2-quantizer` 把任意 Krea 2 模型一鍵轉 NVFP4（限 RTX 50 系以上）；`sakamakismile/Krea-2-Turbo-NVFP4-AWQ` 甚至**塞得進 8GB VRAM**；rebelai 也把 GGUF 重傳並修好了 layer 問題、提升 fidelity。
- **LoRA 海嘯**：CivitAI 上 Krea 2 LoRA **一週破 150 個**；ilker 用 Krea 2 Trainer **訓了 999 個 Style LoRA**，每個只 100 steps、LR 0.00035、平均 **~30 秒**訓完。訓練工具方面，社群推 `musubi-tuner-scripts GUI`（kohya 官方版會報錯卡死，這版 30×20=600 steps **約 25 分鐘**出一個 style LoRA）；Ostris 也放了 `krea2_turbo_training_adapter`，可直接在 Turbo 上訓 LoRA 而不破壞蒸餾。
- **自訂 ComfyUI 節點**：`ComfyUI-krea2-negpip`（在 prompt 內做負向強調）、`ComfyUI-Krea2T-Enhancer`（用權重調 prompt 忠實度）、換 Text Encoder（qwen3vl_4b 帶影像解析、甚至有無審查版 `Huihui-Qwen3-VL-4B-abliterated`）、換 VAE（`qwen_image_clear_vae`、Wan2.1 VAE 2x upscale 硬拉到 4K）等。

> **判讀**：Krea 2 這波證明了「開放權重 + 蒸餾快版 + day-0 diffusers」是目前圖像模型最健康的發佈範式。Raw/Turbo 雙軌讓微調者與終端使用者各取所需，加上 NVFP4/AWQ 快速把門檻壓到 8GB VRAM，社群自組裝速度已經比官方 template 更快、更精簡（noma_door 直接吐槽官方 template「無謂地複雜」）。

### 2. 巨型 MoE 本地化戰爭：DeepSeek V4-Flash、GLM-5.2 與 DGX Spark

這週最硬核的主線，是社群在**把幾百 B ～ 1T 級模型塞進單機/雙機**。

**DeepSeek V4-Flash（284B MoE，256 experts、每 token 啟用 6 個）** 成了壓力測試的主角：
- 單張 **RTX 3090（24GB）+ 64GB RAM = 88GB** 就能跑 **IQ2_XXS（80.8 GiB，2-bit experts）**GGUF。
- `0xSero` 的 **REAP** 把 284B **剪枝到 180B**、量化後 82GB，塞進單台 DGX Spark，Q3 單流約 **65 tok/s prefill、13 tok/s 生成**（生成受 Spark ~273 GB/s 頻寬所限）。
- 雙 DGX Spark 的 **DSpark + NVFP4 KV** recipe 把 KV pool 翻倍到 2.04M tokens，**實測 1,048,576（1M）context、50-65 tok/s** 單流。llama.cpp **b9840** 已正式支援 DeepSeek V4。Anemll 也在 M5 MAX 上做 DSpark 實作（DS4 IQ2 +5 tps）。

**GLM-5.2** 被 Philip Kiely 直接定位為「自 DeepSeek-R1 以來開放模型最大新聞」，**效能對標 GPT-5.5 / Opus 4.8，但成本低 70-80%**：
- **Unsloth**：2-bit 版把 **1.51TB 壓到 238GB（-84%）仍保留 ~82% 準確率**，1-bit GGUF 在 **Mac Studio M3 Ultra 256GB 上 ~21.6 tok/s**。
- **NVFP4**：`nvidia/GLM-5.2-NVFP4` 約 465GB，vLLM 官方可 `vllm serve` 直接上 Blackwell，記憶體比 FP8 更省、準確率打平。`0xSero` 用 **Nvidia dynamo + custom NVFP4 + MTP** 把速度拉到 **280+ tok/s（比 6000 系快 4 倍）**。
- 也有 abliterated 無審查 GGUF（huihui-ai）與 REAP-504B GGUF。

**新面孔 Ornith-1.0-35B** 值得記一筆：**spark-bench TrueScore 87.5**，比 Qwen 3.6-27B 高 34 分、比 Qwen 3.6-35B 高 21 分，**僅 3B active（MoE）**，單台 DGX Spark Q4_K_M 就能跑，登上排行榜 **#2（僅次於 API 版 GLM-5.2）**。TQ3_4S 量化只要 13GiB 就能與大 7GB 的 q4_k_m 打平。不過也有實測潑冷水（selim_aktas2 指出 BF16 版容易先列一堆待辦再 loop，HTML smoke test 表現不如 benchmark）。

> **判讀**：這條線的重點不是「誰最強」，而是**部署可行性正在被量化技術重寫**。REAP 剪枝 + NVFP4 KV 是本週兩個關鍵字：前者直接砍參數量讓模型「裝得下」，後者把 KV cache 減半、換來 1M context。對選型的實際意義是 —— **DGX Spark（單台/雙台）已成為跑 200B+ 開源 MoE 的事實標準盒子**，M5 Ultra Mac Studio（傳 2026 上看 768GB RAM）則是另一條 unified memory 路線。另外注意 loop 問題反覆出現（Qwen3.6-Base、GLM-5.2、Ornith 都有），社群共識解法是 `presence_penalty`、temp 0.85-1、reasoning budget、或用會偵測 loop 的 harness。

### 3. Blender previs + Seedance 2.0：AI 電影工作流定型

「Blender 擋戲 → Seedance 2.0 生成」本週被反覆驗證為當前最可控的 AI 影像流程。標準 pipeline 是：**Midjourney 出起始幀 → Blender 用基本幾何體 block out 場景並做鏡頭運動 → Seedance 2.0 填充**。多位創作者示範只用簡單方塊/mannequin 就能驅動精準的多角色鏡頭。

延伸出的巧思：
- **Max Prokopp / kan_mi** 用 **iPhone AR 實拍**取代 Blender —— 在真實 3D 空間走位捕捉手持晃動，再讓 Seedance 上戲（甚至請 Codex 現做 iOS 工具）。
- **WTR「Kling → Gemini Omni 化 mannequin → Seedance」**：先用 Kling 3 生好動作，再用 **Gemini Omni 把角色轉成 mannequin**（否則風格細節會被整個模仿），最後 Seedance 做 motion reference。
- 周邊工具：`AWPlanet`（把遊戲引擎運鏡引入 3D 工作台）、Martini 的 Step Into Set™ Motion Control、Blender addon **QuickMaker**（1 張圖 → 3D 模型/分鏡/運鏡動畫）。

> **判讀**：這代表 AI 影像的競爭焦點已從「單鏡頭好不好看」轉向**鏡頭語言的可控性**。3DCG 沒有被取代，而是退化為「提供空間與運鏡的 previs 骨架」（koguGameDev 提醒：這只用到 3DCG 累積價值的一小部分）。對想入場的人，門檻是「會擺方塊 + 懂鏡頭」而非「會渲染」。

---

## 🛠️ 工具清單

### 圖像生成模型與 ComfyUI 生態
| 工具 | 連結 | 用途 |
|---|---|---|
| Krea 2 (Raw/Turbo) | `github.com/krea-ai/krea-2` | 12B 開源圖像模型，train on Raw / generate with Turbo |
| Comfy-Org/Krea-2 | `huggingface.co/Comfy-Org/Krea-2` | ComfyUI 官方版含 DistillLoRA |
| krea2-quantizer | `github.com/blue-pen5805/krea2-quantizer` | 一鍵把 Krea 2 量化為 NVFP4（限 RTX 50+） |
| ComfyUI-krea2-negpip | `github.com/blue-pen5805/ComfyUI-krea2-negpip` | Krea 2 的 prompt 內負向強調 |
| krea2_turbo_training_adapter | `huggingface.co/ostris/krea2_turbo_training_adapter` | 直接在 Turbo 上訓 LoRA 不破壞蒸餾 |
| PromptCanvas | `github.com/knishika62/PromptCanvas` | 利用 BBOX 認識做區域佈局（支援 Krea 2 / Ideogram 4） |
| Anima v1.0 / NTRMix STYLE | `civitai.com` | 動漫向 base 模型與 style LoRA |
| i1 | (Zhuang Liu 團隊) | 純公開資料集的開源 text-to-image 完整配方 |

### 本地 LLM / 量化與推論
| 工具 | 連結 | 用途 |
|---|---|---|
| DeepSeek V4-Flash GGUF | `huggingface.co/huihui-ai/...DeepSeek-V4-Flash-abliterated-GGUF` | 284B MoE、含無審查版 |
| DeepSeek V4-Flash DSpark (2x Spark) | `github.com/tonyd2wild/DeepSeek-v4-Flash-DSpark-1M-NVFP4-KV-2x-DGX-Spark` | 雙 Spark 跑 1M context |
| REAP (DeepSeek 剪枝) | `0xSero` REAP | 284B → 180B 剪枝，塞進單機 |
| GLM-5.2-GGUF | `huggingface.co/unsloth/GLM-5.2-GGUF` | 1/2-bit 量化，Mac 256GB 可跑 |
| nvidia/GLM-5.2-NVFP4 | `huggingface.co/nvidia/GLM-5.2-NVFP4` | vLLM 官方 NVFP4 checkpoint（~465GB） |
| Ornith-1.0-35B-TQ3_4S | `huggingface.co/YTan2000/Ornith-1.0-35B-TQ3_4S` | 3B active MoE，13GiB 高分模型 |
| llama.cpp-tq3 (TQ3_4S) | `github.com/turbo-tan/llama.cpp-tq3` | Blackwell 專用、按誤差分配位元的量化 |
| spark-bench | `github.com/Weschera/spark-bench` | DGX Spark 部署 recipe 與 benchmark |
| Qwen3.6-35B-A3B-NVFP4-vLLM | `github.com/MiaAI-Lab/Qwen3.6-35B-A3B-NVFP4-vLLM` | 自架 vLLM NVFP4 推論 |
| NVIDIA/Model-Optimizer | (NVIDIA repo) | 自製 NVFP4 量化模型 |
| LFM2.5-230M | `huggingface.co/LiquidAI/LFM2.5-230M` | 230M 極小模型，跑手機/CPU/NPU |
| OpenFugu | `github.com/trotsky1997/openfugu` | 開源複刻 Sakana Fugu 的 LLM 協調器 |

### 影像 / 3DGS / 動畫
| 工具 | 連結 | 用途 |
|---|---|---|
| LTX-2.3 系列 LoRA | `huggingface.co/fal/LTX-2.3-3DREAL-LoRA` 等 | render-to-real、Foley、Sync 首幀編輯、Ingredients |
| free-splatter.cpp | `github.com/localai-org/free-splatter.cpp` | GGML/Vulkan 版影像轉 3DGS，含 WebGL demo，無需 Python |
| Flux-GS | `github.com/xiaobiaodu/Flux-GS` | 手機端即時 Gaussian Splatting |
| QuickMaker (Blender) | `modelinghappy.com/archives/69177` | 1 圖 → 3D 模型/分鏡/運鏡影片 |
| SAM 3 CoreAI | `huggingface.co/mlboydaisuke/sam3-CoreAI-official` | iPhone 端上跑開放詞彙分割 |
| PuruPuruPNGTuber | `github.com/rotejin/PuruPuruPNGTuber` | 本地 Web app 做進階 PNGTuber |
| Manga Monochrome Normalizer | `github.com/dr1610/manga-monochrome-normalizer` | 漫畫網點/防摩爾紋處理 |

### 開發者 CLI / Agent
| 工具 | 連結 | 用途 |
|---|---|---|
| gogcli | `gogcli.sh` | Google Workspace CLI（作者被 Google 開除後仍維護） |
| gemini-skills | `github.com/google-gemini/gemini-skills` | Omni Flash 影片編輯 skill（`npx skills add`） |
| HyperFrames | `github.com/heygen-com/hyperframes` | Write HTML, Render video，為 agent 設計 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Krea 2 Raw / Turbo | 圖像模型 (12B) | GitHub / HF | 開源出圖，Raw 微調 + Turbo 快速推論（5090 約 15 秒/張） | @krea_ai |
| Krea 2 NVFP4-AWQ | 量化模型 | HF `sakamakismile/...` | 8GB VRAM 就能跑 Krea 2 Turbo | @Tono_Ken3 |
| DeepSeek V4-Flash | LLM (284B MoE/6 active) | HF GGUF | IQ2_XXS 可上 3090+64GB；REAP 180B 上單 Spark | @ItsmeAjayKV / @sudoingX |
| GLM-5.2 | LLM (開源旗艦) | Unsloth GGUF / NVIDIA NVFP4 | 對標 GPT-5.5/Opus 4.8，成本低 70-80% | @philipkiely |
| Ornith-1.0-35B | LLM (3B active MoE) | HF | spark-bench TrueScore 87.5，單 Spark Q4_K_M | Wesche |
| LongCat-2.0 | LLM (1.6T/48B active) | OpenRouter | agentic coding，1M context + LSA 稀疏注意力 | @Meituan_LongCat |
| Gemma 4 31B | 多模態 LLM | Cerebras | 1,800+ tok/s 超快圖文工作流 | @cerebras |
| LFM2.5-230M | 極小 LLM (230M) | HF `LiquidAI` | 手機/機器人/邊緣裝置 agentic 任務 | @liquidai |
| LTX-2.3 3DREAL-LoRA | 影片 LoRA | HF `fal/...` | render-to-real，保留構圖與運鏡 | @fal |
| free-splatter.cpp | 3DGS 工具 | GitHub | 影像轉 3D 場景，Vulkan/WebGL，無需 Python | @jichiep |
| gogcli | 開發者 CLI | `gogcli.sh` | Google Workspace 命令列操作 | @steipete |

---

## 🎯 本週趨勢

1. **NVFP4 + REAP 成為「巨模型上小盒子」的黃金組合。** 本週幾乎每個大模型部署貼文都繞著這兩者：NVFP4 KV cache 把 context 翻倍（DeepSeek 直上 1M）、REAP 剪枝把參數量硬砍（284B→180B、GLM 504B）。對選型的實際意義是——**「能不能跑」正在從「有多少 VRAM」轉為「用什麼量化 recipe」**，而 recipe 本身已經 repo 化、可被 coding agent 直接讀取複製（多篇貼文直接說「把 repo 貼給你的 agent 問問題」）。

2. **DGX Spark 正在變成社群級的巨模型基準機。** 從 DeepSeek V4-Flash、GLM-5.2 到 Ornith，評測與 recipe 全都以「單台/雙台 DGX Spark」為單位標定 tok/s。這形成了一個可比較的硬體基準線；同時 M5 Ultra Mac Studio（傳 768GB RAM）與 M3 Ultra 256GB 走 unified memory 另一條路，兩條路線的 tok/s 與可裝載尺寸將是下半年選機的核心指標。

3. **開源圖像模型的競爭力不在模型本身，而在「生態成形速度」。** Krea 2 從釋出到量化、LoRA、自訂節點、訓練 GUI 全套齊備只花了幾天，社群產出甚至超越官方 template。這意味著**評估一個開源模型該看的是「day-0 diffusers 支援 + 蒸餾快版 + 可訓性」**這組配套，而非單張出圖品質——生態越好組裝，模型的實際壽命越長。

4. **AI 影像從「運氣抽卡」轉向「可控 previs」。** Blender/iPhone AR 擋戲 + Seedance 2.0 的組合，以及 Gemini Omni「mannequin 化」去除風格污染的技巧，都指向同一件事：**鏡頭運動與角色一致性正被工程化**。純靠 prompt 賭鏡頭的時代在退場，會 previs（哪怕只是方塊）的人握有明確優勢。

5. **無審查（abliterated）與創作特化模型形成穩定支流。** huihui-ai 這週連發 DeepSeek V4-Flash、GLM-5.2、Gemma 4、Qwen3-VL 的 abliterated GGUF，加上 MN-Oblivion-26B 這類創作寫作特化模型與大量 NSFW Krea 2 workflow/LoRA。值得注意的是這些無審查 text encoder 開始被拿來**替換其他模型的元件**（如替換 ideogram-4 的 text_encoder 以解除約束），顯示元件級混搭正在成為繞過限制的常規手法。

---

需要的話，我可以把這份週報寫成 `.md` 檔案存到指定路徑，或再針對某一條主線（例如 DGX Spark 部署 recipe 或 Krea 2 LoRA 訓練）展開成更完整的操作筆記。
