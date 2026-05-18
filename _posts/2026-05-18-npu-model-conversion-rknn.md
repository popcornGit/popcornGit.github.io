---
layout: post
title: "NPU 模型转换笔记（一）：从 PyTorch 到 RKNN — YOLO11s 实战"
date: 2026-05-18
tags: [NPU, RKNN, YOLO, 模型转换]
---

## 前言

最近在系统学习各品牌 NPU 的模型转换流程，作为系列的第一篇，先走通瑞芯微 Rockchip 的 RKNN 路线。目标是把 YOLO11s 从 PyTorch 训练权重一路转换到 RKNN 格式，并在 RK3588 等平台上部署推理。

## 环境准备

| 组件 | 版本 |
|------|------|
| Python | 3.10 |
| RKNN-Toolkit2 | 2.3.2 |
| PyTorch | 2.x |
| 模型 | YOLO11s |

## 第一步：PT → ONNX 导出

```python
import torch
from ultralytics import YOLO

model = YOLO("yolo11s.pt")
model.export(format="onnx", opset=12, simplify=True)
```

导出时注意：
- 使用 **opset 12**，避免某些 NPU 工具链对更高 opset 的不兼容
- `simplify=True` 可以清理 ONNX 图中的冗余节点

## 第二步：ONNX → RKNN 转换

```python
from rknn.api import RKNN

rknn = RKNN(verbose=True)

# 配置量化参数
rknn.config(
    mean_values=[[0, 0, 0]],
    std_values=[[255, 255, 255]],
    target_platform="rk3588",
)

# 加载 ONNX 模型
rknn.load_onnx(model="yolo11s.onnx")

# 构建 RKNN 模型（FP16 推理，无需量化校准数据）
rknn.build(do_quantization=False)

# 导出
rknn.export_rknn("yolo11s_fp16.rknn")
```

**关键点**：
- `do_quantization=False` 保持 FP16 精度，适合对精度要求较高的场景
- 如需 INT8 量化，需准备校准数据集

## 第三步：RKNN 推理验证

```python
# 初始化
rknn.init_runtime()

# 加载输入图片
img = cv2.imread("test.jpg")
img = cv2.resize(img, (640, 640))

# 推理
outputs = rknn.inference(inputs=[img])

# 解析结果
# ... (后处理部分)
```

## 结果

转换后模型约 **21MB**（FP16），在 RK3588 NPU 上推理速度较纯 CPU 提升显著。

## 小结

RKNN 工具链较为成熟，文档齐全，是入门 NPU 转换的不错选择。下一步计划尝试爱芯元智 AXera 的 Pulsar2 工具链，敬请期待！

---

_系列预告：下一篇会记录 YOLO11s 在 AXera Pulsar2 上的转换实战。_
