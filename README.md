# 🚀 Model Deployment Collection (模型部署实战指南)

[![GitHub license](https://img.shields.io/github/license/beizhai35/Model-deployment-collection)](https://github.com/beizhai35/Model-deployment-collection/blob/main/LICENSE)
[![Maintenance](https://img.shields.io/badge/Maintained%3F-yes-green.svg)](https://github.com/beizhai35/Model-deployment-collection/graphs/commit-activity)

欢迎来到 **Model Deployment Collection**！这是一个专门用于记录和分享各类前沿 AI 模型部署流程的开源仓库。

本仓库的核心目标是：**将复杂的部署过程标准化、简单化**。无论你是研究人员、开发者还是 AI 爱好者，都能在这里找到清晰、可操作的部署指南。

---

## 📊 模型部署成果 (Deployment Stats)

以下是部分部署模型的使用活跃度统计

| 模型名称                     | 使用人数/活跃度 | 备注                  |
| :--------------------------- | :-------------- | :-------------------- |
| **IndexTTS-2**         | **230+**  | 零样本 TTS 突破性进展 |
| **OpenClaw**           | **60+**   | 智能体交互工具        |
| **ComfyUI-FLUX.2-dev** | **近 50** | 图像生成工作流        |
| **HunyuanVideo-Foley** | **近 50** | 视频音效生成          |
| **Qwen3-TTS**          | **近 20** | 语音合成部署          |

---

## 📂 仓库内容概览

目前仓库包含以下几大类别的部署文档：

### 🎬 视频生成 (Video Generation)

- **[HunyuanVideo 部署文档](Hunyuanvideo部署文档.md)**: 腾讯混元开源的高性能视频基础模型，支持大规模模型训练和推理.
- **[HunyuanVideo-Foley 部署文档](HunyuanVideo-Foley部署文档.md)**: 腾讯混元开源的端到端视频音效生成模型，专为视频创作设计的 AI 工具.

### 🎨 图像生成 (Image Generation)

- **[FLUX-dev2.0 部署文档](FLux-dev2.0部署文档.md)**: 详细记录了 FLUX.2 [dev] 的部署过程，涵盖文本生成图像、单/多参考编辑等功能.
- **[Glyph 部署文档](Glyph部署文档.md)**: 专注于设计类或特定视觉模型的部署记录.

### 👁️ 多模态视觉 (Vision-Language Models)

- **[Qwen3-VL 部署文档](Qwen3-VL-4B&8B部署文档.md)**: 涵盖 Qwen3-VL 4B/8B 版本的部署，包含视觉感知、推理及智能体交互能力的配置.

### 🔊 语音处理 (Speech & Audio)

- **[IndexTTS2 部署文档](IndexTTS2部署文档.md)**: 突破性的自回归零样本 TTS 模型，支持精确的持续时间控制和情感表达.
- **[Qwen-3TTS 部署文档](Qwen-3TTS部署文档.md)**: 语音合成模型的部署实践.
- **[VibeVoice-ASR 部署文档](VibeVoice-ASR部署文档.md)**: 语音识别 (ASR) 系统的环境搭建与运行.

### 🤖 其他智能模型

- **[OpenClaw 部署文档](OpenClaw部署文档.md)**: 相关智能体或工具的部署指南.

---

## 🛠️ 部署环境建议

在尝试本仓库中的部署流程前，建议准备以下基础环境：

- **操作系统**: Ubuntu 22.04 LTS (推荐) 或 Windows WSL2
- **计算卡**: NVIDIA GPU (建议 16G 显存以上，视具体模型而定)
- **环境管理**: Conda / Docker
- **数据库**: MySQL 8.0+

---

## 🚀 如何使用本仓库

1. **克隆仓库**:
   ```bash
   git clone git@github.com:beizhai35/Model-deployment-collection.git
   cd Model-deployment-collection
   ```
2. **查找文档**: 根据目录结构找到你感兴趣的模型文档。
3. **按步操作**: 每个文档都包含了从环境准备到模型启动的完整步骤。

---

## 📈 更新计划

- [ ] 增加更多主流大模型 (LLM) 的部署文档
- [ ] 补充 Docker 镜像一键部署方案
- [ ] 增加 API 接口调用示例

---

## 🤝 贡献与反馈

如果你在部署过程中遇到任何问题，或者有更好的优化建议，欢迎：

- 提交 **Issue** 进行反馈
- 发起 **Pull Request** 贡献你的部署笔记

---

**维护者**: [@beizhai35](https://github.com/beizhai35)
**邮箱**: whjie@163.com，wwhjie@qq.com, whjie1596@gmail.com
