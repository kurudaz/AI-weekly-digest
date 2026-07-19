# OpenCode 週報 - 2026.07.17

> 本週書籤的重心明顯不在「又有新模型」，而在**如何把既有的旗艦模型塞進你手上的硬體**。1-bit / NVFP4 量化、llama.cpp + MTP、WebGPU/MLX 端側推論一起發力，把 27B 甚至 295B 的模型推進了手機、瀏覽器與單張 5090。影片端則是 LTX 2.3 與 Krea 2 兩條生態鏈在一週內冒出成串的 LoRA 與 ComfyUI 節點。

---

## 🔥 本週焦點

### 1. Bonsai 27B —— 27B 級模型第一次「跑進手機與瀏覽器」

這是本週串起最多帳號的主線。`@PrismML` 發布 **Bonsai 27B**（基於 **Qwen3.6 27B**）作為 Bonsai 家族的多模態旗艦，主打 multi-step reasoning、structured tool use 與 long-context workflow，並宣稱是「第一個能在手機上運行的 27B 級模型」。接著社群的連鎖反應很快鋪開：

- **端側落地**：`@LocallyAIApp` 直接讓 1-bit Bonsai 27B 跑在 **iPhone 17 Pro / iPhone Air / 部分 iPad** 上，底層由 **MLX** 驅動。
- **量化數字**：`@xenovacom` 給出關鍵數據 —— 1-bit 量化把模型從 **54GB 壓到 3.8GB（−93%）**，仍保留約 **90% 智力**；再靠 Fable 5 與 GPT-5.6 Sol 手寫的 **custom WebGPU kernels**，直接在**瀏覽器**裡本地推論。
- **去審查版**：`@dealignai` 隔天就放出 `Bonsai-27b-Ternary-CRACK-GGUF` 與 `Bonsai-27b-1bit-CRACK-GGUF`（refusals removed / abliterated）。
- **同源熱度**：底模 **Qwen3.6-27b** 兩天內被下載 30 萬次；`@Tono_Ken3` 進一步把 Huihui 的無審查加工 + **ThinkingCap**（最多砍掉 90% thinking token）燒成 **NVFP4**，模型檔約 20GB。

