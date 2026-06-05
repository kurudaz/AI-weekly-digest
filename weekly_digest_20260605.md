# OpenCode 週報 - 2026.06.05

---

## 🔥 本週焦點

### 1. Ideogram 4.0 開放權重正式登場
Ideogram 推出 9.3B 參數的開放權重文字轉圖像基礎模型，以結構化 JSON 標題資料集訓練，支援精確場景描述、文字渲染與專業排版。RTX 5090 約 20 秒可出圖，已原生整合進 ComfyUI，並提供 KJNodes 的 Ideogram 4 Prompt Builder 節點。
- 工作流程：[github.com/Comfy-Org/workflow_templates](https://github.com/Comfy-Org/workflow_templates/blob/main/templates/image_ideogram4_t2i.json)
- 來源：[@ComfyUIWiki](https://twitter.com/ComfyUIWiki)、[@ぶるぺん](https://twitter.com/blue_pen5805)

### 2. PrismML 發布 1-bit/Ternary Bonsai Image 4B
1-bit 擴散模型正式到來。Bonsai Image 4B 僅約 3GB（相比 FLUX.2 Klein 的 16GB），可 100% 在瀏覽器本地運行，對比 FLUX.2-Klein-9B 的 16GB 大幅壓縮，對邊緣端部署與行動裝置推論是重大突破。
- 來源：[@PrismML](https://twitter.com/PrismML)、[@xenovacom](https://twitter.com/xenovacom)

### 3. 3D Gaussian Splatting 工具鏈全面爆發
本週多個高品質 3DGS 相關工具同步釋出：
- **TripoSplat**（MIT 授權）：單張 2D 圖像轉高品質 3D Gaussian，已整合進 ComfyUI
- **TriSplat**：完整開源，附瀏覽器互動 Demo
- **SurGe**（MIT/Apache 2.0）：改善細薄結構的局部精度，優於 MoGe-2
- Corona 15 已支援 Gaussian Splat 的全域光照整合
- 來源：[@tripoai](https://twitter.com/tripoai)、[@karimknaebel](https://twitter.com/karimknaebel)

### 4. 本地 LLM 在消費級硬體上的邊界持續推進
- **DeepSeek V4 Flash**（162B）：Anemll 釋出 SSD MoE expert paging 方案，Apple M2 Max 64GB 可本地運行
- **Qwen3.6 27B NVFP4**：RTX 5090 下 2-8k tok/s，peak 可達 11k tok/s，已含 MTP 及圖像支援
- **Gemma 4 12B GGUF**：Unsloth 製作 Dynamic GGUF，`llama-cli` 可直接拉取執行
- 來源：[@Anemll](https://twitter.com/anemll)、[@0xSero](https://twitter.com/0xSero)、[@Unsloth](https://twitter.com/UnslothAI)

### 5. LongCat-Video-Avatar 1.5 — 免費本地說話頭像生成
美團開源模型，單張照片（實拍或動漫）＋音訊即可生成說話頭像，MIT 授權，支援多說者，宣稱精度超越 HeyGen、OmniHuman 等商業方案。
- GitHub：[meituan-longcat/LongCat-Video](https://github.com/meituan-longcat/LongCat-Video)
- HuggingFace：[meituan-longcat/LongCat-Video-Avatar-1.5](https://huggingface.co/meituan-longcat/LongCat-Video-Avatar-1.5)
- 來源：[@Seamless](https://twitter.com/shiropen2)

---

## 🛠️ 工具清單

### SearXNG 1-Click Launcher
- **用途**：免費、私有、可編程的本地搜尋引擎，AI Agent 本地化作業的關鍵基礎設施
- **支援平台**：Mac / Windows / Linux
- **安裝**：參考 [@cocktailpeanut](https://twitter.com/cocktailpeanut) 貼文中的一鍵啟動器
- **GitHub**：搜尋 `cocktailpeanut SearXNG`

### TripoSplat（Tripo AI / VastAI Research）
- **用途**：單張圖像生成高品質 3D Gaussian Splat，支援 ComfyUI 原生工作流程
- **授權**：MIT
- **安裝**：更新 ComfyUI → 開啟模板庫搜尋 TripoSplat
- **Blog**：[blog.comfy.org](https://blog.comfy.org)

### SurGe（RWTH Aachen）
- **用途**：前饋式 3D 重建，改善細薄結構精度（椅腿、路燈等）
- **授權**：MIT + Apache 2.0
- **GitHub**：[github.com/karimknaebel/surge](https://github.com/karimknaebel/surge)
- **Demo**：[huggingface.co/spaces/karimknaebel/surge](https://huggingface.co/spaces/karimknaebel/surge)

### GLB Shrink
- **用途**：一步驟大幅縮減 GLB 3D 模型檔案大小，適用遊戲、Web、AR
- **安裝**：`git clone https://github.com/boona13/glb-shrink`
- **GitHub**：[github.com/boona13/glb-shrink](https://github.com/boona13/glb-shrink)

### image-extender
- **用途**：以 AI（Gemini + Imagen 2）無縫延伸圖像，支援批次處理遊戲資產，開源
- **GitHub**：[github.com/boona13/image-extender](https://github.com/boona13/image-extender)

### Stability AI Stable-Layers
- **用途**：將圖像拆分成可編輯圖層，減少分離偽影
- **Demo**：[stability-ai.github.io/stable-layers.github.io](https://stability-ai.github.io/stable-layers.github.io)
- **來源**：[@aisearchio](https://twitter.com/aisearchio)

### NVIDIA LocateAnything
- **用途**：視覺語言偵測模型，重新思考 bounding box 預測，適用 AI Agent 與機器人
- **HuggingFace Demo**：[huggingface.co/spaces/nvidia/LocateAnything](https://huggingface.co/spaces/nvidia/LocateAnything)
- **來源**：[@ZhidingYu](https://twitter.com/ZhidingYu)（本週 HuggingFace 熱門 #1）

### Anemll ds4-ssd（DeepSeek V4 Flash SSD 推論）
- **用途**：DeepSeek V4 Flash 的 SSD MoE Expert Paging，讓 64GB RAM Mac 可本地運行 162B 模型
- **GitHub**：[github.com/Anemll/ds4-ssd](https://github.com/Anemll/ds4-ssd)
- **HuggingFace**：[anemll/dsv4-iq2xxs-expert-major](https://huggingface.co/anemll/dsv4-iq2xxs-expert-major)

### ppt-master
- **用途**：AI 生成原生 `.pptx`（非圖片），文字框、形狀、圖表、動畫均可後續編輯
- **GitHub**：搜尋 `ppt-master`（23k stars）
- **來源**：[@BTCqzy1](https://twitter.com/BTCqzy1)

### PIXLRelight（Oxford）
- **用途**：單張圖像可控重打光，基於 PBR conditioning 與 Transformer 渲染
- **GitHub**：[github.com/mlfarinha/pixlrelight](https://github.com/mlfarinha/pixlrelight)
- **HuggingFace**：[huggingface.co/mlfarinha/pixl](https://huggingface.co/mlfarinha/pixl)

### lucebox（Luce Org）
- **用途**：家用 GPU 本地高速推論伺服器，定位為最快的本地推論引擎
- **GitHub**：[github.com/Luce-Org/lucebox-hub](https://github.com/Luce-Org/lucebox-hub)

### ACE-Step 1.5（ComfyUI 整合）
- **用途**：音樂/音訊生成，僅需下載 2 個檔案，23 秒即可出聲
- **HuggingFace**：[Comfy-Org/ace_step_1.5_ComfyUI_files](https://huggingface.co/Comfy-Org/ace_step_1.5_ComfyUI_files/tree/main/checkpoints)

### GSI 3次元地図可視化サイト（日本國土地理院）
- **用途**：免費開源 WebGIS，3D 建物、道路、鐵路可視化，Fork 後可直接客製化
- **GitHub**：[gsi-cyberjapan/gsi-3d-2025](https://github.com/gsi-cyberjapan/gsi-3d-2025)
- **授權**：BSD-2-Clause

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 授權/來源 |
|---|---|---|---|---|
| Ideogram 4.0 | 圖像生成模型 | ComfyUI 模板庫 | 文字轉圖像，JSON prompt | Open-weight / @ideogram_ai |
| Bonsai Image 4B | 1-bit 擴散模型 | 瀏覽器直接運行 | 本地/瀏覽器圖像生成 | PrismML |
| TripoSplat | 3DGS 生成 | ComfyUI 模板庫 | 單圖轉 3D Gaussian | MIT / @tripoai |
| SurGe | 3D 重建 | pip / HF Space | 細薄結構精確 3D 重建 | MIT+Apache2 / @karimknaebel |
| TriSplat | 3DGS 生成 | GitHub clone | 3D Gaussian + 瀏覽器 Demo | 開源 / @wjwang2003 |
| GLB Shrink | 3D 工具 | GitHub clone | GLB 模型壓縮 | 開源 / @DivyanshT91162 |
| image-extender | 圖像工具 | GitHub clone | AI 圖像延展 | 開源 / @Kirill |
| Stable-Layers | 圖像分層工具 | Web Demo | 圖像圖層拆分 | Stability AI |
| LocateAnything | 視覺語言模型 | HF Space | Bounding Box 偵測 | NVIDIA / @ZhidingYu |
| LongCat-Video-Avatar 1.5 | 說話頭像生成 | HuggingFace | 單圖+音訊生成頭像影片 | MIT / 美團 |
| ds4-ssd (Anemll) | 本地推論引擎 | GitHub clone | DeepSeek V4 Flash SSD 分頁 | 開源 / @Anemll |
| SearXNG Launcher | 本地搜尋 | 一鍵啟動器 | 私有本地搜尋引擎 | @cocktailpeanut |
| ppt-master | 簡報生成 | GitHub clone | AI 生成原生 .pptx | 開源 / 23k stars |
| PIXLRelight | 重打光模型 | GitHub + HF | 單圖可控光源編輯 | Oxford / @mlfarinha |
| ACE-Step 1.5 | 音訊生成 | ComfyUI 節點 | 文字轉音樂/音效 | @Comfy-Org |
| lucebox | 推論伺服器 | GitHub clone | 家用 GPU 本地推論 | Luce Org |
| GSI 3D Map | WebGIS 框架 | GitHub fork | 3D 地圖可視化客製化 | BSD-2-Clause / 日本國土地理院 |
| ControlLight LoRA | FLUX LoRA | HuggingFace | 低光照可控增強 | @yfyang007 |
| Gemma 4 12B GGUF | 本地 LLM | `llama-cli -hf ggml-org/gemma-4-12b-it-GGUF:Q4_K_M` | 本地推論，4GB 以下 Q4 | Unsloth / Google |

---

## 🎯 本週趨勢

### 1. 開放權重圖像生成正在趕超閉源商業服務
Ideogram 4.0 與 Bonsai Image 4B 同週釋出，標誌著開放模型在文字渲染、排版品質與模型體積上正面迎擊商業 API。1-bit 量化讓擴散模型進入瀏覽器原生運行，邊緣端部署障礙快速消失。

### 2. ComfyUI 成為開源 AI 的整合中樞
本週多個重要模型（Ideogram 4.0、TripoSplat、ACE-Step 1.5、Wan 2.2）均優先或同步提供 ComfyUI 工作流程，Anima 生態系的節點（IP-Adapter、Prompt-Control、FastTrain）也持續補齊。ComfyUI 已從圖像工具演化為跨模態 AI pipeline 的核心。

### 3. 3D Gaussian Splatting 進入實用工具化階段
從 TripoSplat（單圖轉 3DGS）、TriSplat（開源+瀏覽器 Demo）、SurGe（改善細薄結構）到 Corona 15 的 GI 整合，3DGS 從研究成果轉為遊戲、AR/VR、影視製作的實際管線工具，且多數採 MIT/Apache 授權。

### 4. 「草圖轉影片」成為視頻生成新互動正規
Google Gemini Omni 的手繪路徑轉無人機 POV 影片、Seedance 的簡單繪圖轉動態鏡頭，展示了視覺輸入作為攝影機控制介面的潛力。配合 NVIDIA Light Interaction 的 2.5x 推論加速，互動式影片生成的延遲正在接近實用門檻。
