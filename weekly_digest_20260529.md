# OpenCode 週報 - 2026.05.29

---

## 🔥 本週焦點

### 1. 1-bit 擴散模型正式來臨：Bonsai Image 4B
PrismML 發布了 Binary 與 Ternary Bonsai Image 4B，是第一批可直接在**瀏覽器本地運行**的 1-bit 量化圖像生成模型。模型大小僅約 3GB（對比 FLUX.2 Klein 的 16GB），支援 4B 參數推理，適合筆電、手機等輕量硬體。這代表擴散模型推理正式進入極致量化階段，低階硬體也能生圖。
> 來源：[@PrismML](https://x.com/PrismML)、[@xenovacom](https://x.com/xenovacom)

### 2. Gemini Omni 的影片編輯能力讓業界震驚
Google 的 Gemini Omni 展現出真正的多模態影片編輯能力：輸入草繪相機路徑即可生成無人機視角動畫、逐幀編輯、風格替換、場景氛圍轉換等。多位創作者實測顯示，其影片編輯（V2V）品質明顯優於獨立影片生成，是目前最強的原生多模態影片編輯工具。
> 來源：[@bilawalsidhu](https://x.com/bilawalsidhu)、[@emollick](https://x.com/emollick)、[@ponzponz15](https://x.com/ponzponz15)

### 3. DeepSeek V4 本地推理大躍進
antirez 的 `deepseek-v4-gguf` 2-bit 量化版本在 Apple 硬體上達到 ~35 tok/s，RTX Pro 6000 可達 39 tok/s。BF16 完整版本（可純 CPU 推理）也已由社群發布。Vitalik Buterin 也公開分享了完整的「自主本地 LLM 安全部署方案」，圍繞 DS4 建立。
> 來源：[@antirez](https://x.com/antirez)、[@VitalikButerin](https://x.com/VitalikButerin)、[@support_huihui](https://x.com/support_huihui)

### 4. Stable Audio 3.0 開源：可 inpainting 的音效生成
Stability AI 釋出 Stable Audio 3.0 開放權重，支援音效生成、音樂生成、以及**音頻 inpainting**（局部音頻修補），訓練資料使用授權音樂與 Creative Commons。輸出結果版權歸使用者所有，適合藝術創作與商業應用。
> 來源：[@StabilityAI](https://x.com/StabilityAI)、[@jordiponsdotme](https://x.com/jordiponsdotme)

### 5. LM Studio MTP Beta：Qwen3.6 推理速度提升 137%
LM Studio 發布 0.4.14+ Beta，整合 Multi-Token Prediction (MTP) 加速。實測 Qwen3.6 27B 從 51 tps 提升至 117 tps（+137%），35B-A3B MoE 也有 +25% 提升。Unsloth 同步支援自動 MTP + Speculative Decoding，Mac/CPU/GPU 全平台適用。
> 來源：[@CryZe107](https://x.com/CryZe107)、[@UnslothAI](https://x.com/UnslothAI)、[@atomic_chat_hq](https://x.com/atomic_chat_hq)

---

## 🛠️ 工具清單

### PrismML Bonsai Image 4B
- **類型**：圖像生成模型（1-bit 量化）
- **用途**：在本地瀏覽器/低階硬體上運行高品質擴散推理
- **GitHub/HF**：尚待官方釋出，可關注 [@PrismML](https://x.com/PrismML)

### Stable Audio 3
- **安裝**：`git clone https://github.com/Stability-AI/stable-audio-3`
- **用途**：開放權重音效/音樂生成，支援 inpainting 局部音頻編輯
- **GitHub**：`github.com/Stability-AI/stable-audio-3`

### Nova3D
- **安裝**：`git clone https://github.com/RareSense/Nova3D`
- **用途**：以提示詞生成 Blender Python 腳本，輸出每個零件可單獨編輯的 3D GLB 模型
- **GitHub**：`github.com/RareSense/Nova3D`

### Pixal3D（SIGGRAPH 2026，MIT License）
- **安裝**：`pip install pixal3d`（或參考專案頁）
- **用途**：像素對齊的 Image-to-3D，保留輪廓與細節的高保真 3D 資產生成
- **HuggingFace Demo**：`victormustar` 提供免費 demo

### ASASR（Flux-based 超解析度）
- **安裝**：`git clone https://github.com/wafer-bob/ASASR`
- **用途**：取代 SUPIR，基於 FLUX.1 dev，防止幻覺細節、提升 OCR 和分割精度
- **GitHub**：`github.com/wafer-bob/ASASR`

### seedance2-skill（Claude Code / Cursor 外掛）
- **安裝**：直接加入 Claude Code 或 Cursor 的 skill 目錄
- **用途**：電影級鏡頭語言提示詞外掛，專為字節 Seedance 2.0 設計，支援分鏡結構
- **GitHub**：`github.com/dexhunter/seedance2-skill`

### Unsloth（MTP + 量化推理）
- **安裝**：`pip install unsloth`
- **用途**：自動 MTP + Speculative Decoding，支援 Qwen3.6 MTP GGUF，20GB RAM 可跑 70+ 站搜尋
- **GitHub**：`github.com/unslothai/unsloth`

### Tencent Hy-MT2（多語言翻譯模型）
- **安裝**：`git clone https://github.com/Tencent-Hunyuan/Hy-MT2`
- **用途**：三款翻譯專用模型（1.8B/7B/30B-A3B），支援 33 種語言含粵語，1.8B 量化版不到 1GB
- **GitHub**：`github.com/Tencent-Hunyuan/Hy-MT2`

### MiMo V2.5-Coder（本地 Coding 模型）
- **安裝**：下載 `jedisct1/MiMo-V2.5-coder-Q2` via llama.cpp/LM Studio
- **用途**：需 128GB RAM，作者實測優於 Qwen3.6 和 DeepSeek V4-Flash 的本地程式碼模型
- **HuggingFace**：`huggingface.co/jedisct1/MiMo-V2.5-coder-Q2`

### PIXLRelight（單張圖像重打光）
- **安裝**：`git clone https://github.com/mlfarinha/pixl-relight`
- **用途**：基於 PBR conditioning 與 Transformer 渲染，可控的單張圖像照明編輯
- **HuggingFace**：`huggingface.co/mlfarinha/pixl`

### ComfyUI Untwisting RoPE（風格遷移節點）
- **安裝**：複製至 ComfyUI `custom_nodes/` 目錄
- **用途**：無訓練需求的風格對齊遷移，目前支援 Z-Image Turbo 和 Anima
- **GitHub**：`github.com/BigStationW/ComfyUi-Untwisting-RoPE`

### Microsoft Lens T2I 3.8B
- **用途**：使用 FLUX.2 VAE + GPT-OSS-20B 文字編碼器，支援日文等多語言提示，4步 Turbo 生成，最高 1440×1440
- **HuggingFace**：`huggingface.co/microsoft/Lens`
- **ComfyUI 整合**：已合併至 Comfy-Org 主分支

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Bonsai Image 4B | 圖像生成 | 瀏覽器直接運行 | 1-bit 本地擴散推理 | PrismML |
| Stable Audio 3 | 音頻生成 | git clone | 音效/音樂生成+inpainting | Stability AI |
| Nova3D | 3D 生成 | git clone | 提示詞生成可編輯 3D 模型 | RareSense |
| Pixal3D | 3D 生成 | pip install | 像素對齊圖像轉 3D | Tencent/SIGGRAPH'26 |
| ASASR | 圖像增強 | git clone | FLUX-based 超解析度 | wafer-bob |
| seedance2-skill | CLI Skill | 手動安裝 | Seedance 2.0 電影級提示詞外掛 | dexhunter |
| Unsloth | LLM 推理 | pip install | MTP+量化加速，全平台 | unslothai |
| Hy-MT2 | 翻譯模型 | git clone | 33語言翻譯，1.8B~30B-A3B | Tencent |
| MiMo V2.5-Coder | Coding LLM | llama.cpp 下載 | 128GB RAM 本地 Coding | jedisct1 |
| PIXLRelight | 圖像編輯 | git clone | 單張圖可控重打光 | Oxford/mlfarinha |
| Untwisting RoPE | ComfyUI 節點 | custom_nodes | 無訓練風格遷移 | BigStationW |
| Microsoft Lens | T2I 模型 | HuggingFace | 多語言 T2I，4步生成 | Microsoft |
| ComfyUI-AnimaFastTrain | ComfyUI 節點 | custom_nodes | Anima 模型快速訓練 | quinteroac |
| Ucupaint 2.4.7 | Blender 外掛 | GitHub Releases | PSD 圖層匯入匯出 | ucupumar |
| Cohere Command A+ | LLM | API/開源 | 218B MoE，active 25B，ASL2.0 | Cohere |

---

## 🎯 本週趨勢

### 1. 極致量化：模型跑進瀏覽器和手機
1-bit Bonsai、SANA 的線性注意力、GGUF 2-bit 量化——這週展示的多個模型都在追求「在任何硬體上跑」。推理不再需要叢集，筆電甚至手機開始成為可行的推理平台。

### 2. 影片編輯 AI 正在取代獨立生成工具
Gemini Omni、Runway Aleph 2.0、Seedance 2.0 本週集中展示的不再是「生成」，而是「精確編輯」——保留運動、替換角色、轉換風格。創作者流程正從「生成然後挑選」轉向「生成然後精修」。

### 3. MTP（Multi-Token Prediction）成為本地 LLM 標配
LM Studio、Unsloth、atomic.chat 本週同步支援 MTP，Qwen3.6 推理速度暴增 137%。這項技術正快速從研究走向生產工具，預計未來兩週內主流推理框架將全面跟進。

### 4. Text-to-CAD / Text-to-3D 進入實用化
Nova3D 的可編輯 3D 腳本生成、Pixal3D 的像素對齊高保真轉換、以及 Text-to-CAD 整合 Bambu Labs 和 Claude/Gemini/Codex 外掛——3D 工作流正在從「生成一個大概的形狀」進化到「生成可直接送印的精確模型」。
