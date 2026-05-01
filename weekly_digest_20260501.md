# OpenCode 週報 - 2026.05.01

---

## 🔥 本週焦點

### 1. AI 世界模型：從單張圖像生成完整 3D 空間
本週多個世界模型同場競技。**World Labs** 的 **Spark 2.0** 搭配 LoD 系統，在瀏覽器中即時渲染 6000 萬個 Gaussian Splat 的暗黑奇幻世界，並透過 Three.js 驅動。**Echo-2** 宣稱能從單張圖像生成具備物理一致性的 3D 空間（採用 3DGS），支援遊戲與機器人訓練。**Lyra 2**（NVIDIA，Apache 2.0）與 **Matrix3D**（SkyworkAI）也加入比較行列，世界模型從研究走向實用工作流的速度明顯加快。
> 來源：[@theworldlabs](https://x.com/theworldlabs)、[@tokufxug](https://x.com/tokufxug)、[@AKurian001](https://x.com/AKurian001)

---

### 2. Blender MCP 正式開源，LLM 可直接操控 3D 軟體
**Blender Lab** 將 Blender MCP 伺服器正式開源，任何支援 MCP 協議的 LLM 現在都可以直接控制 Blender 進行場景分析、多邊形最佳化與渲染。這是「AI 助手操控創作軟體」生態中的重要里程碑。
> 來源：[@msurguy](https://x.com/msurguy)、[@kurono73](https://x.com/kurono73)  
> 專案：https://projects.blender.org/lab/blender_mcp

---

### 3. 本地 LLM 效能競賽：Qwen3.6-27B 成為焦點
本週大量測試數據湧現。單張 RTX 3090 (24GB) 可跑 Qwen3.6-27B 達 **80 TPS**（fp8 + MTP）；RTX 5090 可達 **140 TPS**（nvfp4）；雙 3090 TP=2 配置可達 **78 TPS** 並支援 262K context。**Structured CoT** 技巧（用語法約束 `<think>` 區塊）讓推理 token 縮減 **22x**，且 HumanEval+ 準確率無損失。**MiMo-V2.5**（小米，MIT 授權，1M context）在中文語音辨識上超越 ElevenLabs Scribe v2。
> 來源：[@stevibe](https://x.com/stevibe)、[@0xkeenz](https://x.com/0xkeenz)、[@andthatto](https://x.com/andthatto)、[@XiaomiMiMo](https://x.com/XiaomiMiMo)

---

### 4. GPT Image 2 全景圖像熱：360° 場景生成工作流爆發
GPT Image 2 的 equirectangular 360° 全景生成能力引發創作者社群廣泛實驗。從靜態全景圖接 Seedance 2.0 做動態化，到利用間取圖（平面圖）指定攝影機角度，再到用提示詞生成 PBR 貼圖（Normal / Depth / ORM）—— 多個實用工作流本週密集公開，幾乎已成為獨立的創作方法論。
> 來源：[@Anirole_jp](https://x.com/anirole_jp)、[@onofumi_AI](https://x.com/onofumi_AI)、[@blixt](https://x.com/blixt)、[@genel_ai](https://x.com/genel_ai)

---

### 5. Anima + ControlNet-LLLite 生態系快速成形
kohya-ss 的 **Anima** 模型搭配 **LLLite** 架構的 ControlNet（支援 Lineart / Depth / Pose / Fake Scribble）進入實用階段。anytest 風格的預訓練權重（1000/2000 step 各有適用場景）本週公開，ComfyUI 自訂節點同步更新。社群實測顯示自然語言可有效控制角色姿勢與武器位置，不再依賴 ガチャ（隨機抽取）。
> 來源：[@kohya_tech](https://x.com/kohya_tech)、[@higana_yogana](https://x.com/higana_yogana)、[@FET_SHIZAIBU](https://x.com/FET_SHIZAIBU)

---

## 🛠️ 工具清單

### Blender MCP Server
- **用途**：讓任何 LLM 透過 MCP 協議控制 Blender，可做場景分析、多邊形最佳化
- **授權**：開源（Blender Lab 官方）
- **GitHub**：https://projects.blender.org/lab/blender_mcp
- **安裝**：依 README 設定 MCP 端點，接入支援 MCP 的 LLM 客戶端

---

### ComfyUI-SAM3DRender
- **用途**：單張圖像轉換為含空洞的 3D 空間（.ply），支援任意角度渲染
- **GitHub**：https://github.com/tori29umai0123/ComfyUI-SAM3DRender
- **安裝**：
  ```bash
  cd ComfyUI/custom_nodes
  git clone https://github.com/tori29umai0123/ComfyUI-SAM3DRender
  pip install -r requirements.txt
  ```

---

### ComfyUI-Sapiens2（kijai）
- **用途**：Sapiens 2 姿勢估測、法線圖、語意分割自訂節點（含 Gaussian Splat 輸出）
- **GitHub**：https://github.com/kijai/ComfyUI-Sapiens2
- **安裝**：透過 ComfyUI Manager 搜尋 `Sapiens2` 一鍵安裝

---

### llama.cpp-deepseek-v4-flash（antirez）
- **用途**：DeepSeek V4 Flash 的實驗性 llama.cpp 實作，128GB RAM 可跑完整推理
- **GitHub**：https://github.com/antirez/llama.cpp-deepseek-v4-flash
- **模型**：https://huggingface.co/antirez/deepseek-v4-gguf
- **安裝**：
  ```bash
  git clone https://github.com/antirez/llama.cpp-deepseek-v4-flash
  cmake -B build && cmake --build build -j
  ```

---

### vllm-swift（Apple Silicon）
- **用途**：vLLM 的原生 Swift/Metal 後端，針對 Apple Silicon 最佳化，Python 不在推理熱路徑
- **GitHub**：https://github.com/TheTom/vllm-swift
- **安裝**：
  ```bash
  brew tap TheTom/tap && brew install vllm-swift
  ```

---

### structured-cot
- **用途**：用 GBNF 語法約束 `<think>` 區塊，讓 Qwen3.6 推理 token 縮減 22x，不損準確率
- **GitHub**：https://github.com/andthattoo/structured-cot
- **安裝**：
  ```bash
  pip install structured-cot
  ```

---

### AI4AnimationPy（Facebook Research）
- **用途**：AI 即時生成每幀姿勢的動畫引擎，支援雙足與四足步態控制，含瀏覽器 Demo
- **GitHub**：https://github.com/facebookresearch/ai4animationpy
- **Demo**：https://paulstarke-ai4animationpy.hf.space
- **安裝**：提供 1-Click Launcher，README 中有詳細說明

---

### Luce DFlash（本地推理引擎）
- **用途**：純 C++ 推理引擎，27B 模型在 24GB VRAM 達 130 tok/s，比標準自迴歸快 3.4x，含 Speculative Decoding
- **特點**：Zero Python in hot path，無需 vLLM 或 llama.cpp

---

### cuimao-translator（Claude Code Skill）
- **用途**：一鍵將英文 PDF 翻譯為流暢中文的 Claude Code 技能
- **GitHub**：https://github.com/Cuimao777/cuimao-translator

---

### Chro-NOS（CLIP STUDIO PAINT 備份工具）
- **用途**：自動記錄 CSP 繪圖歷史，可隨時回到任意繪製中間點，含工作時間視覺化，多語言支援
- **官網**：https://cohistudio-lab.com

---

### VirtuCamera 2
- **用途**：用手機（iOS/Android）作為虛擬攝影機，現支援 Blender、Maya、Houdini、Cinema 4D、3ds Max
- **官網**：https://virtucamera.com

---

### DeepSeek-V4 AutoRound INT4（Intel）
- **用途**：Intel AutoRound 量化的 DeepSeek-V4 Pro/Flash，無需 MXFP4 硬體支援
- **模型**：
  - https://huggingface.co/Intel/DeepSeek-V4-Pro-W4A16-AutoRound
  - https://huggingface.co/Intel/DeepSeek-V4-Flash-W4A16-AutoRound

---

### sarashina2.2-tts（SBIntuitions）
- **用途**：基於 LLM 的日文語音合成，高自然度、高再現性，支援日英雙語及聲質複製
- **模型**：https://huggingface.co/sbintuitions/sarashina2.2-tts

---

### LTX 2.3 Post Apocalyptic LoRA
- **用途**：LTX Video 2.3 的後啟示錄風格電影感 LoRA
- **模型**：https://huggingface.co/vrgamedevgirl84/LTX_2.3_Post_Apocalyptic_Style_LoRa

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---|---|---|---|---|
| Blender MCP Server | MCP 伺服器 | git clone + 設定 MCP | LLM 控制 Blender | blender.org |
| ComfyUI-SAM3DRender | ComfyUI 節點 | git clone + pip | 圖像轉 3D + 多角度渲染 | GitHub |
| ComfyUI-Sapiens2 | ComfyUI 節點 | ComfyUI Manager | 姿勢/法線/分割估測 | GitHub |
| llama.cpp-deepseek-v4-flash | CLI 推理 | cmake build | 本地跑 DeepSeek V4 Flash | GitHub |
| vllm-swift | 推理後端 | brew install | Apple Silicon vLLM | GitHub |
| structured-cot | Python 套件 | pip install | 壓縮 CoT 推理 token | GitHub |
| AI4AnimationPy | 動畫引擎 | 1-Click Launcher | AI 即時生成動畫姿勢 | GitHub/HF |
| Luce DFlash | 推理引擎 | 二進位下載 | 本地超高速 27B 推理 | Twitter |
| cuimao-translator | Claude Code Skill | Claude Code 載入 | 英文 PDF → 中文翻譯 | GitHub |
| Chro-NOS | 桌面應用 | 官網下載 | CSP 繪圖歷史備份 | cohistudio-lab.com |
| VirtuCamera 2 | 手機 App | App Store/Google Play | 手機作虛擬攝影機 | virtucamera.com |
| DeepSeek-V4 AutoRound INT4 | HF 模型 | huggingface-cli download | 低門檻跑 DeepSeek V4 | HuggingFace |
| sarashina2.2-tts | HF 模型 | huggingface-cli download | 日文 LLM 語音合成 | HuggingFace |
| LTX 2.3 Post Apocalyptic LoRA | HF 模型 | ComfyUI 載入 | 後啟示錄風格影片生成 | HuggingFace |
| Overworld Waypoint 1.5 | 桌面應用 | 1-Click Launcher | 8GB VRAM 即時擴散世界模型 | HuggingFace |
| MiMo-V2.5（小米） | 開源模型 | HF 下載 / Ollama | 1M context，中文 ASR 最強 | HuggingFace |

---

## 🎯 本週趨勢

### 1. 3D Gaussian Splat 進入瀏覽器即時渲染時代
World Labs 展示了 6000 萬 Gaussian Splat 在瀏覽器中即時渲染，Three.js + LoD 系統已讓這成為現實。Gaussian Splat 不再只是研究展示，而是正在成為遊戲、建築視覺化的實用管線。

### 2. 本地 27B 模型效能跨越臨界點
Qwen3.6-27B 在單張消費級 GPU 上突破 100+ TPS，加上 structured CoT 讓推理成本大幅降低，本地 LLM 在 coding 任務上與雲端 API 的差距正在快速縮小，「本地優先」工作流的採用門檻大幅降低。

### 3. ComfyUI 節點生態系向動畫與 3D 控制深度延伸
本週新增的節點不再只是圖像濾鏡，而是 3D 空間渲染（SAM3DRender）、姿勢驅動（Sapiens2）、音訊生成（Woosh）、ControlNet 架構擴展（Anima LLLite）—— ComfyUI 正在演化為跨媒體 AI 管線的整合框架。

### 4. AI 驅動的一鍵式創作工具崛起
從 Codex + GPT Image 2 的 10 分鐘生成完整瀏覽器遊戲，到 Pika Agents 用對話完成影片製作，「單一提示詞完成複雜創作物」的閾值正在快速下降。開發者工具（Claude Code Skill、Codex CLI）與 AI 內容生成的整合愈來愈緊密。

---

*資料來源：X/Twitter 書籤 · 整理日期：2026-05-01*
