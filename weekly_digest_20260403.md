# OpenCode 週報 - 2026.04.03

---

## 🔥 本週焦點

### 1. Google Gemma 4 正式發布，開源生態全面爆發

Google 發布 Gemma 4 系列（含 27B MoE 26B 等規格），宣稱建立於 Gemini 3 同等研究基礎，支援進階推理與 agentic 工作流。發布後 24 小時內社群即展開全面整合：

- **fine-tune**：`kai-os/gemma4-31b-Opus-4.6-reasoning` 以 Claude Opus 4.6 thinking 資料微調，改善模型品質與個性
- **瀏覽器本地推理**：HuggingFace `webml-community/Gemma-4-WebGPU` 用 transformers.js 在瀏覽器內 100% 本機運行
- **低記憶體執行**：`Anemll/anemll-flash-llama.cpp` Flash MoE 分支支援 SSD expert streaming，16GB Mac 可跑 Gemma 4 26B（僅 ~3.8B 啟動參數）
- **量化壓縮**：TurboQuant+ 實驗分支將 31B 從 30.4 GB 壓縮至 18.9 GB；NVIDIA NVFP4 版本在 GPQA 保持 99.7% 精度

**來源**：@Google、@kaiostephens、@ClementDelangue、@Anemll、@no_stp_on_snek

---

### 2. Claude Code 源碼洩露事件：從分析到衍生工具全紀錄

Claude Code 源碼透過 npm registry 中的 `.map` 檔案洩露，引發大規模社群分析浪潮。本週相關進展：

- **源碼分析**：OpenAI Codex 分析洩露碼找出 `autoCompact`（自動上下文壓縮）機制無限重試 bug，曾有單一會話失敗 **3272 次**，是 token 異常消耗元凶
- **可編譯版本**：`roger2ai/Claude-Code-Compiled` 修復所有缺失依賴，主力工具（Bash、檔案讀寫、搜尋、WebFetch）均正常
- **Android 移植**：確認在 30 美元 Android 手機上成功運行，修復 `/tmp` 硬編碼問題
- **架構文件化**：社群將源碼轉化為 50 課架構課程（`markdown.engineering`）、Mintlify 文件站
- **反追蹤工具**：`cc-gateway` 攔截 640+ 種遙測事件、40+ 維度指紋，在出網前全部重寫身份指纹

**來源**：@Fried_rice、@CatFly、@marshallrichrds、@aigclink、@Vineetwts

---

### 3. Seedance 2.0 視頻生成技術突破

ByteDance Seedance 2.0 本週大量實際案例流出，社群驗證其核心能力：

- **2D 角色穩定性**：無論鏡頭角度如何變化，2D 動漫角色不會被強制 3D 化，消除傳統 v2v 需要的臉部形變補償
- **表情可控性**：可在 previs 階段指定大致表情走向
- **成本分析**：RunningHub API 服務，720p 15 秒約 375 日圓（1 分鐘約 1440 日圓），4K 15 秒約 555 日圓
- **省錢方案**：先用 480p Standard 生成後再用 TopazAI 等工具升解析度，大幅降低成本

**來源**：@craftcapitallab、@kiyoshi_shin、@yukimino_doga、@onofumi_AI

---

### 4. 本地 LLM 效能全面突破：Flash MoE、1-bit 模型與 KV 壓縮

多項本地推理技術同時爆發：

- **Flash MoE SSD Streaming**：`anemll-flash-llama.cpp` 讓 M5 Max 128GB 跑 Kimi K2.5（1T 參數）峰值達 **7.5 t/s**；Qwen 3.5 35B-A3B 在 M4 Mac Mini 16GB 可運行
- **1-bit 模型 Bonsai**：Qwen 3 為底、1.7B～8B 參數、bf16 的 14x 壓縮、8x 加速，256MB～1.2GB，專為邊端設計
- **TurboQuant+ KV Cache**：4.6x 壓縮，Apple Silicon + Metal 打通，已支援 Gemma 4
- **Draw Things Metal 量化**：Metal Quantized Attention + Int8 矩陣乘法，M5 系列與 A19 Pro 效能大躍升
- **tinygrad eGPU**：Apple 正式核准 AMD 和 NVIDIA eGPU 驅動

