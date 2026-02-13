# OpenCode 週報 - 2026.02.05

> **本期焦點**: AI Agent 生態爆發、開源音樂生成、前端性能優化
> **資料來源**: X 書籤精選 (過去 7 天)
> **生成時間**: 2026-02-05

---

## 🔥 本週焦點

### 1. MiniCPM-o4.5：首個開源全模態、全雙工模型
**全模態支援**：視頻、音頻、圖片、文本輸入 + 音頻、文本輸出
**亮點**：實時音頻生成、語音克隆、無限長度音頻
來源：[@Gorden_Sun](https://x.com/Gorden_Sun)

### 2. Craft Agent - 文檔團隊的 AI 革命
Craft 團隊推出的 Agent 系統，界面精美、交互細節一流
**核心特色**：巧妙的狀態流轉系統，支援多 Agent 並行，可視化任務進展
完爆 Codex App，值得關注
來源：[@op7418](https://x.com/op7418)

### 3. OpenClaw 生態：Agent 將取代傳統 App
Peter Steinberger 預測：未來不需要臃腫的 App
**核心理念**：說出需求，Agent 現場為你造專屬工具
**社群案例**：
- 自動開發睡眠監測 APP（[@xbanboo](https://x.com/xbanboo)）
- Vibe 虛擬機工具 - 為 Agent 沙盒化設計

### 4. ACE-Step 1.5 - 本地 AI 音樂生成
**ComfyUI 整合完成**：RTX 4090 上 3 分鐘曲目約 3.5 分鐘生成
**社群工具鏈**：
- ComfyUI-LyricForge：支援作詞、BPM/Keyscale 推測
- Ace Step UI：專業級 Suno 開源替代方案

### 5. Claude Code 性能優化 3x
Aiden Bai 分享如何讓 Claude Code 快 3 倍
**核心問題**：前端 UI → Prompt → Code → UI 的意圖損失
文章：[How I made Claude Code 3x faster](https://x.com/aidenybai)

---

## 🛠️ 工具清單

### AI 模型 & 推理

#### GLM-4.7-Flash-Claude-Opus-4.5-High-Reasoning-Distill
```bash
# GGUF 模型，結合 GLM 架構與 Claude 級推理能力
# 蒸餾版本，適合本地運行
huggingface.co/models (搜尋關鍵字)
```
**用途**：本地高效推理，輕量級封裝的 Claude Opus 級推理能力

---

### Agent 開發 & 協作

#### Mission Control
```bash
git clone https://github.com/crshdn/mission-control
```
**用途**：AI 智能體群組的"指揮塔"
**特色**：基於 OpenClaw 網關，實現多智能體任務派發與協同

#### Vibe - MacOS Linux 虛擬機
```bash
# Rust 編寫，二進制 < 1MB，終端敲個 vibe，10 秒起虛擬機
brew install vibe  # 或從 GitHub 下載
# GitHub: https://github.com/lynaghk/vibe
```
**用途**：MacOS 上輕量 Linux 沙盒，為 LLM Agent 隔離環境

---

### 音樂生成 & ComfyUI

#### ACE-Step 1.5 (AIO 版本)
```bash
# ComfyUI 節點，需下載 AIO 版模型檔案
# 注意：使用 AIO 版 checkpoint，避免分割版踩坑
```
**用途**：本地 AI 音樂生成，RTX 4090 上 3 分鐘歌 3.5 分鐘生成

#### ComfyUI-LyricForge
```bash
git clone https://github.com/knishika62/ComfyUI-LyricForge
cd ComfyUI/custom_nodes/
mv ~/ComfyUI-LyricForge ./
```
**用途**：ACE-Step 1.5 配套工具
**功能**：Style Caption、作詞、BPM/Keyscale 推測

#### Ace Step UI
```bash
git clone https://github.com/fspecii/ace-step-ui
npm install
npm run dev
```
**用途**：Suno 的專業開源替代方案
**特色**：完整的 Web UI，支援本地音樂生成工作流

---

### 前端開發

#### 多平台專業 UI/UX 設計 Skill
```bash
# 26.4K ⭐，支援 Claude Code、Cursor、Windsurf 等
# 67 種 UI 風格：玻璃化、黏土化、極簡、粗獷、Bento 網格等
# 查找: GitHub trending (關鍵字: UI/UX design skill)
```
**用途**：為 Claude Code、Cursor 等工具提供專業 UI 設計能力

#### IES Sculptor (Chrome 擴展)
```bash
# 程序化生成 IES 光源配置檔
# Chrome Web Store: 搜尋 "IES Sculptor"
```
**用途**：為 3D 藝術家提供精確的燈光控制
**網站**：[80.lv/articles/procedural-ies-light-profile-generator-for-artists](https://80.lv)

---

### 其他實用工具

#### Gemini in Chrome (macOS 快速啟用)
```bash
# VPN 走美國節點
# Command + Q 徹底關閉 Chrome
open -n -a "Google Chrome" --args --variations-override-country=us
```
**用途**：一行命令在 macOS 開啟 Chrome 內建 Gemini

#### Refly.ai - 工作流模板平台
```bash
# 13/100 個模板已上線，支援 Remix
# 專業漫畫家 Pro 模板：refly.ai/app/wfa-uo2i1kay3nxkdzqi5it1xhkw
```
**用途**：V1.1 版本無需邀請碼，提供各類專業工作流模板

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | GitHub/來源 |
|---------|------|----------|----------|------------|
| **GLM-4.7-Flash-Distill** | AI 模型 | Hugging Face | 本地高效推理 | HuggingModels |
| **Mission Control** | Agent 協作 | `git clone` | 多智能體調度 | [crshdn/mission-control](https://github.com/crshdn/mission-control) |
| **Vibe** | 虛擬機 | `brew install` | Agent 沙盒環境 | [lynaghk/vibe](https://github.com/lynaghk/vibe) |
| **ACE-Step 1.5** | 音樂生成 | ComfyUI 節點 | 本地 AI 作曲 | ComfyUI 生態 |
| **ComfyUI-LyricForge** | ComfyUI 節點 | `git clone` | 作詞 + BPM 推測 | [knishika62/ComfyUI-LyricForge](https://github.com/knishika62/ComfyUI-LyricForge) |
| **Ace Step UI** | Web UI | `npm install` | Suno 開源替代 | [fspecii/ace-step-ui](https://github.com/fspecii/ace-step-ui) |
| **UI/UX Design Skill** | Claude Skill | Skill 市場 | 67 種 UI 風格 | 26.4K ⭐ |
| **IES Sculptor** | Chrome 擴展 | Web Store | 程序化燈光 | [80.lv](https://80.lv/articles/procedural-ies-light-profile-generator-for-artists) |
| **Refly.ai** | 工作流平台 | Web 服務 | 模板化工作流 | [refly.ai](https://refly.ai) |

---

## 🎯 本週趨勢

1. **AI Agent 生態爆發**：OpenClaw、Mission Control 等工具鏈成熟，Agent 開始取代傳統 App
2. **開源音樂生成崛起**：ACE-Step 生態完善，ComfyUI 整合讓本地生成門檻大降
3. **Claude Code 優化浪潮**：社群開始深挖性能瓶頸，前端開發者關注意圖傳遞損失問題
4. **本地化優先**：Vibe、GLM-4.7-Distill 等工具強調隱私與離線能力

---

**下週預告**：關注 MiniCPM-o4.5 社群實作案例、Craft Agent 正式版發布

---

*本週報由 Claude Code 自動生成，基於 X 書籤精選內容*
*原始數據：14 則書籤，1,238 行內容，112 次滾動*
