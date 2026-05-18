---
layout: post
title: "Kokoro TTS 引擎在 AX650N 上的适配实践"
date: 2026-05-07
tags: [TTS, Kokoro, AX650N, NPU]
---

## 背景

Kokoro 是一个轻量级的多语言语音合成引擎，支持中文、英文、日文等多种语言。本次任务是将 Kokoro TTS 集成到 algcore AI 视觉核心库，在 AX650N 平台上实现嵌入式语音合成。

## 架构设计

Kokoro TTS 采用 **4 阶段模型流水线**：

```
输入文本 → [模型1 AXModel] → [模型2 AXModel] → [模型3 AXModel] → [模型4 ONNX] → WAV 输出
```

| 阶段 | 模型格式 | 功能 |
|------|----------|------|
| 1 | AXModel | 文本编码 |
| 2 | AXModel | 声学特征预测 |
| 3 | AXModel | 语音合成 |
| 4 | ONNX | 后处理/输出 |

其中 3 个关键模型已转换为 AXModel 格式（借助 Pulsar2 工具链），最后一级后处理保持 ONNX 格式运行。

## algcore 集成

集成主要在以下几部分：

### 1. KokoroPool 管理

设计了一个线程池管理 Kokoro 实例的生命周期：

```cpp
class KokoroPool {
public:
    KokoroPool(const std::string& model_dir, int pool_size);
    Kokoro* GetInstance();
    void ReturnInstance(Kokoro* inst);
private:
    std::vector<Kokoro*> instances_;
    std::mutex mutex_;
};
```

### 2. KeepAlive 机制

通过 KeepAliveTask 实现 TTS 引擎的保活策略：

- 注册 `SERV_TYPE_TTS` 到 `ai_pic_server.cpp` 的 infos map
- 使用 `addCb` / `removeCb` 管理 KokoroPool 生命周期
- **120 秒空闲超时**后自动释放资源
- `KeepAliveGetTTS` 先 `EnsureRun()` 保活，再调用 `GenerateAlarmWav()`

### 3. 音频输出

`GenerateAlarmWav()` 返回 `inf_tts_res_t` 结构体，包含内存中的 WAV 数据，可直接播放或保存。

## 编译与部署

```bash
# 交叉编译
make ax650n_cross -j8
```

模型路径配置在 `ModelDir + "axmodel/kokoro/"` 目录下。

## 验证结果

- ✅ 中文语音合成清晰自然
- ✅ 英文语音合成流畅
- ✅ 日文语音合成支持
- ✅ 内存使用稳定（120s 超时释放正常）
- ✅ 与 KeepAlive 任务调度系统正常配合

## 总结

Kokoro TTS 在 AX650N 上的成功适配，为 algcore 增加了语音交互能力，验证了多语言 TTS 在嵌入式 NPU 平台上的可行性。也证明了 **AXModel + ONNX 混合推理** 是一种灵活高效的部署方案。