**來源**：@Anemll、@HessianFree、@QingQ77、@drawthingsapp、@__tinygrad__

---

### 5. ⚠️ 安全警報：axios npm 供應鏈攻擊

**Critical**：axios@1.14.1 與 axios@0.30.4 遭投毒，植入 `plain-crypto-js` 惡意模組，會安裝遠端存取木馬（RAT）。

排查方式：
```bash
npm ls axios
npm ls plain-crypto-js
```

若發現惡意版本，立即降版或升版至安全版本。所有使用 axios 的 Agent 環境均需排查。

**來源**：@feross、@evilcos

---

## 🛠️ 工具清單

### 1. anemll-flash-llama.cpp
- **用途**：Flash MoE SSD expert streaming，讓低記憶體 Mac 可運行大型 MoE 模型（Gemma 4 26B、Kimi K2.5 1T）
- **安裝**：
  ```bash
  git clone https://github.com/Anemll/anemll-flash-llama.cpp -b Gemma-4-MoE-26B
  cd anemll-flash-llama.cpp && cmake -B build && cmake --build build
  ```
- **GitHub**：`github.com/Anemll/anemll-flash-llama.cpp`

---

### 2. free-code（Claude Code 開源替代）
- **用途**：移除遙測的 Claude Code 開源版，支援 OAuth、本地模型（Ollama）、OpenAI Codex
- **安裝**：
  ```bash
  npm install -g free-code
  # 本地模型設定
  export ANTHROPIC_BASE_URL=http://localhost:11434
  export ANTHROPIC_AUTH_TOKEN=ollama
  export ANTHROPIC_API_KEY=""
  free-code -p "hello" --model <model-name>
  ```
- **GitHub**：`github.com/paoloanzn/free-code`

---

### 3. Claude-Code-Compiled
- **用途**：洩露源碼的可編譯修復版，Bash、檔案讀寫、搜尋、WebFetch 均正常
- **安裝**：
  ```bash
  git clone https://github.com/roger2ai/Claude-Code-Compiled
  cd Claude-Code-Compiled && npm install && npm run build
  ```
- **GitHub**：`github.com/roger2ai/Claude-Code-Compiled`

---

### 4. TurboQuant+
- **用途**：本地 LLM KV cache 4.6x 壓縮，Apple Silicon + Metal 支援，已相容 Gemma 4、Qwen3.5
- **安裝**：
  ```bash
  git clone https://github.com/TheTom/turboquant_plus
  # Gemma 4 weight compression 實驗分支
  git checkout main && pip install -r requirements.txt
  ```
- **GitHub**：`github.com/TheTom/turboquant_plus`

---

### 5. Video Frame Extractor
- **用途**：AI 動畫影片 sprite 素材切割工具，支援格線切割、背景刪除，免安裝（瀏覽器運行），免費
- **安裝**：直接開啟瀏覽器使用，無需安裝
- **GitHub/Link**：（作者 @haruka_apps，推文內含連結）

---

### 6. SpatialLM
- **用途**：LLM 推理力延伸至 3D 點雲空間理解，解析牆、門結構與空間連結，輸出結構化資料
- **安裝**：
  ```bash
  git clone https://github.com/manycore-research/SpatialLM
  pip install -r requirements.txt
  ```
- **GitHub**：`github.com/manycore-research/SpatialLM`
- **HuggingFace**：`manycore-research (Manycore Research)`

---

### 7. ComfyUI-Yedp-Action-Director
- **用途**：ComfyUI 節點，支援 scene splats、HDRI rendering、scene saving，適合進階場景合成
- **安裝**：
  ```bash
  # ComfyUI Manager 搜尋 Yedp-Action-Director
  # 或手動：
  cd ComfyUI/custom_nodes
  git clone https://github.com/yedp123/ComfyUI-Yedp-Action-Director
  ```
