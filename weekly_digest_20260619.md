# OpenCode 週報 - 2026.06.19

---

## 🔥 本週焦點

### 1. GLM-5.2 開放權重模型 × Unsloth 本地化量化
Z.ai 發布 GLM-5.2，號稱目前最強開源模型：支援 1M context window、強化 coding 與 agentic 任務、具備兩段推理模式（max/high）。Unsloth 隨即推出 2-bit 量化版本，將原始 1.51TB 模型壓縮至 238GB（縮減 84%），保留約 82% 準確率，可在 256GB RAM/VRAM 環境或 Mac 上本地運行。

- 來源：[@UnslothAI](https://x.com/UnslothAI)、[@Zai_org](https://x.com/Zai_org)
- GGUF：`huggingface.co/unsloth/GLM-5.2-GGUF`
- 指南：`unsloth.ai/docs/models/glm-5.2`

---

### 2. Claude Fable 5 引爆 Vibe Coding 風潮
Anthropic 發布 Mythos 等級的 Claude Fable 5，多位開發者實測展示驚人的一次性代碼生成能力：20 分鐘生成完整 Skyrim clone（含地牢、角色創建、音效、對話、過場動畫）、兩個 prompt 完成 Titanic 遊戲。Higgsfield AI 更透過 Fable 5 + MCP 推出多人遊戲生成平台。社群中出現「Fable 5 架構師 + GPT-5.5 Codex 建構者」的雙模型協作工作流，$20 訂閱即可享用。

- 來源：[@Valentin_Ignatev](https://x.com/valigo)、[@MisbahSy](https://x.com/MisbahSy)、[@JUMPERZ](https://x.com/jumperz)、[@higgsfield](https://x.com/higgsfield)

---

### 3. LTX 2.3 22B 影片生成生態爆發
Lightricks 發布 LTX 2.3 IC-LoRA 系列，包含 Inpainting/Outpainting 專用版本，僅需 6GB VRAM 即可在 ComfyUI 中運行。社群已實現「分鏡腳本 → 最終渲染」的完整一小時工作流，並獲得高度的鏡頭穩定性評價。相較一年前需要完整 VACE 模型才能完成的任務，現在一個 IC-LoRA 即可超越。

- 來源：[@linoy_tsaban](https://x.com/linoy_tsaban)、[@SD_Tutorial](https://x.com/SD_Tutorial)、[@ComfyUI](https://x.com/ComfyUI)
- HuggingFace：`Lightricks/LTX-2.3-22b-IC-LoRA-Ingredients`
- HuggingFace：`Lightricks/LTX-2.3-22b-IC-LoRA-In-Outpainting`

---

### 4. Ideogram 4 ComfyUI 工具鏈全面爆發
本週多個 Ideogram 4 專用 ComfyUI 節點同步發布：視覺 Bbox 編輯器、JSON 提示詞自動生成器、多語言提示詞建構器（含自由繪製、圖層管理）。Kohya Tech 亦確認 Ideogram 4 LoRA 訓練可行（建議 Rank 64+）。整體形成從提示詞設計到訓練的完整工具鏈。

- 來源：[@toyxyz3](https://x.com/toyxyz3)、[@bdsqlsz](https://x.com/bdsqlsz)、各 ComfyUI 社群
- GitHub 節點列表見工具清單

---

### 5. Anima 模型生態：Kohya 優化 + Regional Controlnet
Kohya Tech 為 Anima 模型推出 Qwen-Image VAE 的 2D 專用版 PR，記憶體節省 2/3、處理速度提升至原本的 3/5，並注明「Codex 實作、Claude Code 審查」。同期，`Anima-LLLite-Regional-Controlnet` 發布，支援在 ComfyUI 中以 mask 精確控制角色位置，引發大量創作者實測。

- 來源：[@kohya_tech](https://x.com/kohya_tech)
- PR：`github.com/kohya-ss/sd-scripts/pull/2382`
- HuggingFace：`Sen-sou/Anima-LLLite-Regional-Controlnet`

---

## 🛠️ 工具清單

### GLM-5.2-GGUF（Unsloth 量化）
- **用途**：本地運行目前最強開源大型語言模型，2-bit 量化至 238GB
- **安裝**：透過 Ollama/llama.cpp 載入 GGUF
- **HuggingFace**：`huggingface.co/unsloth/GLM-5.2-GGUF`
- **指南**：`unsloth.ai/docs/models/glm-5.2`

---

### Qwopus 3.6 27B Coder（GGUF）
- **用途**：開源編碼專用模型，SWE-bench verified 67%，單張 RTX 5090 達 100 token/s，支援原生 MTP head
- **安裝**：透過 llama.cpp/Ollama 載入 GGUF
- **HuggingFace**：`Jackrong/Qwopus3.6-27B-Coder-MTP-GGUF`

---

### glm-5.2-sm120（RTX PRO 6000 Blackwell 部署）
- **用途**：在 4× RTX PRO 6000 Blackwell（SM120）上部署 GLM-5.2 469B NVFP4-REAP 推論服務
- **GitHub**：`github.com/0xSero/glm-5.2-sm120`

---

### Flash-KMeans
- **用途**：IO 感知的精確 KMeans 實作，針對現代 GPU 記憶體瓶頸優化，速度比標準實作快 200 倍，超越 cuML 與 FAISS
- **安裝**：`pip install flash-kmeans`（詳見 repo）
- **GitHub**：`github.com/svg-project/flash-kmeans`

---

### privacy-filter.cpp
- **用途**：OpenAI 隱私過濾 NER 模型架構的本地 C++ 實作，可在本地運行敏感資訊識別
- **GitHub**：`github.com/localai-org/privacy-filter.cpp`

---

### game-art-prompt-kit
- **用途**：遊戲藝術生成的可重用提示詞模板集，含 14 種配置（像素 spritesheet、角色概念、2D 背景等）
- **GitHub**：`github.com/lexlex47/game-art-prompt-kit`

---

### gorest - 2D Animation Spritesheet Generator
- **用途**：瀏覽器內拖曳式 2D 場景編輯器，透過 Codex 本地生成 12/16 幀 spritesheet，幀大小與錨點一致
- **GitHub**：`github.com/NO6KIKO/gorest-2d-animation-spritesheet-generator`

---

### Ironsmith
- **用途**：macOS 原生工具，透過 AI 文字描述一鍵生成 SwiftUI 原生小應用（Widget）
- **GitHub**：`github.com/Jeidoban/Ironsmith`

---

### node_wrangler_octane（Blender 插件）
- **用途**：Blender Octane 渲染器的 Node Wrangler 擴充，支援 Octane 2026.3-31.8-stable，修復紋理設定問題
- **安裝**：下載後從 Blender 偏好設定安裝
- **GitHub**：`github.com/AiSatan/node_wrangler_octane`

---

### GuidedDenoising
- **用途**：引導式網格法線過濾，快速平滑雜訊網格，在 AI 生成的複雜幾何體處理上仍優於新工具
- **GitHub**：`github.com/bldeng/GuidedDenoising`

---

### Anima-LLLite-Regional-Controlnet
- **用途**：Anima 模型的 ComfyUI 區域 ControlNet，透過 mask 精確控制角色在畫面中的位置
- **HuggingFace**：`huggingface.co/Sen-sou/Anima-LLLite-Regional-Controlnet`

---

### comfyui-ideogram4-bbox-editor（自訂節點）
- **用途**：ComfyUI 內視覺化 Bounding Box 編輯器，直接在節點上繪製區域，輸出 Ideogram 4 v15 格式 JSON 提示詞
- **GitHub**：`github.com/quzopl/comfyui-ideogram4-bbox-editor`

---

### comfyui-ideogram-autoprompter（自訂節點）
- **用途**：自動為 Ideogram 4 生成 JSON 提示詞（含區域、bbox、風格、光線），無需手動撰寫結構化提示詞
- **GitHub**：`github.com/collbroGTR/comfyui-ideogram-autoprompter`

---

### Ideogram 4 GGUF（stduhpf）
- **用途**：Ideogram 4 模型的 GGUF 量化版本，可透過 llama.cpp 本地運行
- **HuggingFace**：`huggingface.co/stduhpf/ideogram-4-gguf`

---

### Archify
- **用途**：透過 LLM 將自然語言描述轉為結構化 JSON，再以 Node.js 純幾何演算法渲染 SVG，注入自包含 HTML，無需圖像生成模型
- **GitHub**：`github.com/tt-a1i/archify`

---

### flash-attention-prebuild-wheels
- **用途**：Flash Attention 預編譯 wheel 包，支援 ARM、Windows、Linux，省去自行編譯的麻煩
- **GitHub**：`github.com/mjun0812/flash-attention-prebuild-wheels`

---

### CoreAI Model Zoo（Apple Core AI）
- **用途**：在 iPhone/Mac 上本地運行多種 LLM（Gemma 4、Qwen3.5、Qwen3-VL、Qwen3.6-35B MoE 等），完全免費、設備端推論
- **TestFlight**：`testflight.apple.com/join/bK4P7xby`
- **GitHub（Mac .dmg）**：`github.com/john-rocky/coreai-model-zoo`

---

### FlashImgs（HuggingFace Space）
- **用途**：在 HuggingFace 上訓練 2DGS（ImageGS 風格），同時輸出互補的 3DGS PLY 文件
- **Space**：`huggingface.co/spaces/opsiclear-admin/flashimgs`

---

### Underfit（Stable Audio 3 LoRA 訓練器）
- **用途**：本地訓練 Stable Audio 3 LoRA，輸入自有音樂樣本訓練個人風格，一鍵啟動（Linux + Windows NVIDIA GPU）

---

### Kohya sd-scripts PR#2382（Anima Qwen-Image VAE 2D 優化）
- **用途**：Anima 模型的 Qwen-Image VAE 2D 專用版，記憶體節省 2/3、處理速度提升 40%，由 Codex 實作、Claude Code 審查
- **PR**：`github.com/kohya-ss/sd-scripts/pull/2382`

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| GLM-5.2-GGUF | LLM / 本地模型 | llama.cpp / Ollama GGUF | 最強開源 LLM 本地推論 | HuggingFace |
| Qwopus 3.6 27B Coder | LLM / 編碼模型 | llama.cpp GGUF | 高速本地編碼輔助 | HuggingFace |
| glm-5.2-sm120 | LLM 部署 | Docker / 直接執行 | 企業級 GLM-5.2 推論服務 | GitHub |
| Flash-KMeans | ML 函式庫 | pip install | GPU 加速 200x KMeans | GitHub |
| privacy-filter.cpp | CLI / 本地 NER | 編譯 C++ | 本地隱私過濾 NER | GitHub |
| game-art-prompt-kit | 提示詞資源 | git clone | 遊戲素材提示詞模板 | GitHub |
| gorest Spritesheet Gen | 開發工具 / 瀏覽器 | git clone + 瀏覽器 | 本地 2D 動畫 spritesheet 生成 | GitHub |
| Ironsmith | macOS 工具 | 直接執行 | AI 描述生成 SwiftUI Widget | GitHub |
| node_wrangler_octane | Blender 插件 | Blender 偏好設定安裝 | Octane 渲染器 Node Wrangler | GitHub |
| GuidedDenoising | 3D 工具 | 編譯 / 直接執行 | 網格法線平滑過濾 | GitHub |
| Anima-LLLite Regional CN | ComfyUI 節點 | HuggingFace 下載 | Anima 區域 ControlNet | HuggingFace |
| comfyui-ideogram4-bbox-editor | ComfyUI 節點 | git clone 至 custom_nodes | Ideogram 4 視覺 Bbox 編輯 | GitHub |
| comfyui-ideogram-autoprompter | ComfyUI 節點 | git clone 至 custom_nodes | Ideogram 4 JSON 提示詞自動生成 | GitHub |
| Ideogram 4 GGUF | 影像生成模型 | llama.cpp GGUF | 本地運行 Ideogram 4 | HuggingFace |
| Archify | CLI / Web 工具 | git clone + node | LLM → SVG 幾何演算法渲染 | GitHub |
| flash-attention-prebuild-wheels | Python 套件 | pip install wheel | Flash Attention 免編譯安裝 | GitHub |
| CoreAI Model Zoo | macOS / iOS App | TestFlight / .dmg | Apple 設備端 LLM 運行 | GitHub |
| FlashImgs | HuggingFace Space | 直接使用 Space | 雲端 2DGS / 3DGS 訓練 | HuggingFace |
| Underfit | 本地訓練工具 | 一鍵啟動 | Stable Audio 3 LoRA 訓練 | — |
| sd-scripts PR#2382 | 訓練框架 PR | git pull + 合併 | Anima Qwen-Image VAE 2D 優化 | GitHub |
| LTX-2.3-22b-IC-LoRA | 影片生成模型 | ComfyUI 載入 | 6GB VRAM 影片 Inpaint/Outpaint | HuggingFace |

---

## 🎯 本週趨勢

### 1. 超大模型加速本地化：量化技術成關鍵路徑
GLM-5.2 從 1.51TB 壓縮至 238GB 的案例，代表「頂級開源模型 × 量化技術 = 個人可用」的循環正在加速。Unsloth 的 2-bit 量化保留 82% 準確率，Qwen3.6 27B NVFP4 也有多人測試，本地推論已不再是大公司的專利。

### 2. Claude Fable 5 重新定義 Vibe Coding 天花板
本週書籤中，「一次 prompt 生成完整遊戲」的示範數量激增。社群出現 Fable 5（架構師）+ Codex（建構者）的雙模型工作流分層，$20 訂閱搭配 GPT-5.5 的組合顯示：高效能 AI 輔助開發已在成本可控範圍內成熟。

### 3. ComfyUI 成為 AI 創作的核心基礎設施
Ideogram 4、LTX 2.3、Anima 三大模型的周邊 ComfyUI 節點本週集中爆發：Bbox 編輯器、自動提示詞生成器、Region Controlnet、LoRA 測試工具……ComfyUI 已從「Stable Diffusion 的介面」演變為多模態生成的通用工作流平台。

### 4. Codex + 本地工具整合成新的開發正規軍
多個本週熱門專案明確標注「Codex 實作」（gorest spritesheet generator、Kohya PR、ぐるぐるアバター）。Codex 被用於實際功能開發而非只是補全，顯示 AI 輔助工程流程已從「對話輔助」升格為「實際提交代碼的隊友」。
