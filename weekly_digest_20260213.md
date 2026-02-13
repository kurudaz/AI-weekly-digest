# OpenCode 週報 - 2026.02.13

> **本期焦點**: GLM-5 登場、Seedance 2.0 生態爆發、本地 AI 工具鏈持續進化
> **資料來源**: X 書籤精選 (過去 7 天)
> **生成時間**: 2026-02-13

---

## 🔥 本週焦點

### 1. GLM-5 正式發布：從 Vibe Coding 到 Agentic Engineering
Z.ai 發布 GLM-5，參數從 355B 擴展至 744B（40B active），預訓練數據從 23T 增至 28.5T tokens。專為複雜系統工程和長期 Agent 任務設計。
**本地運行**：MLX 已支援，單台 512GB M3 Ultra 可跑 Q4 版本，15.4 tok/s。
來源：[@Zai_org](https://x.com/Zai_org)、[@awnihannun](https://x.com/awnihannun)

### 2. Seedance 2.0 生態大爆發
字節跳動的 Seedance 2.0 成為本週最熱視頻生成模型：
- **龍珠超完整集數**由 AI 生成（[@chetaslua](https://x.com/chetaslua)）
- **動漫歌曲 MV** 超流暢輸出
- **專用 Skill 開源**：自然語言描述 → 多版本視頻提示詞（[@songguoxiansen](https://x.com/songguoxiansen)）
- **GEM 電影分鏡系統**：指定導演風格自動切換表述（[@xiaojietongxue](https://x.com/xiaojietongxue)）

### 3. MiniMax M2.5 × Ollama 免費體驗
Ollama 與 MiniMax 合作，用戶可免費使用 M2.5 模型，支援 OpenCode、Claude Code、Codex、OpenClaw。
```bash
ollama run minimax-m2.5:cloud
```
來源：[@ollama](https://x.com/ollama)

### 4. zimage-ncnn-vulkan：3MB 的 AI 圖像生成
ncnn 實作的 Z-Image，3MB 綠色包，無需 Python/Torch/CUDA：
- 支援 Windows/Linux/macOS
- 16GB 記憶體即可跑 BF16 全精度
- 支援任何 Vulkan 顯卡（包括核顯）
來源：[@nihui](https://x.com/nihui)

### 5. OpenClaw 0-day 漏洞揭露
Marc Köhlbrugge 發現影響所有群聊頻道的漏洞，利用聊天歷史注入攻擊。已修復，提醒用戶注意安全。
來源：[@marckohlbrugge](https://x.com/marckohlbrugge)

---

## 🛠️ 工具清單

### AI 模型

#### GLM-5 (MLX)
```bash
# 744B 參數 (40B active)，Q4 量化
# 需要 512GB M3 Ultra
pip install mlx-lm
mlx_lm.generate --model glm-5-q4
```
**用途**：複雜系統工程、長期 Agent 任務

#### MiniMax M2.5 (Ollama)
```bash
ollama run minimax-m2.5:cloud
# 支援 OpenCode / Claude Code / Codex / OpenClaw
ollama launch opencode --model minimax-m2.5:cloud
```
**用途**：Real-World Productivity，免費限時體驗

---

### 圖像 & 視頻生成

#### zimage-ncnn-vulkan
```bash
# 3MB 綠色包，無需 Python/CUDA
# GitHub: https://github.com/nihui/zimage-ncnn-vulkan
# 支援 Windows/Linux/macOS，16GB RAM 即可
```
**用途**：超輕量 AI 圖像生成，核顯也能跑

#### Seedance 2.0 Skill（視頻提示詞生成）
```bash
# 專為 Seedance 2.0 開發的提示詞 Skill
# 搜尋 GitHub: songguoxiansen/seedance-skill
# 自然語言 → 多版本高質量視頻提示詞
```
**用途**：簡化 Seedance 2.0 視頻創作流程

#### Kling 3.0 Killer Prompts
```
# 12 個實測有效的 Kling 3.0 提示詞模板
# 來源：@1littlecoder 的詳細教程
```
**用途**：Kling 3.0 視頻生成最佳實踐

---

### ComfyUI 節點

#### ComfyUI Action Director
```bash
git clone https://github.com/yedp123/ComfyUI-Yedp-Action-Director
# 互動式 3D 視口，支援 ControlNet
# 功能：載入 FBX/GLB、批次渲染 OpenPose/Depth/Canny/Normal
# 手動近遠景深度控制 + 無限 UI 縮放
```
**用途**：3D 動作導演工具，精確控制 ControlNet 姿勢

---

### 蒸餾 & 研究

#### Arcflow 蒸餾方法
```
# 基於 Qwen-Image 20B 和 Flux.1 12B
# 更高保真度的 2NFE 蒸餾
# 來源：@bdsqlsz（青龍聖者）
```
**用途**：新型蒸餾方法，提升模型效率

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|----------|----------|------|
| **GLM-5** | AI 模型 | MLX | Agentic 工程 | [Z.ai](https://x.com/Zai_org) |
| **MiniMax M2.5** | AI 模型 | `ollama run` | 生產力 (免費) | [Ollama](https://x.com/ollama) |
| **zimage-ncnn-vulkan** | 圖像生成 | 下載即用 | 超輕量 AI 圖像 | [GitHub](https://github.com/nihui/zimage-ncnn-vulkan) |
| **Seedance 2.0 Skill** | Skill | GitHub | 視頻提示詞 | [@songguoxiansen](https://x.com/songguoxiansen) |
| **ComfyUI Action Director** | ComfyUI 節點 | `git clone` | 3D 動作控制 | [GitHub](https://github.com/yedp123/ComfyUI-Yedp-Action-Director) |
| **Arcflow** | 蒸餾方法 | 研究 | 高保真蒸餾 | [@bdsqlsz](https://x.com/bdsqlsz) |

---

## 🎯 本週趨勢

1. **GLM-5 挑戰 Claude/GPT 地位**：744B 參數、Agent 專精設計，MLX 本地推理已就位
2. **Seedance 2.0 = 視頻生成新標竿**：從動漫到電影分鏡，社群工具鏈迅速成形
3. **本地 AI 全面化**：zimage 3MB 包、Ollama 雲端模型、Mac Studio 24/7 Agent，門檻持續降低
4. **安全意識提升**：OpenClaw 漏洞揭露提醒 Agent 工具的安全風險不容忽視

---

*本週報由 Claude Code 自動生成，基於 X 書籤精選內容*
*原始數據：13 則書籤，1,428 行內容，136 次滾動*