> 意義：這代表「模型智力」與「可部署性」正在解耦。當 3.8GB 能保住九成能力，選型的第一問題不再是 benchmark，而是「它能不能在我口袋裡的裝置上跑」。
> 來源：[PrismML](https://x.com/PrismML) ｜ [Xenova](https://x.com/xenovacom) ｜ [Locally AI](https://x.com/LocallyAIApp) ｜ [dealignai on HF](https://huggingface.co/dealignai/Bonsai-27b-1bit-CRACK-GGUF)

### 2. 巨型模型下放單卡 —— Hunyuan Hy3（295B 1-bit）、GLM-5.2 NVFP4、Gemma 4 QAT+MTP

如果說 Bonsai 是「小模型鑽進手機」，那本週另一半故事是「超大模型鑽進單張消費卡」，共通配方是 **llama.cpp + MTP + 低位元量化**：

- **Tencent Hunyuan Hy3**：釋出 **1-bit & 4-bit** 版本，把 **295B** 的 flagship-scale 模型做到**單 GPU 可服務**，搭配 llama.cpp 啟用 MTP。`@EMostaque` 實測 **1-bit 量化下 SWE-Bench Verified 75.4% / SWE-Bench Pro 53.9%**，掉分極小，且 **88GB 就能在 Macbook Max 上跑**，並喊出「why not run everything binary?」。
- **GLM-5.2 NVFP4**（`@0xSero`）：號稱 no pruning / no quantising、model card 不變，**單流 110 tok/s**；朋友以 **vllm-Moet** 在 **5090 + DDR5** 上跑到 full DS4-Flash、**38.5 tok/s**。
- **Gemma 4 升級**（`@ggerganov` × Google Gemma）：以社群修正為基礎導入 **QAT + MTP**，用 llama.cpp 取得明顯加速，新權重上架 HF。
- **Nemotron labs 3 puzzle 75B-A9B NVFP4**（`@aijoey`）：**75B 總量 / 9.3B active**、mamba + MoE、config 內 **256k context**，剛好塞進一台 **DGX Spark** 還有餘裕。

> 意義：`SWE-Bench 只掉幾個百分點` 這種數字，實質上宣告了 1-bit/NVFP4 已經從「玩票」變成「可上線」。對自建部署的人來說，這一週最該更新的認知是——旗艦級 coding 能力，現在真的能跑在一張卡或一台 Mac 上。
> 來源：[Hunyuan Hy3 / Emad](https://x.com/EMostaque) ｜ [GLM-5.2 NVFP4](https://github.com/kacper-daftcode/vllm-Moet) ｜ [Gemma 4 collection](https://huggingface.co/collections/google/gemma-4)

### 3. LTX 2.3 生態爆發 —— 從「文生影片」轉向「可控的後期工具」

LTX 2.3 這週一口氣冒出多支 LoRA，主題高度一致：**相機控制**與 **render-to-real**。

- **CrossView LoRA**（`@wildmindai` / Cseti）：丟一段原始 clip + 一句相機角度 prompt，就能把同一場景從**完全不同的視角**重渲染，主體不變、鏡頭移動——等於從單機位素材反推出多機位。`@DeepForest_Inc` 實測生成 **8 個不同角度、拼成 9 宮格**。
- **3DREAL Strong V2**（`@OdinLovis` × fal）：把 blocky 的 3D render 轉成 photoreal 影像，同時**保留原本的相機、佈局與時間軸**，權重開源（`fal/LTX-2.3-3DREAL-LoRA`）並附 ComfyUI workflow / 線上 endpoint。
- **Multiple-Subject-Reference V2**：一次餵最多 **5 張參考圖**，角色身份與服裝終於能保持一致，惱人的 flicker 也大致消失。
- **LTX 2.3 I2V | Ollama Enhancer**：用 **Ollama** 從短句自動擴寫 prompt，再走 2 Pass + Upscale 出片。

> 意義：影片生成的競爭正在從「畫面好不好看」轉到「可不可控」。CrossView 的多機位、3DREAL 的 render-to-real，本質上是把 AI 影片插進既有的 3D/VFX pipeline 當後期工具，而不是取代它。
> 來源：[CrossView LoRA](https://huggingface.co/Cseti/LTX2.3-2B_IC-LoRA-CrossView-Prompt) ｜ [3DREAL LoRA](https://huggingface.co/fal/LTX-2.3-3DREAL-LoRA)

### 4. Krea 2 一週內長出完整 ComfyUI 工具鏈

Krea 2 發布後，社群的節點化速度快得驚人，identity / merge / LoRA / HDR / outpaint 幾乎一次補齊：

- **Identity Edit 1.1**（`@conradlocke` / PhotoX86）：搭配 `ComfyUI-Krea2-Ostris-Edit` 節點可接**三張參考圖**（人物、服裝、場景各一），比舊的只能接兩張的節點更完整；`@SlipperyGem` 指出對**風格化輸入**效果極佳，但目前只在 **1:1** 長寬比穩定。
- **模型工具**（`@isiwork`）：`ComfyUI-Krea2-Model-Merge` 做模型合併、`ComfyUI-Krea-LoRA-Tools` 支援**分層權重調整**與多 LoRA 合併存檔。
- **Scene-Linear HDR LoRA**（LAXMAYDAY，for Krea 2 Turbo）：生成 **float radiance 值可 >1.0** 的真 scene-linear HDR，可匯出 `.exr` 供 render / VFX / IBL 使用。
- **Krea 2 Outpaint** workflow：無需 prompt 即可依「zoom out」等指令擴圖。

> 意義：模型發布後的實際價值，越來越取決於**社群工具化的速度**。Krea 2 一週內就從「一個模型」變成「一套可用工作流」，這正是 ComfyUI 生態的護城河。
> 來源：[Krea2 Identity Edit](https://huggingface.co/conradlocke/krea2-identity-edit) ｜ [ComfyUI-Krea-LoRA-Tools](https://github.com/isiwork/ComfyUI-Krea-LoRA-Tools)

---

## 🛠️ 工具清單

### 🧠 本地 LLM / 量化
| 工具 | 連結 | 用途 |
|---|---|---|
| Bonsai 27B（1-bit / Ternary CRACK GGUF） | huggingface.co/dealignai/Bonsai-27b-1bit-CRACK-GGUF | 54GB→3.8GB 的手機/瀏覽器級 27B 多模態模型（含去審查版） |
| Hunyuan Hy3（1-bit/4-bit, 295B） | x.com/TencentHunyuan | 單 GPU 服務的 295B 旗艦，llama.cpp + MTP |
| GLM-5.2 NVFP4 / vllm-Moet | github.com/kacper-daftcode/vllm-Moet | 5090 上跑 GLM-5.2，單流 110 tok/s |
| Gemma 4（QAT + MTP） | huggingface.co/collections/google/gemma-4 | 社群修正後的加速版 Gemma 4，配 llama.cpp |
| Huihui-ThinkingCap-Qwen3.6-27B-abliterated-NVFP4 | huggingface.co（sakamakismile） | 砍 thinking token + 無審查 + NVFP4 的 Qwen3.6-27B，約 20GB |
| 2Wild Vision Mode | github.com/tonyd2wild/2Wild-Vision-Mode | 用 Qwen 3.5 給任何純文字模型「裝上眼睛」，2GB VRAM |
| FLUX.2-9B-klein text-encoder 瘦身 | x.com/kgonia7 | 砍掉 38% text encoder，768×768 可塞進 16GB VRAM |

### 🎬 影片 / 世界模型
| 工具 | 連結 | 用途 |
|---|---|---|
| LTX2.3 CrossView LoRA | huggingface.co/Cseti/LTX2.3-2B_IC-LoRA-CrossView-Prompt | 單 clip 生成多機位視角 |
| LTX-2.3-3DREAL-LoRA | huggingface.co/fal/LTX-2.3-3DREAL-LoRA | blocky 3D render 轉 photoreal，保留相機/時間軸 |
| LTX2.3 Multiple-Subject-Reference V2 | x.com（LTX2.3 LoRA） | 最多 5 張參考圖，角色/服裝一致性 |
| LingBot-World 2.0 | x.com/robbyant_brain | 開源互動世界模型，720p @ 60 FPS |
| Tencent HY-World 2.1 | x.com/DylanTFWang | 可行走/互動的真 3D 世界 |
| AnimeGen | x.com（AnimeGen） | 免費開放的動畫特化文生影片模型 |
| ComfyUI First/Last frame Extractor | #ComfyUIFirstframeLastframeExtractor | 抽取影片首尾幀，支援 mp4/gif |

### 🖼️ 圖像 / 編輯 / ComfyUI 節點
| 工具 | 連結 | 用途 |
|---|---|---|
| NVIDIA PiD v1.5 | github.com/nv-tlabs/PiD | 最快開源 upscaler，修色偏，支援 FLUX/FLUX2/Qwen-Image/Z-Image |
| ComfyUI-Krea2-Model-Merge | github.com/isiwork/ComfyUI-Krea2-Model-Merge | Krea 2 模型合併節點 |
| ComfyUI-Krea-LoRA-Tools | github.com/isiwork/ComfyUI-Krea-LoRA-Tools | LoRA 分層權重 + 多 LoRA 合併 |
| krea2-scene-linear-hdr-lora | huggingface.co/LAXMAYDAY/krea2-scene-linear-hdr-lora | 生成可輸出 .exr 的 scene-linear HDR |
| comfyui_qwen_edit_pixel_perfect | github.com/oron1208/comfyui_qwen_edit_pixel_perfect | 修 Qwen-Image-Edit-2511 的像素位移（AKAZE+RANSAC） |
| Anima-ControlNet-VACE-Depth | huggingface.co/TaihoC/Anima-ControlNet-VACE-Depth | Anima 的 depth ControlNet |
| Muse Image | Meta AI app | MSL 首款 agentic 圖像模型（7 項測試勝 Nano Banana 2） |

### 🔊 音訊
| 工具 | 連結 | 用途 |
|---|---|---|
| SAORI（Stable Audio Open ReImpl.） | huggingface.co/spaces/alfredplpl | 商用可用的聲音生成，輸出著作權可商用 |
| MuScriptor（kyutai） | x.com/kyutai_labs | Audio-to-MIDI，把混音拆成分軌 MIDI |
| sound-efect | github.com/animede/sound-efect | 簡易效果音生成 app |

### 🧊 3D / Blender / CLI
| 工具 | 連結 | 用途 |
|---|---|---|
| AutoRemesher 1.0 | 3dnchu.com（+ github.com/adriflex/autoremesher-blender-bridge） | 高速高品質 quad remesh，MIT 授權，已有 Blender addon |
| GNM | github.com/google/GNM | Google 開源幾何工具，可在 Blender 用 vertex groups 遮罩 |
| charsheet | github.com/animede/charsheet | 以 Diffusers 生成角色設定表，含去背 |
| Anime2.5DRig | github.com/izumix77/Anime2.5DRig | 楕圓體 3D shell 再投影臉/髮的 2.5D rig |
| flash-attention-windows-wheel | huggingface.co/lldacing | Windows 用 flash attention 預編譯 wheel，加速生成 |
| Higgsfield MCP/Plugin for AE | x.com/higgsfield | After Effects 的 MCP 外掛，Fable 5 驅動，對話式做動態圖形 |
| camkeys | beyonddev.gumroad.com/l/camkeys | 自動以頭部為中心壓平場景景深 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Bonsai 27B | 本地 LLM（多模態） | HF / Locally AI app / 瀏覽器 | 手機・瀏覽器端運行的 27B，1-bit 3.8GB 保 90% 智力 | @PrismML / @xenovacom |
| Hunyuan Hy3 | 本地 LLM（295B） | llama.cpp + GGUF | 88GB 於 Macbook Max 跑 295B，1-bit SWE-Bench 75.4% | @TencentHunyuan |
| GLM-5.2 NVFP4 | 本地 LLM | github（vllm-Moet） | 5090 單卡 110 tok/s | @0xSero |
| Nemotron 3 puzzle 75B-A9B | 本地 LLM（MoE+mamba） | NVFP4 權重 | 單台 DGX Spark、256k context | @aijoey |
| PiD v1.5 | 圖像 upscaler | github.com/nv-tlabs/PiD | 多模型通用高倍放大、修色偏 | @DylanTFWang / @aisearchio |
| LTX2.3 CrossView LoRA | 影片 LoRA | HF（Cseti） | 單 clip → 多機位 | @wildmindai |
| LTX-2.3-3DREAL LoRA | 影片 LoRA | HF（fal）+ ComfyUI | 3D render → 擬真 | @OdinLovis |
| Krea2 Identity Edit 1.1 | 圖像編輯節點 | HF + ComfyUI 節點 | 人/衣/景三參考圖編輯（1:1 佳） | @SlipperyGem / PhotoX86 |
| SAORI | 音訊生成 | HF Space | 商用可用聲音生成 | @alfredplpl |
| MuScriptor | Audio-to-MIDI | kyutai | 完成曲反推分軌 MIDI | @taziku_co |
| AutoRemesher 1.0 | 3D 工具 | 官網 / Blender addon | 高速 quad remesh（MIT） | @ymt3d |
| 2Wild Vision Mode | 視覺外掛 | github | 給純文字模型加視覺，2GB VRAM | @Tech2Wild |

---

## 🎯 本週趨勢

**1. 1-bit / NVFP4 量化正式從「實驗」跨進「可上線」。**
本週最大的訊號不是新模型，而是量化掉分小到可以忽略：Hy3 在 1-bit 下 SWE-Bench Verified 仍有 75.4%，Bonsai 3.8GB 保住 90% 智力。對選型/部署的實際意義是——benchmark 榜單的權重正在下降，「這個能力能塞進哪塊硬體」成了第一決策變量。Emad 那句「why not run everything binary?」已經不只是口號。

**2. 推論前線下移到手機、瀏覽器與消費級單卡。**
MLX 讓 Bonsai 跑在 iPhone、WebGPU 讓它跑在瀏覽器分頁裡、`@UtaAoya` 用一張 8 萬日圓的 RTX 5060 Ti 16GB 就把 Qwen3.6-35B-A3B 跑到 ~105 tok/s。端側推論不再是玩具展示；這也意味著「隱私敏感 / 離線 / 零 API 成本」的應用場景在這一週真正變得可行。

**3. Abliteration（去審查）幾乎成為熱門模型的標準衍生版。**
Bonsai 出 CRACK GGUF、Qwen3.6-27B 出 Huihui abliterated，發布後幾天內必有「refusals removed」版本。這反映本地部署社群把**可控性/可改造性**看得比雲端式的安全對齊更重——對做產品的人是雙面刃：拿得到更聽話的模型，但責任與合規也全落回自己身上。

**4. 影片與圖像模型的價值，越來越由「社群節點化速度」決定。**
Krea 2 一週內長出 identity edit / merge / LoRA tools / HDR / outpaint 全套 ComfyUI 節點，LTX 2.3 同期冒出 CrossView、3DREAL、multi-subject 一串 LoRA。模型本體的領先窗口越來越短，真正的護城河是它周邊工具鏈成形的快慢。做選型時，值得把「這個模型的 ComfyUI/工具生態有多活躍」當成硬指標。

**5. 生成式影片明確轉向「可控的後期工具」而非「一鍵成片」。**
CrossView 的多機位、3DREAL 的 render-to-real、Multiple-Subject 的多參考一致性、LTX 的 render→real workflow，共同點都是**保留相機、佈局、時間軸**去嵌進既有 3D/VFX pipeline。這條路線比「純文字生成整支片」更務實，也更可能先在專業製作流程裡落地。
