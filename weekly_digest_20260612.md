# OpenCode 週報 - 2026.06.12

---

## 🔥 本週焦點

### 1. Claude Fable 5 引爆生成式遊戲新紀元
Higgsfield AI 發布 **Higgsfield Games**，透過 Claude Fable 5 驅動，只需一句 prompt 即可建立並部署多人遊戲（支援 2D/3D 任意類型）。搭配 Higgsfield MCP，角色、道具、場景全部 AI 生成。同期，社群大量展示 Fable 5 在互動藝術、水墨工具、3D 場景等方面的驚人創作力，墨韻（inkpainting）等作品在推特廣泛傳播。
**來源**：[@higgsfield](https://x.com/higgsfield)、[@TanShilong](https://x.com/TanShilong)

### 2. Flux Klein 9B LoRA 生態系爆發
本週 Flux.2-Klein-9B 模型的 LoRA 社群呈現爆炸性成長：
- **SmartCharacterSwap** LoRA：3D 角色換裝
- **manga-colorization-by-reference**：依參考圖彩色化漫畫
- **refcontrol-reference-depth**：以深度圖重新姿勢並保留參考風格
- **Lighting Migration / Art Style Migration**：光線與畫風轉移

多位創作者同步測試，ComfyUI 工作流程也陸續釋出。
**來源**：[@SlipperyGem](https://x.com/SlipperyGem)、[@toyxyz3](https://x.com/toyxyz3)、[@raromasa](https://x.com/raromasa)

### 3. Gemma 4 QAT + 本地推論大突破
Google 釋出 Gemma 4 全系列 **Quantization-Aware Training (QAT)** 權重，Ollama 同步支援，記憶體需求大幅降低。Apple CoreAI 框架上首次跑通 **Qwen3-VL 2B**（33 tok/s on iPhone 17 Pro，M4 Max 達 188 tok/s）。同時，llama.cpp 正式合併 Gemma 4 MTP 支援，QAT + MTP 組合提供輕量極速的本地推論方案。
**來源**：[@googlegemma](https://x.com/googlegemma)、[@ollama](https://x.com/ollama)、[@osanseviero](https://x.com/osanseviero)、[@JackdeS11](https://x.com/JackdeS11)

### 4. Ideogram 4.0 開源後社群創作浪潮
Ideogram 4.0 開源一週，JSON bounding box prompting 成為新主流玩法。社群貢獻了大量 LoRA（Fantasy Realism、Arcane 風格等），fal.ai 上線 LoRA 訓練端點，ComfyUI 工作流程百花齊放。`cocktailpeanut` 更發布自然語言轉 bbox prompt 工具（llamacpp + Qwen，完全本地）。
**來源**：[@ideogram_ai](https://x.com/ideogram_ai)、[@cocktailpeanut](https://x.com/cocktailpeanut)、[@0xNynxz](https://x.com/0xNynxz)

### 5. Apple WWDC26：輕量 Linux 容器環境登場
Apple 在 WWDC26 發布 **Container Machines**，基於 Virtualization Framework 演進，在 Mac 上提供輕量持久的 Linux 環境，被社群視為 macOS 對 WSL 的正式回應。
**來源**：[@shibu_jp](https://x.com/shibu_jp)

---

## 🛠️ 工具清單

### CoreAI Model Zoo
- **用途**：在 iPhone/Apple Silicon 上本地運行 LLM/VLM（LFM2.5-1.2B、Qwen3.5-2B、Gemma 4 等）
- **GitHub**：`github.com/john-rocky/coreai-model-zoo`
- **安裝**：Clone 後依 README 匯入 Swift Package

### mlx2coreai
- **用途**：將 MLX 格式模型轉換為 Apple CoreAI 格式
- **GitHub**：`github.com/lucasnewman/mlx2coreai`
- **安裝**：`pip install mlx2coreai`（依 repo 指示）

### ComfyTV
- **用途**：無限畫布版 ComfyUI 應用，支援 subgraph、plugin，每步輸出自動傳遞
- **GitHub**：`github.com/jtydhr88/ComfyTV`
- **安裝**：透過 ComfyUI Manager 搜尋 `ComfyTV` 安裝

### ComfyUI-IdeogramHelper
- **用途**：ComfyUI 節點，輔助 Ideogram bbox JSON prompt 生成
- **GitHub**：`github.com/Nynxz/ComfyUI-IdeogramHelper`
- **安裝**：透過 ComfyUI Manager 搜尋 `IdeogramHelper` 安裝

### ComfyUI-DualLoraStack
- **用途**：一次載入兩個 LoRA 並共用名稱/強度參數，適合 Ideogram 4.0 unconditional/conditional 雙側套用
- **GitHub**：`github.com/knishika62/ComfyUI-DualLoraStack`
- **安裝**：透過 ComfyUI Manager 安裝

### D2-nodes-ComfyUI（含 D2 Prompt Sanitizer）
- **用途**：ComfyUI 節點集，包含 Prompt 清理、文字處理等工具
- **GitHub**：`github.com/da2el-ai/D2-nodes-ComfyUI`
- **安裝**：透過 ComfyUI Manager 安裝

### image-to-prompt
- **用途**：拖曳圖片自動生成 Ideogram bbox prompt 模板，基於 Florence 2，完全本地
- **GitHub**：`github.com/cocktailpeanut/image-to-prompt`
- **安裝**：`git clone` 後依 README 執行（llamacpp + Qwen 後端）

### html-video（剪映 HTML 版）
- **用途**：Agent 可透過撰寫 HTML 製作產品宣傳/知識解說影片，支援 20+ 視頻風格模板、MP4 匯出、Claude Code/Cursor 等 Agent 接入
- **GitHub**：詳見作者 [@tuturetom](https://x.com/tuturetom) 留言
- **安裝**：開源，Clone 後本地部署

### ds4 多 GPU 推論 Fork
- **用途**：DeepSeek V4 Flash/PRO 原生推論引擎，支援單機多 GPU 層切分（`--tensor-parallel N`），無需 TCP/分散式，支援消費級顯卡
- **GitHub**：由 [@support_huihui](https://x.com/support_huihui) 發布，詳見推文
- **安裝**：Clone fork repo 後依文件編譯

### zhongguo-traditional-colors（中國傳統色）
- **用途**：742 個中國傳統色彩，含同類、互補、分裂互補等完整配色方案，附 Markdown 資源
- **GitHub**：`github.com/nevertoday/zhongguo-traditional-colors`
- **網站**：`nevertoday.github.io/zhongguo-traditional-colors/`
- **安裝**：直接使用網站或下載 Markdown 配色表

### forge-neo-ideogram4（Forge Neo 擴充）
- **用途**：Forge Neo 的 Ideogram 4 擴充，含視覺化 JSON prompt 建構器
- **GitHub**：`github.com/Whatwhatio/forge-neo-ideogram4`
- **安裝**：依 Forge Neo 擴充安裝流程

### Huihui Gemma 4 Abliterated 系列（Ollama）
- **用途**：移除審查限制的 Gemma 4 各尺寸版本（2B/4B/12B/26B/31B），可本地運行
- **安裝指令**：
  ```bash
  ollama run huihui_ai/gemma-4-abliterated:E2b-qat
  ollama run huihui_ai/gemma-4-abliterated:E4b-qat
  ```
- **Hugging Face**：`huggingface.co/huihui-ai/`

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| CoreAI Model Zoo | 本地 LLM 框架 | GitHub Clone | iPhone/Apple Silicon 跑 LLM | [@JackdeS11](https://x.com/JackdeS11) |
| mlx2coreai | 模型轉換工具 | pip install | MLX → Apple CoreAI 格式 | [@lllucas](https://x.com/lllucas) |
| ComfyTV | ComfyUI 應用 | ComfyUI Manager | 無限畫布 ComfyUI 介面 | [@jtydhr88](https://x.com/jtydhr88) |
| IdeogramHelper | ComfyUI 節點 | ComfyUI Manager | Ideogram bbox prompt 輔助 | [@0xNynxz](https://x.com/0xNynxz) |
| DualLoraStack | ComfyUI 節點 | ComfyUI Manager | 雙 LoRA 同步載入 | [@PhotogenicWeekE](https://x.com/PhotogenicWeekE) |
| D2-nodes-ComfyUI | ComfyUI 節點集 | ComfyUI Manager | Prompt 清理/文字處理 | [@el2da_ai](https://x.com/el2da_ai) |
| image-to-prompt | CLI 工具 | Git Clone | 圖片自動生成 bbox prompt | [@cocktailpeanut](https://x.com/cocktailpeanut) |
| html-video | Web 工具 | Git Clone | Agent 驅動影片生成 | [@tuturetom](https://x.com/tuturetom) |
| ds4 多 GPU Fork | 推論引擎 | Git Clone + 編譯 | DeepSeek 多 GPU 分層推論 | [@support_huihui](https://x.com/support_huihui) |
| zhongguo-traditional-colors | 設計資源 | GitHub / 網站 | 742 中國傳統色配色方案 | [@xiaoxiaodong01](https://x.com/xiaoxiaodong01) |
| forge-neo-ideogram4 | Forge Neo 擴充 | 擴充安裝 | Ideogram 4 視覺化 prompt 建構 | [Whatwhatio](https://github.com/Whatwhatio) |
| Huihui Gemma 4 Abliterated | 本地 LLM | `ollama run` | 無審查 Gemma 4 各尺寸 | [@support_huihui](https://x.com/support_huihui) |
| Flux.2-Klein-9B LoRA（系列）| HF LoRA 模型 | ComfyUI 載入 | 角色換裝/漫畫彩色/風格遷移 | [@thedeoxen](https://huggingface.co/thedeoxen) |
| ideogram-4-gguf | GGUF 量化模型 | llama.cpp 載入 | 低 VRAM 運行 Ideogram 4 | [@realrebelai](https://x.com/realrebelai) |

---

## 🎯 本週趨勢

### 1. 端側 AI 推論速度突破臨界點
Apple CoreAI 框架上，Qwen3-VL 2B 在 iPhone 17 Pro 達 33 tok/s、M4 Max 達 188 tok/s；Xiaomi MiMo-V2.5-Pro 聲稱在消費級 GPU 上以 1T 參數模型突破 1000 tok/s。本地 AI 的速度已逼近甚至超越部分雲端服務，「無雲端依賴」正成為新的賣點標準。

### 2. Ideogram 4.0 帶動 JSON Prompt 工程新範式
開源一週後，bounding box JSON prompting 迅速成為社群默認操作。從自動化工具（image-to-prompt）到 ComfyUI 節點（IdeogramHelper），整個工具鏈圍繞這個新範式快速建立。LoRA 微調也已在 fal.ai 平台商業化。

### 3. Claude/Fable 作為創意中介層的角色確立
Claude Fable 5 不只是聊天機器人，本週多個案例顯示其作為「創意生成引擎」的定位：遊戲生成（Higgsfield Games）、互動藝術工具（水墨畫板）、3D 場景構建，Fable 已成為複雜創意工作流程中的核心 Agent。

### 4. Gaussian Splatting 技術快速商業化與平民化
TripoSplat（MIT License）達 SOTA 3D Gaussian 生成；Insta360 推出 feedforward 360 GS 重建（MIT License）；KIRI Engine 實現 Blender 中重新打光 Splat；World Labs 在瀏覽器直接渲染百萬 Splat。從研究工具到創作者日常工具的距離正急速縮短。
