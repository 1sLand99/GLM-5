# GLM-5

<div align="center">
<img src=resources/logo.svg width="15%"/>
</div>
<p align="center">
    👋 加入我们的<a href="resources/WECHAT.md" target="_blank">微信</a>或<a href="https://discord.gg/QR7SARHRxK" target="_blank">Discord</a>社区。
    <br>
    📖 查看 GLM-5 的<a href="https://z.ai/blog/glm-5" target="_blank">技术博客</a>。
    <br>
    📍 在 <a href="https://docs.z.ai/guides/llm/glm-5">Z.ai API 平台</a>上使用 GLM-5 API 服务。
    <br>
    👉 一键体验 <a href="https://chat.z.ai">GLM-5</a>。
</p>

## 简介

我们发布 GLM-5，目标是应对复杂系统工程与长周期的智能体（agentic）任务。规模化仍然是提升通用人工智能（AGI）“智能效率”的最重要路径之一。相比 GLM-4.5，GLM-5 的参数规模从 355B（激活 32B）扩展到 744B（激活 40B），预训练数据从 23T 提升到 28.5T tokens。GLM-5 还集成了 DeepSeek Sparse Attention（DSA），在保持长上下文能力的同时，大幅降低部署成本。

强化学习（Reinforcement Learning, RL）旨在弥合预训练模型从“胜任”到“卓越”的差距。然而，由于 RL 训练效率低，将其在大规模 LLM 上部署是一项挑战。为此，我们开发了 [slime](https://github.com/THUDM/slime)——一种全新的**异步 RL 基础设施**，显著提升训练吞吐与效率，使更细粒度的后训练迭代成为可能。随着预训练与后训练的共同进步，GLM-5 相比 GLM-4.7 在广泛的学术基准上取得显著提升，并在推理、编程与智能体任务上达到了开源模型的世界领先水平，缩小了与前沿模型的差距。

![bench](resources/bench.png)

GLM-5 专为复杂系统工程与长周期智能体任务打造。在我们的内部评测套件 CC-Bench-V2 上，GLM-5 在前端、后端以及长周期任务上都显著超越 GLM-4.7，并进一步缩小与 Claude Opus 4.5 的差距。

![realworld_bench](resources/realworld_bench.png)

在 [Vending Bench 2](https://andonlabs.com/evals/vending-bench-2)（衡量长期运营能力的基准）上，GLM-5 在开源模型中排名第 1。Vending Bench 2 要求模型在一年的时间跨度内运营一个模拟的自动售货机生意；GLM-5 最终账户余额为 $4,432，已接近 Claude Opus 4.5，展现出较强的长期规划与资源管理能力。

![vending_bench](resources/vending_bench.png)

## 下载模型

| 模型      | 下载链接                                                                                                                        | 模型规模  | 精度 |
| --------- | ------------------------------------------------------------------------------------------------------------------------------- | --------- | ---- |
| GLM-5     | [🤗 Hugging Face](https://huggingface.co/zai-org/GLM-5)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-5)         | 744B-A40B | BF16 |
| GLM-5-FP8 | [🤗 Hugging Face](https://huggingface.co/zai-org/GLM-5-FP8)<br> [🤖 ModelScope](https://modelscope.cn/models/ZhipuAI/GLM-5-FP8) | 744B-A40B | FP8  |

## 本地部署 GLM-5

### 环境准备

vLLM、SGLang 与 xLLM 都支持在本地部署 GLM-5。这里提供一个简单的部署指引。

- vLLM

  使用 Docker：

  ```shell
  docker pull vllm/vllm-openai:nightly
  ```

  或使用 pip：

  ```shell
  pip install -U vllm --pre --index-url https://pypi.org/simple --extra-index-url https://wheels.vllm.ai/nightly
  ```

  然后升级 transformers：

  ```
  pip install git+https://github.com/huggingface/transformers.git
  ```

- SGLang

  使用 Docker：

  ```bash
  docker pull lmsysorg/sglang:glm5-hopper # 适用于 Hopper GPU
  docker pull lmsysorg/sglang:glm5-blackwell # 适用于 Blackwell GPU
  ```

### 部署

- vLLM

  ```shell
  vllm serve zai-org/GLM-5-FP8 \
     --tensor-parallel-size 8 \
     --gpu-memory-utilization 0.85 \
     --speculative-config.method mtp \
     --speculative-config.num_speculative_tokens 1 \
     --tool-call-parser glm47 \
     --reasoning-parser glm45 \
     --enable-auto-tool-choice \
     --served-model-name glm-5-fp8
  ```

  更多细节请参考 [recipes](https://github.com/vllm-project/recipes/blob/main/GLM/GLM5.md)。

- SGLang

  ```shell
  python3 -m sglang.launch_server \
    --model-path zai-org/GLM-5-FP8 \
    --tp-size 8 \
    --tool-call-parser glm47  \
    --reasoning-parser glm45 \
    --speculative-algorithm EAGLE \
    --speculative-num-steps 3 \
    --speculative-eagle-topk 1 \
    --speculative-num-draft-tokens 4 \
    --mem-fraction-static 0.85 \
    --served-model-name glm-5-fp8
  ```

  更多细节请参考 [sglang cookbook](https://cookbook.sglang.io/autoregressive/GLM/GLM-5)。

- xLLM 与其他昇腾 NPU

  请参考这里的部署指南：[example/ascend.md](https://github.com/zai-org/GLM-5/blob/main/example/ascend.md)。

## 引用

我们的技术报告即将发布。
