# OpenCode 週報 - 2026.07.31

> 本週書籤約 90 條，扣掉純作品展示後，真正有「可下載、可安裝、可執行」價值的約 55 條。整週最清楚的訊號是：**開源生態不再比拚「誰的新模型更大」，而是比拚「怎麼把既有模型拆小、加速、變成可控的後製工具」**。

---

## 🔥 本週焦點

### 1. LTX-2.3 IC-LoRA 生態爆炸：開源影片模型長出了一整套「後製軍火庫」

這是本週最完整的一條主線。LTX-2.3 發布後，社群沒有去卷「生成更炫的片段」，而是集體轉向**修片**——把 IC-LoRA（in-context LoRA）當成可疊加的後製濾鏡層，一週之內湊出了一整套傳統上要靠 Nuke / DaVinci 做的活。

| LoRA | 做什麼 | 關鍵細節 |
|---|---|---|
| **LensRemover IC-LoRA** | 移除鏡頭光暈、veiling glare、鏡頭髒污與其他光學瑕疵 | 22B 底模，強調「移除瑕疵但不動底下的場景」 → [JanKanta/ltx-2.3-22b-ic-lora-lens_remover](https://huggingface.co/JanKanta/ltx-2.3-22b-ic-lora-lens_remover) |
| **Black Magic LoRA** | 把暗部／曝光不足的素材救成資訊豐富的畫面 | 作者明講：保留構圖與運動、**腦補**合理細節，「for creative VFX and night shots, **not for forensic recovery**」 → [FuzzPuppy/LTX-2.3-Black-Magic-LoRA](https://huggingface.co/FuzzPuppy/LTX-2.3-Black-Magic-LoRA) |
| **IC-LoRA Relight**（官方 @ltx_io） | 在外景片段裡**重寫太陽**：方向、硬度、時間 | 用一顆小的「光線方向球」當控制訊號，內建 **12 種訓練好的 looks × 8 個方向** |
| **Clean Plate IC-LoRA**（官方） | 移除畫面中的人、行人、車輛並重建背後的空景 | **全畫面移除、不需要 mask**；保留建築、地面標線、植被。@DeepForest_Inc 拿澀谷全向十字路口實測極限 |
| **PinkCherry NSFW** | LTX-2.3 的 distil fine-tune | rank 384，附帶可用的音訊生成 → `SexGod1979/PinkCherry_NSFW_LTX23` |

配套的工程側也同步跟上：kijai 已對 ComfyUI 送出 **PR #15129 支援 PrunaVAED**（更快的 LTX-2.3 decoder）；@ModelCollapse38 示範了目前最實用的管線——**Blender 建簡單船隻場景 → 輸出 render + depth map → 當作結構引導餵給 LTX-2.3 IC-LoRA → 產出 14 秒成片**。

**判讀**：IC-LoRA 正在變成影片模型的「圖層系統」。它的價值不在生成力，而在**局部、可逆、可疊加的編輯語意**——這是 AI 影片從 demo 走向產線的必要條件。如果你要選一個影片開源棧投入時間，這週的證據明顯指向 LTX-2.3。

---

### 2. Kimi-K3 極限量化競賽 vs. Qwen3.6-27B 的「甜蜜點」共識

`moonshotai/Kimi-K3` 一放出來，日本與英文社群立刻進入量化軍備賽，方向卻是兩個極端。

**極端一：把巨獸塞進單機**
- `unsloth/Kimi-K3-GGUF` 第一時間跟上
- @ktam72 實測 **`kernelpool/Kimi-K3-2bit-UVMAX` 在 M5 Max MacBook Pro 128GB 上跑出 1.1 t/s** ——能動，但只能說是「能動」
- @Iamwonderingif 同步推進 2bit 修正與 1bit：[`GrEarl/Kimi-K3-GGUF-IQ1_S`](https://huggingface.co/GrEarl/Kimi-K3-GGUF-IQ1_S)，但**必須等 [llama.cpp PR #26185](https://github.com/ggml-org/llama.cpp/pull/26185) merge 才能跑**

**極端二：27B 才是實際生產力**
@ItsmeAjayKV 那句「**Qwen3.6-27b is all you need no matter how much VRAM you have**」本週被大量轉引，附帶一張直接可抄的對照表：

| VRAM | 量化 |
|---|---|
| 16GB | Tq3_4s |
| 24GB | Q4_K_M |
| 32GB | Q6_K |
| 38GB | Q8_0 |
| 96GB | FP16 |

而 @loktar00 的 BF16 vs Q4 對照給了反面證據：同一 prompt 下 **Q4 第一次輸出全白、補跑三次其中第二次仍全白、第三次直接 error**——低位元量化在圖像/多模態任務上的崩壞比純文字任務嚴重得多。

同一個 27B 底模的衍生版本本週滿地都是：
- [`prism-ml/Ternary-Bonsai-27B-gguf`](https://huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf)：Qwen3.6-27B 的**三值（ternary）壓縮，塞進 4GB VRAM**
- [`sakamakismile/Huihui-ThinkingCap-Qwen3.6-27B-abliterated-NVFP4`](https://huggingface.co/sakamakismile/Huihui-ThinkingCap-Qwen3.6-27B-abliterated-NVFP4)：ThinkingCap 把 Qwen3.6 過長的 thinking **砍半但維持智力**；@Tono_Ken3 指出這個無審查版**兩週 6700 次下載，超過官方 NVFP4 版的 5700 次**——他的解讀是「解除審查沒有 nerf 掉性能」

**判讀**：Kimi-K3 的 1bit/2bit 目前是**炫技與研究價值**大於實用價值（1.1 t/s 無法互動）。真正該投產的是 27B 級距，而且要優先看 **NVFP4** 而非 GGUF——見趨勢段。

---

### 3. 「世界模型在家跑」與 3D 全面遷入瀏覽器

兩件原本各自獨立的事，本週撞在了一起。

**世界模型下放到消費級**：
- **ABot World 0.5B** 發布——「world models at home, running in **real-time on consumer GPU**」。餵一張初始圖，用**鍵盤即時操控**視角，可本地跑也可直接在 Space 上玩：[acvlab/abot-world-interactive](https://huggingface.co/spaces/acvlab/abot-world-interactive)。@alfredplpl 實測連**動畫風場景**都能動起來
- **Wonder by Adobe**：即時互動 3D 世界導覽，**單張圖產出分鐘級 rollout、恆定 latency**，支援 I2V / V2V，底層是 **Wan2.1**。宣稱可以「把任何影片從新機位重拍一次」→ `wonder-world-model.github.io`

**3D 遷入瀏覽器**：
- **SuperSplat / PlayCanvas**：3D Gaussian Splatting 直接在網頁串流大型實景空間，**輕量資料 + 自動生成碰撞** → `superspl.at`
- **InfiniSplat**（HF Space, PLUS-WAVE）與 **[awesome-4dgs](https://github.com/awesome-4dgs/awesome-4dgs)**（4DGS / dynamic 3DGS 的論文・程式碼・資料集彙整）
- **[img2threejs v1.3](https://github.com/hoainho/img2threejs)**：一張照片 → **procedural Three.js 程式碼，不產 mesh、不手動建模**；v1.3 主攻幾何重建、材質生成與驗證。@Saccc_c 已在 Codex 裡直接串起來用
- Chrome 實驗特性 **html-in-canvas**：把任意 DOM 元素（dashboard、表單、整個 app UI）當成 **3D 貼圖**直接畫進 canvas，含完整 CSS

**判讀**：這條線的共同點是**把「3D 內容」的成本從資產製作轉移到推論**。img2threejs 產程式碼而非 mesh 尤其關鍵——輸出是可 diff、可版控、可讓 agent 繼續改的文字，這才是能進工程流程的形態。

---

### 4. Agent-native 工具鏈：從「讓 AI 操作人類 UI」翻轉成「為 AI 設計介面」

本週有幾則獨立的貼文，其實在講同一件事。

- **Anthropic @trq212**：「我們把 Claude Code system prompt 砍掉了約 **80%**」，並整理出給 Claude 5 世代寫 system prompt / Skills / CLAUDE.md 的新規則。核心是新一代模型不需要被過度指導
- **@_3912657840（角煮星丸）**的洞察最直白：「Codex 去操作**為人類設計**的 UI，要一次次做圖像辨識、抓座標、模擬滑鼠，效率極差……那如果前提就是 Codex 操作，**人類用的 UI 根本不需要存在**吧？」他據此做了一個**沒有人類介面、只給 Codex 用的漫畫製作工具**，丟進劇本就自動分鏡
- **[jtydhr88/japanese-composition-skills](https://github.com/jtydhr88/japanese-composition-skills)**：一組 Claude Code Skills，讓 Claude 直接產出**可編輯的多軌 MIDI**——含 velocity、延音踏板、tempo 變化，丟進任何 DAW 繼續編。從 9 位日本音樂製作人的手法蒸餾而來
- **Remotion Agent Skills**：`npx skills add remotion-dev/skills`，純 prompt 出 motion graphics，**不需要 MCP、不需要影片生成模型**，全部是程式碼
- **[MengTo/Skills](https://github.com/MengTo/Skills)**：開源 Three.js 遊戲開發 Skills，含相機控制、VFX、音訊、怪物素材、戰鬥系統，可玩 demo `vesperfall.mengto.chatgpt.site`
- **Blender MCP** 的兩則實測：@explosss1ve 描述的迴圈是「模型寫 bpy → blockout base mesh → 佈景 → 轉相機 → render → **讀自己算出來的那張圖**」；@AiNamanari33743 用 Blender MCP × Claude Fable5 做動畫 previz
- **@MakeAI_CEO**：Claude Code × HyperFrames 做影片，主張「影片編輯軟體可以不用開了」

**判讀**：Skills 這個載體本週明顯壓過了 MCP。差別在於 **MCP 是給 agent 一個工具的 API，Skills 是給 agent 一套領域 know-how**——上面幾個案例（日式編曲手法、Three.js 遊戲架構、Remotion 動畫）都是後者。要讓 agent 產出專業水準的東西，缺的從來不是工具存取權，是判斷力。

---

### 5. ComfyUI 的推論加速年：蒸餾、量化、換 decoder 三管齊下

本週 ComfyUI 側最有價值的不是新節點，是**同一批模型跑得更快**。

**[TheStageAI/ComfyUI-Qlip](https://github.com/TheStageAI/ComfyUI-Qlip)** 是最完整的一組：編譯 + 量化 + 自訂 CUDA kernel + 記憶體管理，支援 **NVFP4 量化與動態 LoRA 切換**，實測加速——

| 模型 | 加速 |
|---|---|
| FLUX | 1.7× |
| Wan 2.2 | 1.5× |
| Z-Image | 1.8× |
| LTX 2.3 | 1.8× |

搭配的 [`TheStageAI/Elastic-Wan2.2-I2V`](https://huggingface.co/TheStageAI/Elastic-Wan2.2-I2V) 更值得注意：**用一個 LoRA 把 high/low-noise 兩個 expert 合併進單一 14B transformer**，在 RTX 5090 上以 NVFP4 + FP4 attention 取得 **2.61× 加速、峰值 VRAM ~25GB，且 VBench consistency 與原始雙 expert 版持平**。

同時，**SeedVR2** 完成了瘦身：@SlipperyGem 指出有人把 **7B 蒸餾成 1.4B**（[`lvladikov/SeedVR2-1.4B`](https://huggingface.co/lvladikov/SeedVR2-1.4B)），@Machinedelusion 隨即放出原生節點 **[filliptm/ComfyUI-FL-SeedVR2](https://github.com/filliptm/ComfyUI-FL-SeedVR2)**——自動下載模型、batch 支援、alpha 通道保留、ComfyUI 原生記憶體管理。SeedVR2 長期以來的問題就是「效果好但太重」，這一刀砍在點上。

低 VRAM 側還有 **[deepbeepmeep/Wan2GP](https://github.com/deepbeepmeep/Wan2GP)**（「a fast AI video generator for the GPU poor」）本週再度被大量轉發：支援 Wan 2.1/2.2，**最低 6GB VRAM，舊的 RTX 10 系與 8GB 筆電也能跑，全程本地**。

---

## 🛠️ 工具清單

### LTX-2.3 生態

| 工具 | 連結 | 用途 |
|---|---|---|
| LTX-2.3 LensRemover IC-LoRA | [HF](https://huggingface.co/JanKanta/ltx-2.3-22b-ic-lora-lens_remover) | 移除鏡頭光暈、眩光、鏡頭髒污等光學瑕疵 |
| LTX-2.3 Black Magic LoRA | [HF](https://huggingface.co/FuzzPuppy/LTX-2.3-Black-Magic-LoRA) | 暗部／欠曝素材補光補細節（創意用途，非取證） |
| LTX-2.3 IC-LoRA Relight | @ltx_io 官方發布 | 用光向球重寫太陽方向／硬度／時間，12 looks × 8 方向 |
| LTX-2.3 Clean Plate IC-LoRA | @ltx_io 官方發布 | 無 mask 移除人／車並重建背景空景 |
| PinkCherry NSFW LTX-2.3 | `SexGod1979/PinkCherry_NSFW_LTX23` | rank 384 distil fine-tune，含音訊 |
| PrunaVAED 支援 | [ComfyUI PR #15129](https://github.com/Comfy-Org/ComfyUI/pull/15129) | 更快的 LTX-2.3 decoder（kijai） |

### ComfyUI 節點與工作流

| 工具 | 連結 | 用途 |
|---|---|---|
| ComfyUI-Qlip | [GitHub](https://github.com/TheStageAI/ComfyUI-Qlip) | NVFP4 量化 + 動態 LoRA 切換，FLUX/Wan2.2/Z-Image/LTX2.3 加速 1.5–1.8× |
| ComfyUI-FL-SeedVR2 | [GitHub](https://github.com/filliptm/ComfyUI-FL-SeedVR2) | SeedVR2 1.4B 原生修復與放大，自動下載、batch、alpha 保留 |
| ComfyUI-NO8D-controls | [GitHub](https://github.com/no8d/ComfyUI-NO8D-controls) | style selector 節點，純 prompt 切換 Ghibli／Cinematic Anime／70s Anime 等風格 |
| ComfyUI-PSD2Layer | GitHub（原文未附完整連結） | PSD 逐圖層拆解、個別處理後依原位置與群組階層重組 |
| MAGE + KREA 雙 pass 工作流 | [HF](https://huggingface.co/realrebelai/LOW_VRAM_Workflows/blob/main/Dual%20Pass%20Workflows/MAGE%20%2B%20KREA.json) | 第二 pass 走 Krea-2-Turbo，**4 steps / denoise 0.35** 精修細節；降 denoise 保人物相似度 |
| Krea 2 Identity & Qwen3-VL Easy Workflow | Civitai | 保持角色同一性，移植到新背景／場景 |
| Wikked Krea2 Image2Image | Civitai | 極簡 i2i，讀圖即跑，支援自動 prompt 與 LoRA |
| comfyui-krea2-controlnet（改） | @tori29umai 改造版 | 加上 start/end percent；只繼承大致構圖、細節放任補完 |
| VNCSS custom node | note.com（@jun_kongo 實測文） | ComfyUI 自訂節點試用報告 |

### 本地 LLM 與量化

| 工具 | 連結 | 用途 |
|---|---|---|
| Kimi-K3 | [HF](https://huggingface.co/moonshotai/Kimi-K3) | Moonshot 本週旗艦開源模型 |
| Kimi-K3-GGUF | [HF](https://huggingface.co/unsloth/Kimi-K3-GGUF) | unsloth 官方 GGUF |
| Kimi-K3-2bit-UVMAX | [HF](https://huggingface.co/kernelpool/Kimi-K3-2bit-UVMAX) | 2bit；M5 Max 128GB 實測 1.1 t/s |
| Kimi-K3-GGUF-IQ1_S | [HF](https://huggingface.co/GrEarl/Kimi-K3-GGUF-IQ1_S) | 1bit；需 [llama.cpp PR #26185](https://github.com/ggml-org/llama.cpp/pull/26185) |
| Ternary-Bonsai-27B | [HF](https://huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf) | Qwen3.6-27B 三值壓縮，**4GB VRAM 可跑** |
| ThinkingCap-Qwen3.6-27B-abliterated-NVFP4 | [HF](https://huggingface.co/sakamakismile/Huihui-ThinkingCap-Qwen3.6-27B-abliterated-NVFP4) | 無審查 + thinking 長度砍半，RTX 50 系專用 |
| Nanbeige4.2-3B | [HF](https://huggingface.co/Nanbeige/Nanbeige4.2-3B) | 4–8GB VRAM 級距，適合打標等輔助任務 |
| Agents-A1-4B-NVFP4 | [HF](https://huggingface.co/sakamakismile/Agents-A1-4B-NVFP4) | 4B 但對話 EQ 異常高（coding 不行），附 MTP |
| Agents-A1-4B-Heretic-GGUF | [HF](https://huggingface.co/NullpoLab/Agents-A1-4B-Heretic-ARA-Refusals8-GGUF) | 同上的解除拒答版 |
| GLM-5.2 NVFP4+AQLM Triple DGX Spark | [GitHub](https://github.com/MiaAI-Lab/GLM-5.2-NVFP4-AQLM-Triple-DGX-Sparks) | 3× DGX Spark 跑 GLM-5.2，348k context 含 vision／380k 純文字 |
| Qwen3.5-9B-Holodeck-Lounge-GGUF | HF | 小說寫作／劇情生成，支援圖文輸入，多模型 merge |
| Ateron_Gemma-4-Novelist-Eclipse-31B-GGUF | [HF](https://huggingface.co/bartowski/Ateron_Gemma-4-Novelist-Eclipse-31B-GGUF) | 小說・RP 向 31B，llama.cpp imatrix 量化，支援 thinking prompt |
| Fara1.5-27B | [HF](https://huggingface.co/microsoft/Fara1.5-27B) | Microsoft 本週釋出 |

### 影像・影片模型

| 工具 | 連結 | 用途 |
|---|---|---|
| Wan2GP | [GitHub](https://github.com/deepbeepmeep/Wan2GP) | 完整 AI 影片工作站，**最低 6GB VRAM**，RTX 10 系／8GB 筆電可跑，全本地 |
| Elastic-Wan2.2-I2V | [HF](https://huggingface.co/TheStageAI/Elastic-Wan2.2-I2V) | 雙 expert 併入單 14B，RTX 5090 上 2.61×、峰值 25GB |
| FLUX 3 | Black Forest Labs 官方 | 單一多模態架構統一 Image／Video／Audio／Action-Prediction，Video early access |
| flux2-klein-9b-uncensored-text-encoder | [HF](https://huggingface.co/ponpoke/flux2-klein-9b-uncensored-text-encoder) | FLUX.2-klein-9B 的無審查 text encoder |
| Qwen-Image-Flash | [HF](https://huggingface.co/nvidia/Qwen-Image-Flash) / [int8 版](https://huggingface.co/nynxz/Qwen-Image-Flash) | NVIDIA 版與社群 int8 量化版 |
| Anima-Turbo v1.0 INT8ConvRot | HF | Anima 底模的 INT8 量化，UNet 與語言模型皆量化，生成速度超越標準 SDXL |
| SCAIL-2 | [HF](https://huggingface.co/zai-org/SCAIL-2) | Reference-to-Video；社群回饋：運行輕但精密控制（運鏡追隨、位置、記憶保持）明顯弱於同類 |
| Krea2_Collection | [HF](https://huggingface.co/cusiman/Krea2_Collection) | Krea 2 相關模型集合 |
| AnimeGen-T2V-GGUF | [HF](https://huggingface.co/NullpoLab/AnimeGen-T2V-GGUF) | AnimeGen 文生影片的有志者量化版 |
| MiniMax H3（Hailuo） | early access | 最長 15 秒、2K、支援 First／Last Frame 與 Omni Reference；煙霧火焰等粒子表現突出 |
| Mage-VL 4B | [HF Space](https://huggingface.co/spaces/hugging-apps/mage-vl-demo) | Microsoft codec-native **串流** VLM，即時描述事件，可指定「火車進站時」「進球時」 |
| TimeLens2-8B | [HF Space](https://huggingface.co/spaces/hugging-apps/timelens2-8b-temporal-grounding) | 影片版 Ctrl+F：問某事件何時發生，回傳時間區間 |

### 3D・世界模型

| 工具 | 連結 | 用途 |
|---|---|---|
| ABot World 0.5B | [HF Space](https://huggingface.co/spaces/acvlab/abot-world-interactive) | 消費級 GPU 即時世界模型，單圖啟動、鍵盤操控 |
| Wonder by Adobe | `wonder-world-model.github.io` | 即時互動 3D 導覽，單圖分鐘級 rollout，恆定 latency，基於 Wan2.1 |
| img2threejs v1.3 | [GitHub](https://github.com/hoainho/img2threejs) | 一張照片 → procedural Three.js 程式碼，無 mesh、無手動建模 |
| SuperSplat | `superspl.at` | 瀏覽器內 3DGS 大空間串流，自動生成碰撞（PlayCanvas） |
| InfiniSplat | HF Space（PLUS-WAVE） | Splat 生成 |
| awesome-4dgs | [GitHub](https://github.com/awesome-4dgs/awesome-4dgs) | 4DGS／動態 3DGS 的論文・程式碼・資料集清單 |
| MengTo/Skills | [GitHub](https://github.com/MengTo/Skills) | Three.js 等距 ARPG 開發 Skills，含相機、VFX、音訊、戰鬥系統 |

### Blender・DCC 工具

| 工具 | 連結 | 用途 |
|---|---|---|
| quickmenu | [GitHub](https://github.com/passivestar/quickmenu) | Blender 免費全能 addon，把大量工作流工具收進一個 node 驅動選單 |
| FreePencil2 | @masamune_sakaki 發布 | Blender 輪廓線 addon（非卡通 shader），**即時預覽線稿**，用 Fable5 開發 |
| VRM to MMD Converter v1.6.3 | `nicodan-mmd.github.io` | VRM → PMX，本版強化多語系 |
| spritefusion-pixel-snapper | [GitHub](https://github.com/Hugo-Dz/spritefusion-pixel-snapper) | 像素對齊到完美網格 |
| iCity | `superhivemarket.com/products/icity` | Geometry Nodes 程序化城市生成，分鐘級建整座城 |

### 音訊

| 工具 | 連結 | 用途 |
|---|---|---|
| AIraoke | @TheAwakenOne619 / cocktail peanut 一鍵啟動 | Gradio app，上傳影片或貼 YouTube URL → 自動轉錄歌詞疊字 → 卡拉OK 影片，免費本地 |
| HeartMuLa-oss-3B | [HF](https://huggingface.co/HeartMuLa/HeartMuLa-oss-3B-happy-new-year) | 音樂生成，社群評接近 Ace Step 1.5 品質 |
| japanese-composition-skills | [GitHub](https://github.com/jtydhr88/japanese-composition-skills) | Claude Code Skills 直出可編輯多軌 MIDI（velocity／延音／tempo） |
| Irodori-TTS-VoiceDesign（fork） | @rootport 發布 | 加入 **CSV 批次輸入**：劇本台詞＋演技指示一次全生成 |
| 合成っぽいど | `synthlike-voice.tanosix.com` | 人聲 → 電音／機器人聲轉換，支援麥克風即時輸入 |

### Agent・開發者 CLI

| 工具 | 連結 | 用途 |
|---|---|---|
| Claude 5 context engineering 指南 | @trq212（Anthropic） | Claude Code system prompt 砍掉 ~80% 後的心得：怎麼寫 system prompt／Skills／CLAUDE.md |
| Remotion Agent Skills | `npx skills add remotion-dev/skills` | 純 prompt 產 motion graphics，全部落在程式碼，不需 MCP／影片模型 |
| Blender MCP | 社群實測（@explosss1ve、@AiNamanari33743） | 模型寫 bpy → blockout → 佈景 → render → 讀回自己的 render 自我修正 |
| Vibe coder 上線前安全檢查表 | @PrajwalTomar_ 整理 | 30 分鐘 pre-launch checklist；起因是 Supabase 帳單爆炸與訴訟案例 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| LTX-2.3 Clean Plate IC-LoRA | 影片 LoRA | HF（@ltx_io） | 無 mask 移除人車＋重建背景 | @ltx_io |
| LTX-2.3 IC-LoRA Relight | 影片 LoRA | HF（@ltx_io） | 重打外景太陽光，12 looks × 8 方向 | @ltx_io |
| LTX-2.3 LensRemover | 影片 LoRA | HF | 去光暈／眩光／鏡頭髒污 | @SD_Tutorial |
| LTX-2.3 Black Magic | 影片 LoRA | HF | 暗部素材救援（創意向） | @wildmindai |
| ComfyUI-Qlip | ComfyUI 節點 | GitHub | NVFP4 加速：FLUX 1.7×／LTX2.3 1.8× | @TheStageAI |
| Elastic-Wan2.2-I2V | 影片模型 | HF | 單 transformer 雙 expert，5090 上 2.61× | @TheStageAI |
| ComfyUI-FL-SeedVR2 | ComfyUI 節點 | GitHub | 1.4B 影像修復放大，alpha 保留 | @Machinedelusion |
| Wan2GP | 影片工作站 | GitHub | 6GB VRAM 全本地影片生成 | @simplifyinAI |
| Kimi-K3 | LLM | HF | Moonshot 新旗艦 | moonshotai |
| Kimi-K3-2bit-UVMAX | 量化 LLM | HF | 128GB Mac 上跑（1.1 t/s） | @ktam72 |
| Ternary-Bonsai-27B | 量化 LLM | HF | 27B 塞進 4GB VRAM | prism-ml |
| ThinkingCap-Qwen3.6-27B NVFP4 | 量化 LLM | HF | 無審查＋thinking 減半，RTX 50 系 | @Tono_Ken3 |
| Agents-A1-4B-NVFP4 | 小型 LLM | HF | 4B 高 EQ 對話（非 coding） | @AM09_21 |
| Mage-VL 4B | VLM | HF Space | 串流影片事件即時描述 | @HuggingApps |
| TimeLens2-8B | VLM | HF Space | 影片時間定位（Ctrl+F for video） | @HuggingApps |
| ABot World 0.5B | 世界模型 | HF Space／本地 | 單圖 + 鍵盤即時操控世界 | @HuggingApps |
| img2threejs | CLI／repo | GitHub | 照片 → Three.js 程序化程式碼 | @NickDevFE |
| SuperSplat | Web 3D | superspl.at | 瀏覽器 3DGS 實景串流 | @willeastcott |
| MengTo/Skills | Agent Skills | GitHub | Three.js ARPG 開發技能包 | @MengTo |
| japanese-composition-skills | Agent Skills | GitHub | Claude 直出多軌可編輯 MIDI | @jtydhr88 |
| quickmenu | Blender addon | GitHub | 一站式工作流選單 | @3DxDEV7 |
| AIraoke | 本地 app | 一鍵啟動 | 任何影片／YouTube → 卡拉OK | @cocktailpeanut |
| MAGE + KREA 雙 pass | 工作流 JSON | HF | Krea-2-Turbo 4 steps／0.35 denoise 精修 | @realrebelai |

---

## 🎯 本週趨勢

### 1. 量化格式已經分裂，而且開始綁死硬體——選型必須從「模型」下移到「模型 × 格式 × 卡」

本週出現的量化格式至少四種：**GGUF**（Kimi-K3、AnimeGen、Gemma-4-Novelist）、**NVFP4**（ThinkingCap、Agents-A1、Qlip、Elastic-Wan2.2）、**AQLM**（GLM-5.2）、**ternary 三值**（Bonsai-27B）。這不是百花齊放，是分裂：NVFP4 實質上是 RTX 50 系專屬，AQLM 出現在 DGX Spark 叢集場景，GGUF 仍是 Apple silicon 與雜牌硬體的最大公約數。

**實際意義**：以前「我要跑 27B」是一個決定，現在是三個決定。挑模型之前先確定卡——如果是 RTX 50 系，優先找 NVFP4 版本（Qlip 的 1.5–1.8× 加速就是白拿的）；如果是 Apple silicon，別碰 NVFP4，直接 GGUF；如果 VRAM 在 4–8GB 這種極限區間，ternary 這類新方案比硬塞 Q3 更值得試。另外，@loktar00 的 Q4 全白翻車提醒了一件事：**文字任務的量化耐受度不能直接套用到圖像／多模態任務**，同一個 Q4_K_M 在兩邊的可用性可能天差地遠。

### 2. 「蒸餾 + 加速」的社群回報率，本週明顯高於「發新模型」

看幾個數字對照就很清楚：SeedVR2 **7B → 1.4B** 之後立刻有人做原生 ComfyUI 節點並衝上高互動；Elastic-Wan2.2 把兩個 expert 併成一個 14B、**VBench consistency 不掉**卻省一半；Qwen3.6 的 ThinkingCap 版把 thinking 砍半、無審查版下載量還**反超官方**（6700 vs 5700）。

**實際意義**：這是社群在用下載量投票——**能跑起來 > 分數高**。對選型的含意是，看到一個新 SOTA 先別急著換，等一到兩週通常會出現一個「小一號、快兩倍、品質差不多」的版本，那個才是該進工作流的。反過來說，如果你在做開源模型發布，把蒸餾／量化版和底模一起放出來，社群採用速度會完全不同。

### 3. IC-LoRA 正在把影片模型從「生成器」重新定義成「可程式化的後製圖層」

LensRemover、Black Magic、Relight、Clean Plate 這四個 LoRA 有個共同結構：**輸入是既有素材，輸出是同一段素材的某個屬性被改寫**，而不是憑空生成。Relight 的「用光向球當控制訊號」和 Clean Plate 的「不需要 mask」尤其關鍵——前者把抽象的打光意圖變成幾何輸入，後者取消了傳統 rotoscoping 的最大人力成本。@ModelCollapse38 的 Blender depth map → IC-LoRA 管線則證明它能吃結構化的引導。

**實際意義**：如果你的用途是「修既有片段」而不是「無中生有」，LTX-2.3 目前的工具密度在開源側已經沒有對手，值得直接投入。同時要留意 Black Magic 作者的免責聲明——這類模型是**腦補**不是還原，任何需要保真的場景（監控、證據、醫療、產品實拍）都不能用。

### 4. Agent 的介面正在反向設計：不是讓 AI 學會用人類的 UI，而是為 AI 重寫工具

Anthropic 砍掉 80% system prompt、@_3912657840 做出「沒有人類 UI 的 Codex 專用 app」、Remotion 用 Skills 取代 MCP、japanese-composition-skills 直出可編輯 MIDI——這四件事指向同一個結論：**讓多模態模型去辨識截圖、抓座標、模擬滑鼠，是目前 agent 工作流裡最浪費的一段**。

**實際意義**：如果你在為 agent 建工具，優先順序應該是「**結構化輸出 > CLI > API > GUI 自動化**」。特別注意 Skills 與 MCP 的分工正在清晰化——MCP 解決「能不能存取」，Skills 解決「知不知道怎麼做得好」。本週幾個高互動案例（MIDI 編曲、Three.js 遊戲、Remotion 動畫）全都是把**領域專業知識**打包成 Skills，而非增加工具數量。

### 5. 開源的政治敘事升溫，但工程側的風險敘事同步跟上

Jensen Huang 的 X 首篇貼文就是一封力挺開源模型的公開信（開源強化安全與資安、加速創新擴散、保障主權），這是本週互動量最大的單則內容（6440 萬瀏覽）。但同一週的另一面是：@PrajwalTomar_ 轉述的「**vibe coder 被告**」——有人的 Supabase 帳單爆到 200 美元、有人吃上官司，起因都是跳過上線前的基本安全項；@TheoMediaAI 則提醒大家去讀 Higgsfield 剛更新的服務條款與隱私政策。

**實際意義**：本地／開源棧的價值主張正在從「省錢」與「無審查」，擴大到「**資料不出機器 + 條款不會被單方面改寫**」。這週書籤裡大量出現的「runs locally」「everything stays on your machine」「no filters」，本質上都是對第三方 SaaS 條款風險的定價。反過來說，如果你在用 vibe coding 出貨給真實用戶，那份 30 分鐘檢查表是本週最不性感但最該做的一件事。