- **GitHub**：`github.com/yedp123/ComfyUI-Yedp-Action-Director`

---

### 8. ComfyUI SeeThrough Plugin
- **用途**：單張 2D 動漫角色圖自動分解為獨立語意層（眼睛、頭髮、四肢等），輸出帶深度排序的 PSD，用於 Live2D
- **安裝**：
  ```bash
  cd ComfyUI/custom_nodes
  git clone https://github.com/shitagaki-lab/see-through
  ```
- **GitHub**：`github.com/shitagaki-lab/see-through`

---

### 9. OmniVoice（小米 × Kaldi 之父）
- **用途**：零樣本 TTS，支援 600+ 語言，單階段架構，以 Qwen3-0.6B 初始化，效果超越 MaskGCT、F5-TTS
- **安裝**：（HuggingFace 模型，詳見論文及官方頁面）
- **HuggingFace**：（@FeitengLi 推文內含連結）

---

### 10. Sarashina2.2-OCR
- **用途**：日文文書 OCR，保留版面轉 Markdown，支援縱書、圖表位置輸出
- **安裝**：
  ```bash
  pip install transformers
  # HuggingFace: sbintuitions/sarashina2.2-ocr
  ```
- **HuggingFace**：`huggingface.co/spaces/hayas/sarashina2.2-ocr`

---

### 11. Falcon-OCR（TII UAE）
- **用途**：0.3B 輕量 OCR，early-fusion 架構，olmOCR 平均 80.3% 精度，vLLM 服務達 5825 tok/s（單 A100）
- **安裝**：
  ```bash
  pip install vllm
  # model: tiiuae/Falcon-OCR
  ```
- **HuggingFace**：`huggingface.co/nvidia/Gemma-4-31B-IT-NVFP4`（參見 @HuggingPapers）

---

### 12. LTX 2.3 Multifunctional Desktop Tool
- **用途**：影片生成，支援 LoRA、多幀插入、lip-sync、影片增強，繞過 32GB VRAM 限制
- **安裝**：
  ```bash
  git clone https://github.com/hero8152/LTX2.3-Multifunctional
  pip install -r requirements.txt
  ```
- **GitHub**：`github.com/hero8152/LTX2.3-Multifunctional`
- **HuggingFace**：`huggingface.co/dx8152/LTX2.3-Multifunctional`

---

### 13. Bonsai（1-bit 邊端 AI）
- **用途**：Qwen 3 為底的 1-bit 權重模型，1.7B～8B，14x 壓縮 vs bf16，256MB～1.2GB，專為邊端部署
- **安裝**：（@HessianFree，剛出 stealth，GitHub/HF 連結見推文）

---

### 14. Qwopus 27B v3（Jackrong）
- **用途**：Claude Opus 4.6 推理蒸餾的 Qwen3.5-27B，第三代，GGUF 格式
- **安裝**：
  ```bash
  # HuggingFace: Jackrong/Qwopus3.5-27B-v3-GGUF
  ollama pull <gguf-path>  # 或用 llama.cpp
  ```
- **HuggingFace**：`huggingface.co/Jackrong/Qwopus3.5-27B-v3-GGUF`

---

### 15. cc-gateway（Claude Code 反遙測）
- **用途**：攔截 Claude Code 640+ 種遙測事件、40+ 維度設備指紋，出網前全部重寫為標準化身份
- **安裝**：（@aigclink 推文含 GitHub 連結）

---

### 16. VCC（lllyasviel）
- **用途**：AI Agent 專用編譯器
- **安裝**：
  ```bash
  git clone https://github.com/lllyasviel/VCC
  ```
- **GitHub**：`github.com/lllyasviel/VCC`

---

### 17. Microsoft Harrier 多語言 Embedding 模型
- **用途**：多語言 embedding，0.6B，社群評價效果出色
- **安裝**：
  ```bash
  pip install transformers
  # model: microsoft/harrier-oss-v1-0.6b
  ```
- **HuggingFace**：`huggingface.co/microsoft/harrier-oss-v1-0.6b`

---

