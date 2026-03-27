週報內容已準備好，以下是完整內容，你可以直接複製或授權我寫入檔案：

---

# OpenCode 週報 - 2026.03.27

---

## 🔥 本週焦點

### 1. Seedance 2.0 影片生成品質大躍進
ByteDance 的 Seedance 2.0 本週持續成為焦點。多餘肢體問題大幅改善（「extra limbs is cooked」），Omni Reference 功能讓使用者能以角色圖 + 裝甲圖生成高品質「變身場景」，達成接近真人拍攝的質感。社群也釋出 `seedance-prompt-skill`，能將一句話創意轉為結構化高品質中文提示詞。
> 來源：@nachos2d、@iX00AI、@wherecall1

### 2. 本地 LLM 效能優化：Qwen3.5 + turboquant / vLLM Intel Arc
@0xSero 以 RTX 5090 測試 turboquant，將 Qwen3.5-27B-AWQ-4bit 的可用 context 從 ~44,688 token 提升至 **81,920 token**。同期，vLLM 宣布支援 Intel Arc Pro B-Series GPU（24GB VRAM、456 GB/s 頻寬），MoE 硬體效率達 80%+，為非 NVIDIA 用戶提供高性價比 LLM serving 選項。
> 來源：@0xSero、@vllm_project

### 3. InfiniDepth 深度估計開源（CVPR 2026 入選）
ZJU3DV 團隊的 InfiniDepth 獲 CVPR 2026 接受並同步開源。利用 TSDF 直接從深度圖重建網格，表面品質極為乾淨，完全不需後處理精修。
> 來源：@ChrisAtKIRI｜github.com/zju3dv/InfiniDepth

### 4. Blender × AI 即時渲染工作流程
@Yokohara_h 展示以 FLUX.2-klein 對 Blender Viewport 進行即時圖像生成，速度接近傳統 Renderer。@repalash 發布 threepipe，將 Blender Geometry Nodes 移植至 three.js。NVIDIA Studio 也推廣 Blender → ComfyUI 影片 storyboard 工作流程。
> 來源：@Yokohara_h、@repalash、@NVIDIAStudio

### 5. opencli-rs：Rust 重寫，速度最高 12 倍
完整重寫 opencli，二進位僅 4.7MB，零執行時依賴，支援 55+ 站點（Bilibili、Twitter、Reddit、知乎、小紅書、YouTube、HN 等），記憶體省 10 倍，速度快 12 倍，全平台支援。
> 來源：@nash_su

---

## 🛠️ 工具清單

### turboquant
- **用途**：擴展 vLLM 上 AWQ 量化模型的可用 context 長度
- **安裝**：`git clone https://github.com/0xSero/turboquant && pip install -r requirements.txt`

### InfiniDepth
- **用途**：高精度單目深度估計，TSDF 直接重建乾淨 3D 網格
- **安裝**：`git clone https://github.com/zju3dv/InfiniDepth && pip install -r requirements.txt`

### vLLM（Intel Arc Pro B-Series）
- **用途**：高效能 LLM serving，非 NVIDIA GPU 選項
- **安裝**：`pip install vllm`

### opencli-rs
- **用途**：多站點內容抓取 CLI（55+ 站點），4.7MB，零依賴
- **安裝**：GitHub Releases 下載二進位 → `chmod +x opencli-rs && sudo mv /usr/local/bin/`

### termaid
- **用途**：終端機直接渲染 Mermaid 圖表，純 Python 零依賴
- **安裝**：`pip install termaid`

### threepipe
- **用途**：Blender Geometry Nodes → three.js 移植，Web 程序化建模
- **安裝**：`npm install @threepipe/viewer`
- **GitHub**：github.com/repalash/threepipe

### micro-facemesh
- **用途**：瀏覽器即時 Face Mesh 人臉追蹤
- **安裝**：`npm install @svenflow/micro-facemesh`

### Extract Sharpest Frame
- **用途**：自動從影片擷取最清晰畫格，新增 PNG 匯出
- **安裝**：`git clone https://github.com/Kotohibi/Extract_sharpest_frame`（binary 即將發布）

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|---------|---------|------|
| turboquant | LLM 推論優化 | git + pip | 擴展 AWQ 模型 context 至 81,920 token | @0xSero |
| InfiniDepth | 深度估計 | git + pip | 單目深度 → 乾淨 3D 網格（CVPR 2026） | @ChrisAtKIRI |
| vLLM (Intel Arc) | LLM Serving | pip install vllm | Intel GPU 推論，MoE 80%+ | @vllm_project |
| daVinci-MagiHuman | 影片生成 | 論文待開源 | 影片 + 音訊聯合生成，15B Transformer | @stefan_fee |
| opencli-rs | 開發者 CLI | GitHub 二進位 | 55+ 站點抓取，12x 速度，4.7MB | @nash_su |
| termaid | 視覺化 CLI | pip install | 終端機渲染 Mermaid（零依賴） | @QingQ77 |
| threepipe | Web 3D | npm install | Blender Geometry Nodes → three.js | @repalash |
| micro-facemesh | Web AI | npm install | 瀏覽器即時 Face Mesh 追蹤 | @nsthorat |
| Extract Sharpest Frame | 影片工具 | git clone | 自動擷取最清晰畫格 + PNG | @naribubu |
| FLUX.2-klein (Blender) | AI 渲染 | Blender 插件 | Viewport 即時 AI 圖像生成 | @Yokohara_h |

---

## 🎯 本週趨勢

### 1. 閉源 AI 影片生成進入實用門檻
Seedance 2.0 的品質改善代表 AI 影片已從「演示玩具」轉向「創作工具」。開源陣營的 daVinci-MagiHuman（影片 + 音訊聯合生成）值得持續關注。

### 2. Rust 重寫浪潮延伸至 CLI 工具
opencli-rs 是典型案例：12 倍速度、4.7MB、零依賴。這個趨勢對本地工具鏈選型影響持續擴大。

### 3. Blender 成為 AI 工作流程的核心編排層
NVIDIA Studio、@Yokohara_h、@repalash 各自從不同角度強化 Blender 的 AI 整合，Blender 正演化為多模態生成的前端編排介面。

### 4. 本地 LLM context 長度競賽白熱化
turboquant 將實用 context 拉到 81,920 token，加上 Intel Arc 支援普及，2026 年的重點已從「能不能跑」轉移至「能跑多長的 context」。

---

如需寫入檔案，請授權。本週書籤主軸是：**AI 影片實用化 + 本地 LLM context 擴展 + Blender AI 整合 + Rust CLI 效能化**，工具品質整體偏高，尤其 InfiniDepth 和 opencli-rs 是本週最值得立即試用的項目。
