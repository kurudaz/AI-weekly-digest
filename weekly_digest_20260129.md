# X 書籤週報 — 2026/01/29

> 本期涵蓋 2026/01/22 – 2026/01/29 的書籤內容

---

## 本週焦點

### 1. Z-Image Base 模型全面爆發
本週書籤中最密集的話題。Z-Image Base 是新一代 Stable Diffusion 基底模型，社群在短時間內產出了大量衍生資源：GGUF 量化版、FP8 混合版、NVFP4 版、各式 LoRA（Flat Color Style、Anime-to-Realism）以及專用 VAE。ComfyUI 官方也已提供原生支援節點。

### 2. Qwen3-TTS — 本地多角色語音合成
Qwen 發布了 Qwen3-TTS，支援日語、中文等多語言，可在本地運行。社群已有 ComfyUI 自定義節點實現多角色對話合成（Voice Design + Voice Clone），測試顯示生成速度接近即時。

### 3. Nemotron 3 Nano NVFP4 — NVIDIA 量化蒸餾新標竿
NVIDIA 釋出 Nemotron 3 Nano 30B (A3B) 的 NVFP4 量化版，在 B200 上達到 FP8-H100 的 4 倍吞吐量，精度透過 Quantization-Aware Distillation 得以保留。vLLM 已原生支援。

### 4. Kimi K2.5 免費一週 + Video-to-Code
Kilo Code 宣布 Kimi K2.5 免費開放一週，號稱在多項 coding benchmark 上超越 Opus 4.5。同時展示了 one-shot "Video to Code" 功能，可從螢幕錄影直接複製網站含互動設計。

### 5. ComfyUI 生態持續擴展
多個新節點/工具：VNCCS（3D Pose Studio 全流程控制）、AnyDeviceOffload（任意模型卸載至 GPU/CPU）、SeedVR2 GGUF（超解析度魔法）。

---

## 工具清單

### AI 模型 / Checkpoint

| 工具 | 用途 | 安裝/連結 |
|------|------|-----------|
| Z-Image Base (BF16) | SD 新基底模型 | [Comfy-Org/z_image](https://huggingface.co/Comfy-Org/z_image) |
| Z-Image Base GGUF | 量化版 (q4_k_m/q8/bf16) | [babakarto/z-image-base-gguf](https://huggingface.co/babakarto/z-image-base-gguf) |
| Z-Image NVFP4 | NVIDIA FP4 量化版 | [marcorez8/Z-image-aka-Base-nvfp4](https://huggingface.co/marcorez8/Z-image-aka-Base-nvfp4) |
| Z-Image FP8 Mixed | 6.75GB 精簡版 | [Civitai: Z-Image fp8](https://civitai.com) |
| Z-Image Clear VAE | 專用 VAE | [easygoing0114/Z-Image_clear_vae](https://huggingface.co/easygoing0114/Z-Image_clear_vae) |
| UltraFlux VAE | 通用 VAE | [Owen777/UltraFlux-v1](https://huggingface.co/Owen777/UltraFlux-v1) |
| Nemotron 3 Nano NVFP4 | 30B-A3B 量化推理模型 | [nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-NVFP4](https://huggingface.co/nvidia/NVIDIA-Nemotron-3-Nano-30B-A3B-NVFP4) |
| SeedVR2 GGUF | 超解析度 (3B/7B) | [cmeka/SeedVR2-GGUF](https://huggingface.co/cmeka/SeedVR2-GGUF) |
| Flat Color LoRA (Z-Image) | 平塗風格 LoRA | [Civitai: Flat Color Style v2.1](https://civitai.com) |
| Flux2 Klein Anime-to-Real | 動漫轉真人 LoRA | [WarmBloodAban/Flux2_Klein_Anything_to_Real_Characters](https://huggingface.co/WarmBloodAban/Flux2_Klein_Anything_to_Real_Characters) |
| Qwen3-TTS | 多語言語音合成 | [HF Demo](https://huggingface.co/spaces/Qwen/Qwen3-TTS) |

### ComfyUI 節點 / 擴展

| 工具 | 用途 | 安裝 |
|------|------|------|
| ComfyUI-VNCCS | 3D Pose Studio + 多姿態控制 + QWEN Detailer | `git clone https://github.com/AHEKOT/ComfyUI_VNCCS_Utils` |
| ComfyUI-AnyDeviceOffload | 任意模型/VAE/CLIP 卸載至 GPU 或 CPU | `git clone https://github.com/FearL0rd/ComfyUI-AnyDeviceOffload` |
| ComfyUI-Qwen-TTS | Qwen3-TTS ComfyUI 節點（多角色對話） | `git clone https://github.com/flybirdxx/ComfyUI-Qwen-TTS` |

### 開發者工具

| 工具 | 用途 | 安裝 |
|------|------|------|
| Kimi K2.5 (via Kilo Code) | Coding AI 模型（免費一週） | [kilocode](https://kilocode.ai) |
| find-skills | Claude Code 自動尋找合適 skill | `npx skills add vercel-labs/skills --skill find-skills` |
| AI Toolkit (Z-Image LoRA) | LoRA 訓練工具 | [ostrisai/ai-toolkit](https://github.com/ostrisai/ai-toolkit) |

### 創意工具

| 工具 | 用途 | 連結 |
|------|------|------|
| Spatialograph Maker | 3D Gaussian Splatting 展示/編輯 Web App | [spatialograph.app](https://spatialograph.app) |

---

## 快速參考卡

| 名稱 | 類型 | 一句話說明 |
|------|------|-----------|
| Z-Image Base | Checkpoint | 新一代 SD 基底模型，社群全速量化中 |
| Nemotron 3 Nano NVFP4 | LLM | NVIDIA 30B MoE，4x 吞吐量 |
| Qwen3-TTS | TTS | 多語言本地語音合成 |
| SeedVR2 | Upscaler | 超解析度黑魔法（慢但強） |
| VNCCS | ComfyUI | 全 3D Pose Studio 控制 |
| AnyDeviceOffload | ComfyUI | 模型自由卸載至任意裝置 |
| Qwen-TTS Node | ComfyUI | 多角色對話語音生成 |
| Kimi K2.5 | Coding AI | 號稱超越 Opus 4.5 的 coding 模型 |
| find-skills | CLI | Claude Code 自動探索 skill |
| Spatialograph | Web App | 3DGS 作品展示與編輯 |
| Flat Color LoRA | LoRA | Z-Image 平塗風格 |
| Flux2 Klein A2R | LoRA | 動漫轉真人角色 |

---

*Generated: 2026-01-29 | Source: X/Twitter Bookmarks*
