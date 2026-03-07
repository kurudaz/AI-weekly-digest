# OpenCode 週報 - 2026.03.06

> **本期焦點**: Qwen3.5 全線發布、Seedance 2.0 爆發、ComfyUI V3 Skills、Apple ANE 逆向
> **資料來源**: X 書籤精選 (過去 7 天)
> **生成時間**: 2026-03-06

---

## 🔥 本週焦點

### 1. Qwen3.5 全線發布 + 創始人離職
Qwen 發布 0.8B~35B 全系列多模態小模型：
- **35B-A3B MoE**：256 experts，單張 RTX 3090，112 tok/s，262K 上下文
- **小模型系列**（0.8B/2B/4B）：圖像辨識精度極高、瀏覽器 WebGPU 可跑
- **iPhone 本地推理**：tool-calling 20+ tok/s（[@jtdavies](https://x.com/jtdavies)）
- **27B 稠密版**：RTX 5090 上 240K 上下文，TPS 50

技術負責人 Junyang Lin 宣布離職，引發社群震動。

### 2. Seedance 2.0 席捲視頻生成圈
- 搭配 **Seedance Prompt Skill** 先學習再生成，效果大幅提升
- **Zopia AI**：端到端 AI 影片導演 Agent，一個想法 = 電影級成片
- 注意：NG 詞限制嚴格，可用英文替代片假名繞過

### 3. ComfyUI V3 Custom Node Skills 全套開源
9 個 Claude Code Skills 覆蓋 V3 API 全流程：node 基礎、進階 pattern、V1→V3 遷移。
來源：[@jtydhr88](https://x.com/jtydhr88) / [GitHub](https://github.com/jtydhr88/comfyui-custom-node-skills)

### 4. Apple ANE 逆向突破
開發者 maderix 成功逆向 Apple Neural Engine 私有 API，繞過 CoreML 直接在 ANE 上實現神經網路**訓練**。M4 的 16 核 ANE 算力終於可被完整利用。

### 5. Speculative Speculative Decoding (SSD)
新 LLM 推理演算法，比最強推理引擎快 2 倍。來源：[@tanishqkumar07](https://x.com/tanishqkumar07)

---

## 🛠️ 工具清單

### AI 模型 & 推理

#### Qwen3.5-35B-A3B
```bash
lms get qwen/qwen3.5-35b-a3b
# 單張 RTX 3090，112 tok/s，~21GB，262K 上下文
```

#### Qwen3.5 小模型（瀏覽器/手機）
```bash
# 瀏覽器 WebGPU：Transformers.js
# iOS/macOS MLX-Swift:
# github.com/ml-explore/mlx-swift-lm/pull/97  (Text)
# github.com/ml-explore/mlx-swift-lm/pull/120 (Vision)
```

#### Unsloth 微調 Qwen3.5
```bash
pip install unsloth
# 5GB VRAM 即可 LoRA 微調 Qwen3.5-2B，1.5x 更快
# GitHub: github.com/unslothai/unsloth
```

#### Hermes 4.3 36B Dense
```bash
# RTX 3090 上 Q4_K_M，35 tok/s，93.8% MATH-500，512K 上下文
```

---

### ComfyUI 節點

#### ComfyUI V3 Custom Node Skills
```bash
git clone https://github.com/jtydhr88/comfyui-custom-node-skills
# 9 個 Claude Code Skills，V3 API 全流程 + V1→V3 遷移
```

#### ComfyUI Panorama Stickers
```bash
git clone https://github.com/nomadoor/ComfyUI-Panorama-Stickers
# 360° 全景 ERP 預覽 + 裁切，支援 FLUX.2 Klein 4B/9B
```

#### SeedVR2 Tiler
```bash
# GitHub: BacoHubo/ComfyUI_SeedVR2_Tiler
# Tile 分割拼接用於 SeedVR2 超解析度
```

#### LTX-2.3
```bash
# ComfyUI 已內建支援（官方一度 404，社群搶先整合）
```

---

### 圖像處理

#### Qwen-Image-Layered-Control-V2
```
# ModelScope 上架
# 紅筆刷=目標圖層，綠=移除，黃=重疊，文字控制仍可用
```

#### FireRed-Image-Edit-1.1 + LoRA Zoo
```bash
# HuggingFace: FireRedTeam/FireRed-Image-Edit-1.1
# LoRA Zoo: FireRedTeam/FireRed-Image-Edit-LoRA-Zoo
# 含 8-step Lightning LoRA、Makeup、CoverCraft
```

#### QIE-Object-Remover-Bbox
```bash
# HuggingFace: prithivMLmods/QIE-2509-Object-Remover-Bbox
# Bounding box 選取 → 4 步快速推理移除物件
```

#### copainter 圖層分離
```
# copainter.info — 1 張圖 → 線稿/光/影/下塗り PSD 分離
```

---

### 視頻生成

#### Seedance 2.0 Prompt Skill
```bash
git clone https://github.com/songguoxs/seedance-prompt-skill.git
```

#### Zopia AI — AI Video Director Agent
```
# 端到端 AI 短劇/電影/品牌影片，對話式多 Agent 協同
# 支援 OpenClaw Bot 自動呼叫，7×24 持續製作
```

---

### 開發工具

#### Google Workspace CLI
```bash
# GitHub: googleworkspace/cli — Drive、Gmail 等一個 CLI 統管
```

#### ControlNet 訓練教學（Ostris AI Toolkit）
```bash
# 用 Flux.2 Klein 4B 訓練 ControlNet，含 image⇄sketch 雙向
```

---

### 設計 & 素材

#### Apple 官方 Mockup PSD（免費）
```
# Apple 公式免費公開，解析度官方品質
# 連結在 @gibkun1 推文回覆中
```

#### GB Dot Converter
```
# 圖片→Game Boy 風格像素圖
# deepblizzard.itch.io/gb-dot-converter
```

---

### AI 音樂

#### SUNO 音質 Meta Tag
```
[Mix-Intent: Isolate Stems, Maximize Separation]
# 加在 Lyric 欄頭部，減少頻帶重疊提升分離感
```

---

### 安全

#### OpenClaw 極簡安全實踐指南
```bash
git clone https://github.com/slowmist/openclaw-security-practice-guide
# 慢霧安全團隊：覆蓋提示詞注入、供應鏈投毒、破壞性操作
```

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|----------|----------|------|
| **Qwen3.5-35B-A3B** | AI 模型 | LM Studio | 本地 MoE 推理 | Qwen |
| **Qwen3.5 小模型** | AI 模型 | WebGPU/MLX | 瀏覽器/手機 | [@xenovacom](https://x.com/xenovacom) |
| **Unsloth Qwen3.5** | 微調 | `pip install` | 5GB LoRA | [GitHub](https://github.com/unslothai/unsloth) |
| **ComfyUI V3 Skills** | Skill | `git clone` | 節點開發 | [GitHub](https://github.com/jtydhr88/comfyui-custom-node-skills) |
| **Panorama Stickers** | ComfyUI | `git clone` | 360° 全景 | [GitHub](https://github.com/nomadoor/ComfyUI-Panorama-Stickers) |
| **FireRed-Edit-1.1** | 圖像 | HuggingFace | 身份一致性編輯 | FireRedTeam |
| **QIE-Object-Remover** | 圖像 | HuggingFace | Bbox 物件移除 | [@prithivMLmods](https://x.com/prithivMLmods) |
| **Seedance Prompt Skill** | Skill | `git clone` | 視頻提示詞 | [GitHub](https://github.com/songguoxs/seedance-prompt-skill) |
| **Zopia AI** | 影片 Agent | Web | 端到端影片 | [@Zopia_AI](https://x.com/Zopia_AI) |
| **Google Workspace CLI** | CLI | GitHub | Google 統管 | googleworkspace |
| **OpenClaw 安全指南** | 安全 | `git clone` | Agent 安全 | [GitHub](https://github.com/slowmist/openclaw-security-practice-guide) |
| **Apple Mockup PSD** | 素材 | 下載 | 官方 Mockup | [@gibkun1](https://x.com/gibkun1) |

---

## 🎯 本週趨勢

1. **Qwen3.5 全面攻佔本地推理**：0.8B→35B 覆蓋所有場景，瀏覽器到手機都能跑。創始人離職影響待觀察
2. **AI 影片進入 Agent 時代**：Zopia 端到端導演 + Seedance Skill 工作流，從「一句話」到「一部片」
3. **Apple Silicon 解鎖新篇章**：ANE 逆向讓訓練成為可能，MLX-Swift 讓 iOS 開發者直接整合本地 AI
4. **ComfyUI V3 生態爆發**：完整開發 Skills + LTX-2.3 + Panorama + SeedVR2 Tiler

---

*本週報由 Claude Code 自動生成，基於 X 書籤精選內容*
*原始數據：24 則書籤，1,658 行內容，160 次滾動*
