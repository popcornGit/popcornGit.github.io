---
layout: page
title: 项目
---

## 🚀 项目展示

<div class="project-grid" style="margin-top: 24px;">

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-brain"></i></div>
  <h3>algcore · AI 视觉核心库</h3>
  <p>面向 AX650N 平台的 AI 视觉推理核心库。集成目标检测、人脸识别、KeepAlive 任务调度、Kokoro TTS 语音合成等模块，支持多模型流水线调度与 ARM64 交叉编译。</p>
  <div class="project-tech">
    <span>C++</span>
    <span>AX650N</span>
    <span>ARM64</span>
    <span>交叉编译</span>
  </div>
</div>

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-microchip"></i></div>
  <h3>NPU 模型转换工作流</h3>
  <p>YOLO11s 从 PyTorch → ONNX → RKNN / AXModel 的完整转换与验证流程。包含转换脚本、仿真推理、量化校准、精度对比等环节。</p>
  <div class="project-tech">
    <span>YOLO11s</span>
    <span>RKNN</span>
    <span>Pulsar2</span>
    <span>ONNX</span>
  </div>
</div>

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-robot"></i></div>
  <h3>Kokoro TTS · 语音合成</h3>
  <p>将 Kokoro TTS 引擎适配到 algcore 框架。4 阶段模型流水线（3 个 AX 模型 + 1 个 ONNX 模型），支持中文、英文、日文等多语言语音合成。</p>
  <div class="project-tech">
    <span>TTS</span>
    <span>Kokoro</span>
    <span>AX650N</span>
    <span>多语言</span>
  </div>
</div>

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-face-smile"></i></div>
  <h3>实时人脸识别系统</h3>
  <p>面向嵌入式平台的实时人脸识别方案。包含 face bindBox 状态管理、连续正脸确认、IQA 过滤等机制，经 100 轮 Enable/Disable 压力测试无内存泄漏。</p>
  <div class="project-tech">
    <span>C++</span>
    <span>人脸识别</span>
    <span>AX650N</span>
  </div>
</div>

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-comments"></i></div>
  <h3>LBK-Intelligent-dialogue</h3>
  <p>基于深度学习的智能对话交互系统，融合自然语言理解与多轮对话管理。</p>
  <div class="project-tech">
    <span>Python</span>
    <span>NLP</span>
    <span>Deep Learning</span>
  </div>
</div>

<div class="project-card" style="cursor: default;">
  <div class="project-icon"><i class="fas fa-tags"></i></div>
  <h3>LBK-Bert-Text-Classification</h3>
  <p>基于 BERT 模型的文本分类实践，涵盖情感分析、意图识别等场景。</p>
  <div class="project-tech">
    <span>BERT</span>
    <span>PyTorch</span>
    <span>NLP</span>
  </div>
</div>

</div>