### 18. AI-Novel-Writing-Assistant
- **用途**：長篇小說 AI Native 開源系統，Agent + 世界觀引擎 + RAG + 完整生產工作流
- **安裝**：
  ```bash
  git clone https://github.com/ExplosiveCoderflome/AI-Novel-Writing-Assistant
  pip install -r requirements.txt
  ```
- **GitHub**：`github.com/ExplosiveCoderflome/AI-Novel-Writing-Assistant`

---

### 19. ComfyUI-PascalEditor（3D 建築編輯器）
- **用途**：將 Pascal Editor 3D 建築編輯器移植進 ComfyUI，含 2D 草圖到 3D 建築快速建模
- **安裝**：
  ```bash
  cd ComfyUI/custom_nodes
  git clone https://github.com/jtydhr88/ComfyUI-PascalEditor
  ```
- **GitHub**：`github.com/jtydhr88/ComfyUI-PascalEditor`

---

### 20. GameDev Resources
- **用途**：社群維護的遊戲開發資源清單，含引擎、素材、音效、工具、教學，91 位貢獻者
- **安裝**：（GitHub repo，@sukh_saroy 推文含連結）

---

### 21. hermes-agent computer use
- **用途**：Hermes Agent 整合 computer use，手機下指令遠端控制 Mac 桌面（無沙箱，操作真實應用）
- **GitHub**：`github.com/NousResearch/hermes-agent/pull/3816`

---

### 22. SDXS 1B Upscaler（HuggingFace Space）
- **用途**：AI 圖像超解析度放大
- **HuggingFace**：`huggingface.co/spaces/LoveScapeAI`（@toyxyz3 推文）

---

### 23. vibeproxy（v1.8.120-opus46-fix）
- **用途**：Claude Code proxy 工具，支援 opus46 修復版
- **GitHub**：`github.com/anand-92/vibeproxy/releases/tag/v1.8.120-opus46-fix`

---

### 24. studio.morflax（Web 3D 場景創作）
- **用途**：瀏覽器 3D 場景製作，SVG 轉 3D，支援上傳自製 GLB 模型
- **Link**：`studio.morflax`（@ymt3d / @ModelingHappy 推文）

---

