# OpenCode 週報 - 2026.02.20

> **本期焦點**: OpenClaw Mission Control 熱潮、Pocket TTS 輕量語音、ACE-Step API 免費開放
> **資料來源**: X 書籤精選 (過去 7 天)
> **生成時間**: 2026-02-20

---

## 🔥 本週焦點

### 1. OpenClaw + Mission Control = 效率 10 倍
本週最熱話題：用 Mission Control 升級 OpenClaw 成「最強輔助」。
- Matthew Berman 分享 **21 個 OpenClaw 日常用法**（花了 2.54B tokens 測試）
- Mission Control 是由 OpenClaw 自己長出來的專屬控制台，可管理 Memory、CRM、MD Files 系統
- Alex Finn 的完整設定指南登上 129 萬瀏覽

**OpenClaw 核心配置檔**（@Nate_Google_ 整理）：
- IDENTITY：mberman84 的 gist
- SOUL：mberman84 的 gist
- PRD：mberman84 的 gist

### 2. Pocket TTS by Kyutai：100M 超輕量語音合成
CPU 就能跑、速度比實時更快的開源 TTS 模型：
- 100M 參數，無需 GPU
- Mac Studio CPU 上生成速度超過實時播放速度
- 作者實測所有本地 TTS 模型，Pocket TTS 表現最優
來源：[@Legendaryy](https://x.com/Legendaryy)

### 3. ACE-Step 1.5 免費 API 正式開放
ACE Music 宣布兩項更新：
1. **ComfyUI 節點**：呼叫線上 API（免費，永久免費）
2. **Hosted API**：免費永久，適合在本地模型 UI 外部整合
來源：[@acemusicAI](https://x.com/acemusicAI)

### 4. SKILLS.md：成為 10X 工程師的新方式
Matt Dancho 指出：現在的 10X 工程師，其實只需要一個 SKILLS.md 文件。
> 「AI 時代的槓桿點從技術變成了 prompting 架構」
來源：[@mdancho84](https://x.com/mdancho84)

### 5. Open-Higgsfield-AI 開源克隆版
Higgsfield AI 的開源本地版本現身 GitHub，可本地運行 Higgsfield 風格的圖像生成。
來源：[@L_go_mrk](https://x.com/L_go_mrk)

---

## 🛠️ 工具清單

### CLI & Tracking

#### trackers 2.2.0（Roboflow）
```bash
pip install trackers
# 全管線 CLI 追蹤，支援 Webcam 即時測試
trackers track --source 0 \
    --model rfdetr-medium \
    --tracker bytetrack \
    --show-trajectories
# GitHub: https://github.com/roboflow/trackers
```
**用途**：物件追蹤完整管線，從命令列一行搞定

---

### 語音合成 TTS

#### Pocket TTS by Kyutai
```bash
# 100M 參數，無需 GPU，CPU 即可跑
# 速度超過實時播放
pip install kyutai-tts  # 或參考官方安裝方式
```
**用途**：本地輕量 TTS，Mac Studio CPU 即時輸出，目前評測最優

---

### 音樂生成

#### ACE-Step 1.5 ComfyUI 節點（免費 API）
```bash
# 安裝節點
# registry.comfy.org/nodes/ACE-Step-ComfyUI
# 呼叫線上免費 API，無需本地 GPU
```
**用途**：在 ComfyUI 中使用 ACE-Step 免費雲端 API 生成音樂

---

### VTuber & 動畫

#### MotionPNGTuber
```bash
# 為 Loop 影片加入 Lip Sync
git clone https://github.com/rotejin/MotionPNGTuber
# 製作一次 Lip Sync 影片，之後只需播放音頻即自動同步
```
**用途**：VTuber / PNGTuber 的嘴型同步工具，無需即時 Live2D

---

### 圖像生成

#### FrameScale（Qwen-Image-Edit 支援）
```
# frame-scale.com
# 支援 Qwen-Image-Edit-2509/2511 生成
# 免費登入點數可累積
```
**用途**：圖像編輯生成平台，支援最新 Qwen 圖像模型

#### Open-Higgsfield-AI
```bash
# Higgsfield AI 開源本地克隆
# GitHub: Anil-matcha/Open-Higgsfield-AI
git clone https://github.com/Anil-matcha/Open-Higgsfield-AI
```
**用途**：本地運行 Higgsfield 風格視頻/圖像生成

---

### OpenClaw 進階配置

#### Mission Control 設定
```
# 核心概念：讓 OpenClaw 生成專屬控制台
# 參考指南：@AlexFinn 的完整文章（129萬瀏覽）
# 配置檔參考（@Nate_Google_）：
# - IDENTITY: gist.github.com/mberman84/a128371ffb1fc28554f515905a8bddcd
# - SOUL:     gist.github.com/mberman84/cd6924c7058ba5251a773dac177ae756
# - PRD:      gist.github.com/mberman84/5ccf2085d0049581b4675f7fe64e9b87
```
**用途**：打造專屬 OpenClaw 指揮中心，管理記憶、CRM、任務流

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|----------|----------|------|
| **trackers 2.2.0** | CLI | `pip install` | 物件追蹤管線 | [Roboflow](https://github.com/roboflow/trackers) |
| **Pocket TTS** | 語音合成 | pip | 超輕量本地 TTS | [@Legendaryy](https://x.com/Legendaryy) |
| **ACE-Step ComfyUI** | ComfyUI 節點 | registry.comfy.org | 免費雲端音樂生成 | [ACE Music](https://x.com/acemusicAI) |
| **MotionPNGTuber** | VTuber 工具 | `git clone` | Loop 影片 Lip Sync | [GitHub](https://github.com/rotejin/MotionPNGTuber) |
| **FrameScale** | 圖像生成 | Web 服務 | Qwen 圖像編輯 | [frame-scale.com](https://frame-scale.com) |
| **Open-Higgsfield-AI** | 圖像/影片生成 | `git clone` | 本地 Higgsfield | [GitHub](https://github.com/Anil-matcha/Open-Higgsfield-AI) |
| **Mission Control** | OpenClaw 配置 | 手動設定 | OpenClaw 指揮中心 | [@AlexFinn](https://x.com/AlexFinn) |

---

## 🎯 本週趨勢

1. **OpenClaw 生態系成熟**：Mission Control 成為標配，Memory + CRM + 任務管理一體化
2. **輕量化 AI 工具持續崛起**：Pocket TTS 100M 跑贏大模型，本地部署門檻再降低
3. **免費 API 策略普及**：ACE-Step 宣示「永久免費」，開源生態搶用戶
4. **SKILLS.md 文化興起**：AI 時代的工程師槓桿點從技術遷移到 prompting 架構設計

---

*本週報由 Claude Code 自動生成，基於 X 書籤精選內容*
*原始數據：13 則書籤，1,008 行內容，86 次滾動*
