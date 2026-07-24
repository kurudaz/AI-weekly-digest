# OpenCode 週報 - 2026.07.24

本週書籤的訊號非常集中：**「把大模型塞進小硬體、把商業級能力搬到本地」** 幾乎是所有主線的共同底色。從 4GB VRAM 跑 27B、到 Krea 2 被社群拆成一整套編輯生態、再到 ComfyUI 用 NVFP4 把影片模型加速 2～3 倍，這一週的重點不是「出了什麼新模型」，而是**社群如何在開源模型發布後，用量化、LoRA、MCP 把它們變成能落地的工具鏈**。

---

## 🔥 本週焦點

### 1. 本地無檢閱 LLM 大爆發：量化技術把「跑得動」的門檻壓到極限

這是本週體量最大的一條主線，串起十幾條書籤。核心不是某一個模型，而是**壓縮/量化技術（ternary、NVFP4、MTP）集體成熟**，讓 20B～150B 級模型第一次真正落進消費級硬體。

- **`Ternary-Bonsai-27B`**：把 `Qwen3.6-27B` 做三值（ternary）壓縮後，**塞進 4GB VRAM** ([prism-ml/Ternary-Bonsai-27B-gguf](https://huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf))。同一則書籤把選型按硬體分層：4–8GB 推 `Nanbeige4.2-3B`，24–96GB 才上更大模型。
- **`Agents-A1-4B` 現象**：多位開發者驚訝於「一個 4B 模型的 EQ / 對話品質完全不像 4B」（Coding 仍弱）。社群迅速衍生出**無檢閱版** [`NullpoLab/Agents-A1-4B-Heretic-ARA-Refusals8-GGUF`](https://huggingface.co/NullpoLab/Agents-A1-4B-Heretic-ARA) 與**焊上 MTP 的 NVFP4 版** [`sakamakismile/Agents-A1-4B-NVFP4`](https://huggingface.co/sakamakismile/Agents-A1-4B-NVFP4)（專為 RTX 50XX）。
- **`Step-3.7-Flash-148B` (REAP)** 在 local.ai 榜上，於 **DGX Spark 與 Mac M5 Max** 上拿到 **#1**——顯示 148B 級模型已能在單機工作站級硬體上實用。
- **MTP（多 token 預測）成為本週關鍵字**：`Qwen3.6-27B-Uncensored-MTP` 把 MTP 預測頭直接 merge 進無檢閱基底，宣稱「對話品質 + 推理速度雙提升」；連 Gemma 4 的 llama.cpp 版也走 **QAT + MTP** 路線（ggerganov）。
- **風險面**：有開發者預告 **7/27 Kimi K3（ミュトス級）開源**，並直言無檢閱版必然外流、可能大幅拉高對基礎設施的網路攻擊能力——這是本週少見帶安全視角的提醒。

> **對選型的意義**：VRAM 不再是硬牆。若你在做本地標註/對話 agent，4B 級（Agents-A1、Nanbeige）已經夠用且極省；要品質就用 ternary/NVFP4 把 27B 級壓進中階卡。**優先看「量化格式 + 是否帶 MTP」，而不是只看參數量。**

### 2. Krea 2 被社群「拆解重組」：一個開源影像模型長出一整套編輯生態

Krea 2 是本週最典型的「模型發布 → 社群連鎖反應」案例。它本體強在**單次生成（one-shot）就能做到 identity transfer + 2 個 style LoRA + 高 prompt 依從**（@hellorob），而真正的爆點是社群在兩週內把它改造成 edit model：

- **身分編輯**：[`conradlocke/krea2-identity-edit` LoRA v1.1](https://huggingface.co/conradlocke/krea2-identity-edit)，以及 Mickmumpitz 用 ostrisai 的做法把它轉成 edit 模型的 [`yijunwang2/krea2-reid`](https://huggingface.co/yijunwang2/krea2-reid)（Consistent Character Creator）。
- **外擴（outpaint）**：yijunwang2 的 outpaint LoRA「小、質量好、配 **Krea 2 Turbo 超快**」（Linoy Tsaban），且已包成帶互動畫布的 Spaces app（`krea2-outpaint`）。
- **寫實質感**：[`Danrisi/Canon_UltraReal`](https://huggingface.co/Danrisi/Canon_UltraReal) 用 Canon 1Ds、28/50/100/500mm 實拍照訓練，重現特定鏡頭的色彩與散景。
- **可控性補完**：Krea2 的表情 + 肌肉 prompting 指南、Wikked Krea2 i2i workflow（讀圖即跑、自動生 prompt、支援 LoRA）。

> **意義**：這是「開源模型 > 閉源模型」的真正差距所在——**社群能在幾天內把它做成 identity edit / outpaint / reid，還順手變成 Spaces app**。閉源 API 給你能力，開源生態給你可組裝的工具鏈。

### 3. LTX-2.3 + ComfyUI：IC-LoRA 走向「無 mask 編輯」，NVFP4 把影片推理加速到 3.6×

影片生成本週的兩個重點分別是**能力（IC-LoRA）**與**速度（Qlip）**，且都圍繞 ComfyUI 與 LTX-2.3。

- **能力側 — IC-LoRA**：
  - **Clean Plate IC-LoRA**（LTX.io）：**無需 mask** 直接移除影片裡的人、行人、車輛並補回背景，保留建築/地面標線/植被；DeepForest 用它在**澀谷十字路口**素材上實測多素材消除效果。
  - **結構引導**：Encodium 用 Blender 建船隻場景 → 輸出 depth map → LTX-2.3 IC-LoRA 生出 **14 秒影片**（Blender → LTX-2.3 → Final）。
  - **一致性**：[`LTX2.3-10Eros-1.4-GGUF`](https://huggingface.co/vantagewithai/LTX2.3-10Eros-1.4-GGUF) 主打 **60fps / 30 秒**下的角色一致性與解剖修正。
- **速度側 — [`ComfyUI-Qlip`](https://github.com/TheStageAI/ComfyUI-Qlip)（TheStageAI）**：以 **NVFP4 量化 + 動態 LoRA 切換**，用 Compilation + 量化 + 自訂 CUDA Kernel + 記憶體管理，實測 **FLUX 1.7×、Wan 2.2 1.5×、Z-Image 1.8×、LTX2.3 1.8×，最高 3.6×**。搭配 [`Elastic-Wan2.2-I2V`](https://huggingface.co/TheStageAI/Elastic-Wan2.2-I2V)：用一顆 LoRA 把 high/low-noise 兩專家合併進單一 14B transformer，在 **RTX 5090** 上以 NVFP4 + FP4 attention 達 **2.61× 加速、峰值 VRAM ~25GB**，VBench 一致性仍對齊原始雙專家。

> **意義**：ComfyUI 影片工作流正在同時「補能力」與「降成本」。**IC-LoRA 讓編輯不再需要手繪 mask，Qlip 讓 5090 級卡跑影片模型的成本直接砍半**——本地影片後製的可行性明顯往前跨了一步。

### 4. Blender MCP 與「Skill 化」：3D 建模與動效變成一段對話

本週另一條清晰主線是**用 LLM 直接驅動創作軟體**，重點在 agent 能「寫程式 → 渲染 → 讀回自己的畫面 → 修正」的閉環。

- **Blender MCP**：explos1ve 展示模型透過 MCP 寫 `bpy`、拉出 base mesh、佈景、轉相機、渲染、**再讀自己渲出來的那一幀**去自我修正（GPT-5.6 + Blender MCP）；Namanari 則用 **Blender MCP × Claude Fable 5** 做動畫 previz。
- **Google [`GNM`](https://github.com/google/GNM)**：全開源，Iker 展示在 Blender 裡用 normalized vertex groups 遮罩不同區域做隨機化，適合直接在上面二次開發。
- **Skill / MCP 生態**：Remotion 推出 **Agent Skills**（`npx skills add remotion-dev/skills`，純自然語言 / 程式碼一鏡到底做 motion design）；[`emilkowalski/skills`](https://github.com/emilkowalski/skills) 把 Apple 設計風格蒸餾成 **17 條原則的 apple-design skill（14.5K stars）**；Higgsfield 的 After Effects MCP/Plugin（Fable 5 驅動）能把參考圖拆成可編輯向量圖層。

> **意義**：3D / 動效工具的「腳手架」正在被對話取代。**值得關注的不是單一 demo，而是 MCP + Skill 這套組合**：能力被打包成可安裝、可複用的 skill，這比一次性 prompt 更接近工程化落地。

---

## 🛠️ 工具清單

### 本地 LLM / 量化模型
| 工具 | 連結 | 用途 |
|---|---|---|
| Ternary-Bonsai-27B | [huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf](https://huggingface.co/prism-ml/Ternary-Bonsai-27B-gguf) | Qwen3.6-27B 三值壓縮，塞進 4GB VRAM |
| Nanbeige4.2-3B | [huggingface.co/Nanbeige/Nanbeige4.2-3B](https://huggingface.co/Nanbeige/Nanbeige4.2-3B) | 4–8GB 級小模型，適合標註類任務 |
| Agents-A1-4B-Heretic (GGUF) | [huggingface.co/NullpoLab/Agents-A1-4B-Heretic-ARA](https://huggingface.co/NullpoLab/Agents-A1-4B-Heretic-ARA) | 高 EQ 4B 對話模型的無檢閱版 |
| Agents-A1-4B-NVFP4 | [huggingface.co/sakamakismile/Agents-A1-4B-NVFP4](https://huggingface.co/sakamakismile/Agents-A1-4B-NVFP4) | RTX 50XX 專用 NVFP4 + MTP 量化 |
| Qwen3.6-35B-A3B-Uncensored | [huggingface.co/HauhauCS/…-Aggressive](https://huggingface.co/HauhauCS/Qwen3.6-35B-A3B-Uncensored-HauhauCS-Aggressive) | MoE 無檢閱模型 |
| flux2-klein-9b 無檢閱文本編碼器 | [huggingface.co/ponpoke/flux2-klein-9b-uncensored-text-encoder](https://huggingface.co/ponpoke/flux2-klein-9b-uncensored-text-encoder) | FLUX.2-klein-9B 的替換式 text encoder |
| Gemma 4（Unsloth 量化集） | [huggingface.co/collections/unsloth/gemma-4](https://huggingface.co/collections/unsloth/gemma-4) | 修正 tool-calling/對話，GGUF/MLX/NVFP4 |

### 影像模型 / Krea 2 LoRA
| 工具 | 連結 | 用途 |
|---|---|---|
| krea2-identity-edit v1.1 | [huggingface.co/conradlocke/krea2-identity-edit](https://huggingface.co/conradlocke/krea2-identity-edit) | Krea 2 身分編輯 LoRA |
| krea2-reid | [huggingface.co/yijunwang2/krea2-reid](https://huggingface.co/yijunwang2/krea2-reid) | 把 Krea 2 轉成一致角色 edit 模型 |
| krea2-outpaint（Spaces app） | Spaces `hugging-apps/krea2-outpaint` | 互動畫布外擴，配 Krea 2 Turbo 極快 |
| Canon_UltraReal | [huggingface.co/Danrisi/Canon_UltraReal](https://huggingface.co/Danrisi/Canon_UltraReal) | Canon 1Ds 實拍質感 LoRA |
| Flux.2 Klein Ultimate AIO Pro v4.0 | [civitai.com/models/2390013](https://civitai.com/models/2390013) | T2I/I2I/inpaint/swap/edit 全能工作流 |

### 影片生成 / ComfyUI 節點
| 工具 | 連結 | 用途 |
|---|---|---|
| ComfyUI-Qlip | [github.com/TheStageAI/ComfyUI-Qlip](https://github.com/TheStageAI/ComfyUI-Qlip) | NVFP4 量化加速多模型，最高 3.6× |
| Elastic-Wan2.2-I2V | [huggingface.co/TheStageAI/Elastic-Wan2.2-I2V](https://huggingface.co/TheStageAI/Elastic-Wan2.2-I2V) | 單 transformer 雙專家，5090 上 2.61× |
| LTX-2.3 Clean Plate IC-LoRA | @ltx_io（LTX.io） | 無 mask 移除人/車並補背景 |
| LTX2.3-10Eros-1.4-GGUF | [huggingface.co/vantagewithai/LTX2.3-10Eros-1.4-GGUF](https://huggingface.co/vantagewithai/LTX2.3-10Eros-1.4-GGUF) | 60fps/30s 角色一致性 |
| ComfyUI-NO8D-controls | [github.com/no8d/ComfyUI-NO8D-controls](https://github.com/no8d/ComfyUI-NO8D-controls) | 好用的畫風選擇器節點 |
| AnimeGen-T2V-GGUF | [huggingface.co/NullpoLab/AnimeGen-T2V-GGUF](https://huggingface.co/NullpoLab/AnimeGen-T2V-GGUF) | AnimeGen 文生影片量化版 |

### 3D / Blender / Gaussian Splatting
| 工具 | 連結 | 用途 |
|---|---|---|
| Google GNM | [github.com/google/GNM](https://github.com/google/GNM) | 開源網格生成，可二次開發 |
| SuperSplat / PlayCanvas | [superspl.at](https://superspl.at) | 瀏覽器串流 3DGS 建築可視化，自動碰撞 |
| KIRI Engine 3DGS morph 工具 | [felix-hirt.com](https://felix-hirt.com) | 自動化 Gaussian Splatting 形變 |
| VRM to MMD Converter v1.6.3 | [nicodan-mmd.github.io](https://nicodan-mmd.github.io) | VRM 轉 MMD（vrm2pmx） |

### 音樂 / 音效生成
| 工具 | 連結 | 用途 |
|---|---|---|
| HeartMuLa-oss-3B | [huggingface.co/HeartMuLa/HeartMuLa-oss-3B-happy-new-year](https://huggingface.co/HeartMuLa/HeartMuLa-oss-3B-happy-new-year) | 接近 Ace Step 1.5 品質的音樂生成 |
| MOSS-SoundEffect-v2.0 | [huggingface.co/OpenMOSS-Team/MOSS-SoundEffect-v2.0](https://huggingface.co/OpenMOSS-Team/MOSS-SoundEffect-v2.0) | 可商用音效生成 |
| SAORI（Stable Audio Open 重實作） | Spaces `alfredplpl/SAORI` | 可商用音訊生成（demo） |
| 合成っぽいど | [synthlike-voice.tanosix.com](https://synthlike-voice.tanosix.com) | 人聲轉機械/合成音（支援麥克風） |
| purewavs | [purewavs.vercel.app](https://purewavs.vercel.app) | 降低 AI 音樂金屬音的後處理 |

### 開發者 CLI / Skill / MCP / 影片理解
| 工具 | 連結 | 用途 |
|---|---|---|
| Remotion Agent Skills | `npx skills add remotion-dev/skills` | 用 Claude Code 純程式碼/自然語言做影片 |
| apple-design skill | [github.com/emilkowalski/skills](https://github.com/emilkowalski/skills) | Apple 風格 17 條設計/動效原則 |
| Higgsfield AE MCP/Plugin | @higgsfield（Fable 5） | AE 內建 agent，參考圖拆成可編輯向量層 |
| TimeLens2（8B） | Spaces `hugging-apps/timelens2-8b-temporal-grounding` | 影片版 Ctrl+F，問事件發生時間 |
| MetaView（Kwai） | Spaces `hugging-apps/metaview-lightning` | 單圖生成幾何正確的新相機視角 |
| 2D2VRM | [2d2vrm.k2wanko.dev](https://2d2vrm.k2wanko.dev) | 上傳 1 張圖 → 瀏覽器內生成 VRM 頭像 |

---

## 📊 快速參考卡

| 名稱 | 類型 | 取得方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Ternary-Bonsai-27B | 本地 LLM（量化） | HF GGUF 下載 | 27B 塞進 4GB VRAM | @0xSero |
| Agents-A1-4B (NVFP4/Heretic) | 本地 LLM | HF 下載 | 高 EQ 對話、RTX50XX | @Tono_Ken3 / @NullpoLab |
| Step-3.7-Flash-148B (REAP) | 本地 LLM | local.ai 榜單 | DGX Spark/M5 Max 上 #1 | 書籤內連結 |
| Gemma 4（QAT+MTP） | 本地 LLM | Unsloth / llama.cpp | tool-calling 修正、更快 | @UnslothAI / @ggerganov |
| ComfyUI-Qlip | ComfyUI 節點 | GitHub clone | NVFP4 加速多模型 3.6× | @TheStageAI |
| Elastic-Wan2.2-I2V | 影片模型 | HF 下載 | 5090 上 2.61×、~25GB | @TheStageAI |
| LTX-2.3 Clean Plate IC-LoRA | 影片編輯 LoRA | LTX.io | 無 mask 移除人/車 | @ltx_io |
| krea2-reid / identity-edit | 影像 LoRA | HF 下載 | Krea 2 一致角色/身分編輯 | @mickmumpitz 等 |
| Canon_UltraReal | 影像 LoRA | HF 下載 | Canon 實拍質感 | @Danrisi |
| Google GNM | 3D 工具 | GitHub | 開源網格生成/遮罩 | @soyposmoderno |
| Remotion Skills | 開發者 Skill | `npx skills add` | Claude Code 生成影片 | @Remotion |
| apple-design skill | 開發者 Skill | GitHub | Apple 風格前端規範 | @BaoZiDaDa010 |
| TimeLens2-8B | 影片理解 | HF Spaces | 影片事件時間定位 | @HuggingApps |
| 2D2VRM | Avatar 工具 | 網頁（免安裝） | 單圖轉 VRM 頭像 | @k2wanko |

---

## 🎯 本週趨勢

1. **量化格式（NVFP4 / ternary / MTP）成了新的選型主軸。** 這一週幾乎每個值得跑的本地模型都附帶「哪種量化 + 給哪張卡」的說明：ternary 讓 27B 進 4GB、NVFP4 綁定 RTX 50XX、MTP 同時買到品質與速度。**選型建議從「參數量」轉向「量化格式 + 目標硬體」——同一顆基座模型，不同量化版本的可用性天差地別。**

2. **開源模型的真正護城河是「社群改造速度」。** Krea 2 兩週內被拆成 identity edit / reid / outpaint / Canon 質感 LoRA，還順手變成 Spaces app；LTX-2.3 也在同一週長出 Clean Plate、Eros、IC-LoRA workflow。閉源 API 給你固定能力，開源給你**一整條可組裝、可微調的工具鏈**——對需要客製化 pipeline 的團隊，這才是決定性差異。

3. **ComfyUI 正在從「能力平台」變成「效能平台」。** Qlip 用 CUDA Kernel + NVFP4 把影片模型加速到 2～3.6×、峰值 VRAM 壓到 25GB 級，配合動態 LoRA 切換直指「production」。**這意味著本地影片生成的瓶頸正從「跑不跑得動」轉向「單張圖/單支片的成本」**，中階顯卡的實用邊界明顯外推。

4. **「代碼即創作」透過 MCP + Skill 走向工程化。** Blender MCP 的自我修正閉環（寫 bpy→渲染→讀回畫面→修正）、Remotion / apple-design 的可安裝 Skill、Higgsfield 的 AE 插件，共同指向一個模式：**創作能力被打包成可複用、可版本化的 skill/MCP，而非一次性 prompt。** 想投入的話，值得從「把重複流程封成 Skill」這個角度切入。

5. **無檢閱模型的普及開始帶出安全議題。** 從 phone 上跑的 27B 無檢閱模型，到預告 7/27 開源、被點名可能拉高網路攻擊能力的 Kimi K3——**本地無檢閱模型的門檻同時對防守方與攻擊方降低**。對企業而言，這不再只是「能不能跑」，而是要開始把「員工可在本機跑任意無檢閱模型」納入資安假設。

---

*本週素材以創作類（AI 影像/影片/音樂）與本地 LLM 為主；上表已聚焦可本地運行的工具、HF 模型、ComfyUI 節點、GitHub 專案與開發者 CLI/Skill。部分連結為原文截斷後依可辨識路徑重建，取用前建議再確認。*
