---
title: Mistral-7B 部署
description: Mistral-7B 部署
toc: true
authors:
  - Haoran Zhou
tags:
categories:
series:
date: '2023-10-18T13:11:22+08:00'
lastmod: '2023-10-18T13:11:22+08:00'
featuredImage:
draft: false
---

## 前言

意外发现 Mistral AI 开源的 Mistral 7B，声称在所有基准测试中的表现均优于 Llama2-13B，迫不及待的在公司服务器上进行了部署。测试后，发现效果真的很好：
1. 使用相同的 Prompt 测试代码生成能力，Mistral-7B 效果略次于 CodeLlama-13B-8bit 模型。Mistral-7B 及其 4、8bit 量化版本差异不大。
2. Mistral-7B 逻辑推理能力很差。如：a=1, b=2, n=a+b, n=?，Mistral-7B 无法回答。
3. 推理速度很快，目测在1s内可以完成一次推理。

我想在自己的个人笔记本电脑上也进行部署，我的需求如下：
1. 部署在 Windows 11 系统上
2. 使用显存大小为 8G 的 Geforce 4060

综上，只能选择 Mistral-7B 的 4bit 量化版本。但由于 bitsandbytes 不支持 windows，因此无法使用 bitsandbytes 进行量化。幸运的是，有人已经将 Mistral-7B 量化为 4bit，我只需要下载即可。


## 模型下载

部署流程参考 [CSDN博客](https://blog.csdn.net/u013628121/article/details/133856525)

首先，在 [TheBloke/Mistral-7B-OpenOrca-GGUF](https://huggingface.co/TheBloke/Mistral-7B-OpenOrca-GGUF/blob/main/mistral-7b-openorca.Q4_K_M.gguf) 中下载推荐使用的 4bit 模型文件 mistral-7b-openorca.Q4_K_M.gguf, 该模型运行所需要的最大内存为 6.87 GB。

```bash
pip install ctransformers[cuda]
```

