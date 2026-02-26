# OpenCode 週報 - 2026.02.26

> **本期焦點**: 瀏覽器端 LLM 突破、本地模型效能再創新高、AI 圖像工具鏈成熟
> **資料來源**: X 書籤精選 (過去 7 天)
> **生成時間**: 2026-02-26

---

## 🔥 本週焦點

### 1. 瀏覽器直接跑 LLM：LFM2.5-1.2B 達 200+ tok/s
Liquid AI 的 LFM2.5-1.2B-Thinking 模型透過 WebGPU 在瀏覽器中運行，超過 200 tokens/s。
**零安裝、完全私密、極速推理**。由 Transformers.js + ONNX Runtime Web 驅動。
來源：[@xenovacom](https://x.com/xenovacom)

### 2. dots.ocr：1.7B 打敗 GPT-4o 的 OCR 模型
僅 1.7B 參數，在 OmniDocBench 上達到 SOTA，擊敗 GPT-4o 和 Qwen2-VL-72B。
**文件理解不再需要巨型模型**。
來源：[@techNmak](https://x.com/techNmak)

### 3. MLX-Audio-Swift SDK：Apple Silicon 語音 Agent 開發
模組化 Swift SDK，支援 iOS/macOS/visionOS 原生開發：
- **即時本地語音轉錄**：Qwen3-ASR-0.6B 4bit 量化在 M3 Max 上運行
- 可自建 WisprFlow / Monologue 替代方案，完全本地運行
來源：[@Prince_Canuma](https://x.com/Prince_Canuma)

### 4. Qwen3.5 系列本地部署
- **Qwen3.5-27B 稠密模型**：RTX 5090 上 Q6 量化跑 240K 上下文，TPS 50，寫 code 表現優異（[@aliez_ren](https://x.com/aliez_ren)）
- **Qwen3.5-35B-A3B**：35B 參數僅 3B 激活，約 21GB 即可本地運行，效能超越 6 倍大的前代模型（LM Studio 已上架）

### 5. NDLOCR-Lite：日本國立國會圖書館輕量 OCR
筆電等級即可運行的輕量 OCR，支援英文活字和手寫辨識。
**提供 Windows + Mac 應用程式**，僅需滑鼠操作。
來源：[@NDLJP](https://x.com/NDLJP)

---

## 🛠️ 工具清單

### AI 模型 & 推理

#### LFM2.5-1.2B-Thinking（瀏覽器 WebGPU）
```bash
# 零安裝，直接在瀏覽器運行
# 200+ tok/s，Transformers.js + ONNX Runtime Web
# 搜尋：Xenova/LFM2.5-1.2B-Thinking WebGPU demo
```
**用途**：瀏覽器端私密 LLM 推理

#### LFM2-24B-A2B（Liquid AI 最大模型）
```bash
# 24B MoE，2B active，CPU 也能跑
# LM Studio 直接下載
lms get liquidai/lfm2-24b-a2b
```
**用途**：本地高效推理，MoE 架構

#### Qwen3.5-35B-A3B
```bash
# ~21GB 記憶體，35B 參數 3B 激活
# LM Studio:
lms get qwen/qwen3.5-35b-a3b
```
**用途**：本地程式碼生成，262K 上下文

---

### OCR & 文件處理

#### dots.ocr
```bash
# 1.7B 參數，OmniDocBench SOTA
# 擊敗 GPT-4o 和 Qwen2-VL-72B
```
**用途**：輕量級文件理解與 OCR

#### NDLOCR-Lite
```bash
# 日本國會圖書館出品，Windows + Mac 應用
# 支援英文活字 + 手寫辨識
# 下載：lab.ndl.go.jp/data_set/ndlocrlite-usage/
```
**用途**：輕量離線 OCR，無需 GPU

---

### 音樂生成

#### easy_music（ACE-Step 1.5 簡易音樂 App）
```bash
git clone https://github.com/animede/easy_music
# 30 種音樂風格選擇
# 自動下載 Qwen3-1.7B 作詞（無需外部 LLM）
# 多語言，最大 300 秒
```
**用途**：一鍵 AI 音樂生成，自帶作詞功能

---

### 圖像 & 視覺工具

#### img2bez（Rust 向量化演算法）
```bash
# Rust 實作的光柵圖向量化工具
# 來源：@eliheuer
# 類似 VTracer 但新的演算法設計
```
**用途**：點陣圖轉貝茲曲線向量圖

#### copainter 圖層分離
```
# 新功能：1 張圖 → 線稿/光影/下塗り 圖層分離
# PSD 格式輸出，可直接在繪圖軟體調整
# 比本地跑 QwenImageLayered 輕量許多
# copainter.info
```
**用途**：AI 生成圖的專業圖層分離

#### Chromabba 插件（WebGPU 重製版）
```
# 支援 AE / Premiere / Resolve / Nuke
# Win + Mac，可模擬 Liquid Glass 效果
# Discord: discord.gg/9bEGeMU3mV
```
**用途**：影片色彩特效插件

---

### 語音 & 開發

#### MLX-Audio-Swift SDK
```bash
# Apple Silicon 原生語音 SDK
# iOS / macOS / visionOS
# 即時本地轉錄，Qwen3-ASR-0.6B 4bit
# 搜尋 GitHub: MLX-Audio-Swift
```
**用途**：Apple 平台語音 Agent 開發

---

### Prompt Engineering

#### Grok 4.2 (Beta) System Prompt 洩露
```
# 超詳細版 system prompt 提取
# 使用「少數語言」繞過技巧
# Kimi 系統提示詞同樣可用法語指令套出
```
**參考價值**：了解主流 AI 系統的 system prompt 設計

---

## 📊 快速參考卡

| 工具名稱 | 類型 | 安裝方式 | 主要用途 | 來源 |
|---------|------|----------|----------|------|
| **LFM2.5-1.2B** | AI 模型 | 瀏覽器 WebGPU | 零安裝 LLM | [@xenovacom](https://x.com/xenovacom) |
| **LFM2-24B-A2B** | AI 模型 | LM Studio | 本地 MoE | [Liquid AI](https://x.com/liquidai) |
| **Qwen3.5-35B-A3B** | AI 模型 | LM Studio | 程式碼生成 | LM Studio |
| **dots.ocr** | OCR | pip | 1.7B SOTA OCR | [@techNmak](https://x.com/techNmak) |
| **NDLOCR-Lite** | OCR | App 下載 | 輕量離線 OCR | [NDLJP](https://lab.ndl.go.jp) |
| **easy_music** | 音樂生成 | `git clone` | ACE-Step 簡易 App | [GitHub](https://github.com/animede/easy_music) |
| **copainter** | 圖像處理 | Web 服務 | 圖層分離 PSD | [copainter.info](https://copainter.info) |
| **MLX-Audio-Swift** | 語音 SDK | Swift Package | Apple 語音 Agent | [@Prince_Canuma](https://x.com/Prince_Canuma) |
| **img2bez** | 向量化 | Rust crate | 光柵→向量 | [@eliheuer](https://x.com/eliheuer) |

---

## 🎯 本週趨勢

1. **瀏覽器 = 新推理平台**：WebGPU 讓 LLM 在瀏覽器跑到 200+ tok/s，零安裝時代來臨
2. **小模型大勝利**：dots.ocr 1.7B 打敗 GPT-4o，Qwen3.5-35B 僅 3B 激活即超越前代
3. **Apple Silicon 生態爆發**：MLX-Audio-Swift 讓 iOS/macOS 開發者直接建語音 Agent
4. **專業工具鏈成熟**：copainter 圖層分離、NDLOCR-Lite 離線 OCR，AI 工具走向實用化

---

*本週報由 Claude Code 自動生成，基於 X 書籤精選內容*
*原始數據：12 則書籤，488 行內容，36 次滾動*