### 25. Blender Geometry Node French Cathedral
- **用途**：完全 procedural 的哥德式大教堂，40k 節點，無 boolean，純幾何節點
- **GitHub**：`github.com/IRCSS/Blender-Geometry-Node-French-Houses`

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|---------|---------|------|
| anemll-flash-llama.cpp | 本地推理引擎 | git clone + cmake | Flash MoE SSD streaming，低記憶體跑大型 MoE | GitHub |
| free-code | Claude Code 替代 | npm install -g free-code | 無遙測 CC，支援本地模型 | GitHub |
| Claude-Code-Compiled | CC 源碼修復版 | git clone + npm | 可編譯運行的洩露源碼修復版 | GitHub |
| TurboQuant+ | LLM 量化壓縮 | git clone | KV cache 4.6x 壓縮，Apple Silicon | GitHub |
| Video Frame Extractor | 影片工具 | 免安裝（瀏覽器） | AI 動畫 sprite 素材切割、背景刪除 | Web |
| SpatialLM | 空間 AI | git clone + pip | LLM 理解 3D 點雲空間結構 | GitHub / HF |
| ComfyUI-Yedp-Action-Director | ComfyUI 節點 | git clone | Scene splats、HDRI、場景存儲 | GitHub |
| ComfyUI SeeThrough | ComfyUI 節點 | git clone | 單張動漫圖分解為 Live2D 語意層 | GitHub |
| OmniVoice | TTS 模型 | HuggingFace | 零樣本 600+ 語言 TTS | HF |
| Sarashina2.2-OCR | OCR 模型 | HF Space / pip | 日文縱書文書 OCR，Markdown 輸出 | HF Space |
| Falcon-OCR | OCR 模型 | vLLM | 0.3B 輕量 OCR，高吞吐服務 | HF |
| LTX 2.3 Multifunctional | 影片生成 | git clone + pip | LoRA 支援、多幀插入、lip-sync | GitHub / HF |
| Bonsai | 邊端 LLM | HF（待公開） | 1-bit，14x 壓縮，256MB 起 | HF |
| Qwopus 27B v3 | 微調模型 | GGUF / ollama | Claude Opus 推理蒸餾 Qwen3.5 | HF |
| cc-gateway | 安全工具 | GitHub | Claude Code 遙測攔截與指紋重寫 | GitHub |
| VCC | Agent 編譯器 | git clone | AI Agent 專用編譯器 | GitHub |
| Microsoft Harrier | Embedding 模型 | pip + HF | 多語言 embedding，0.6B | HF |
| AI-Novel-Writing-Assistant | 創作系統 | git clone + pip | 長篇小說 AI 生產工作流 | GitHub |
| ComfyUI-PascalEditor | ComfyUI 節點 | git clone | 3D 建築場景編輯器 | GitHub |
| GameDev Resources | 資源清單 | GitHub | 遊戲開發全棧資源 91 貢獻者 | GitHub |
| hermes-agent computer use | Agent 工具 | GitHub PR | 手機遠端控制 Mac 桌面 | GitHub |
| SDXS 1B Upscaler | 圖像工具 | HF Space | AI 超解析度放大 | HF Space |
| vibeproxy | Proxy 工具 | GitHub Release | Claude Code proxy opus46 修復版 | GitHub |
| studio.morflax | Web 3D 工具 | 免安裝（瀏覽器） | SVG 轉 3D，GLB 上傳，場景創作 | Web |
| Blender Cathedral Geometry Nodes | Blender 資產 | git clone | 哥德式教堂 procedural 建模節點 | GitHub |
| gemma4-31b-Opus-4.6-reasoning | 微調模型 | HF | Gemma 4 31B + Claude Opus 推理微調 | HF |
| Gemma 4 WebGPU Demo | 瀏覽器推理 | HF Space | 瀏覽器 100% 本機跑 Gemma 4 | HF Space |
| PixelSmile LoRA | LoRA 模型 | HF + 自訂節點 | Qwen-Image-Edit 表情滑桿控制 | HF |

---

## 🎯 本週趨勢

### 1. Claude Code 透明化時代來臨
源碼洩露引發的連鎖反應遠超事件本身：社群在 48 小時內產出可編譯版、架構課程、反遙測工具、Mintlify 文件站。這標誌著 AI 開發工具從黑盒走向可解析、可改造的新階段。Anthropic 的 `autoCompact` bug（最高重試 3272 次）被社群找出，實際上加速了官方修復壓力。

### 2. 本地推理「記憶體牆」正在瓦解
Flash MoE SSD streaming、1-bit 模型（Bonsai）、TurboQuant+ KV 壓縮三條技術線同週爆發，加上 tinygrad eGPU 驅動正式上架，讓消費級硬體（M4 Mac Mini 16GB、16GB VRAM GPU）運行百億參數模型不再是夢。2026 年「本地優先 AI」趨勢正在加速落地。

### 3. 模型蒸餾成為快速獲取能力的捷徑
本週出現多個以 Claude Opus 4.6 / Claude 推理資料蒸餾的開源模型（Qwopus 27B v3、gemma4-31b-Opus-4.6-reasoning），社群正在系統性地將閉源 frontier 模型的能力轉移進開源生態。這條路線效率極高，預期未來數月會持續加速。

### 4. ComfyUI 持續成為多媒體 AI 的整合平台
本週 ComfyUI 新節點涵蓋：3D 建築編輯（PascalEditor）、anime 角色分解（SeeThrough）、場景 splat（Yedp-Action-Director）、LLM 圖像描述（QwenVL）。ComfyUI 的定位已從「Stable Diffusion 圖形介面」演化為「多媒體 AI 工作流操作系統」，吸引力遠超單一模型工具。

---

*週報資料來源：X/Twitter 書籤（2026.03.28～2026.04.03）*
*技術名詞保留原文，說明以繁體中文呈現*
