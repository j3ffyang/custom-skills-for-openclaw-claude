# openclaw-custom-skills

[English](README.md) | [简体中文](README.zh-CN.md)

> 精选的生产就绪 **OpenClaw** 技能合集，已发布至 [ClawHub](https://clawhub.ai) 注册表 —— 涵盖内容创作、多语言博客发布及 AI 驱动的媒体生成。

[![ClawHub Registry](https://img.shields.io/badge/registry-ClawHub-blue)](https://clawhub.ai)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![OpenClaw Skills](https://img.shields.io/badge/openclaw%20skills-11-orange)](#技能列表)

> **正在寻找 Claude Code 命令？** 请访问配套仓库：[claude-custom-skills](https://github.com/negtivSpaz/claude-custom-skills)

---

## 目录

- [概述](#概述)
- [前置条件](#前置条件)
- [技能列表](#技能列表)
  - [写作框架](#写作框架)
  - [博客润色](#博客润色)
  - [图片生成](#图片生成)
  - [视频生成](#视频生成)
  - [藏传佛教内容](#藏传佛教内容)
- [安装](#安装)
- [使用方法](#使用方法)
- [贡献指南](#贡献指南)
- [许可证](#许可证)

---

## 概述

本仓库托管已发布至 [ClawHub](https://clawhub.ai) 注册表的生产就绪 OpenClaw 技能。每个技能都是一套自包含的智能体指令集（`SKILL.md`），驱动 OpenClaw 智能体完成特定内容工作流程 —— 从起草、润色技术博客到生成藏传佛教风格的电影级视频。

**核心能力：**

- 将技术博客草稿润色并翻译为英文（en-US）或简体中文（zh-CN）
- 以统一视觉风格生成文章封面图与各章节配图
- 将润色后的内容转换为带有 YAML 前言的 Astro 兼容 Markdown 格式
- 通过 Google Veo 从静态图片生成电影级 MP4 视频
- 创作经文化核查的藏传佛教产品介绍文章及配套图片

---

## 前置条件

- 已安装并完成身份验证的 [OpenClaw CLI](https://openclaw.ai)
- ClawHub 账户（用于安装注册表技能）
- 各技能所需的 API 访问权限：
  - 图片生成：OpenAI DALL-E-3 或兼容接口
  - 视频生成：Google Veo（通过 Vertex AI / Gemini API）
  - 视觉分析：Google Gemini 2.5 Flash

---

## 技能列表

### 写作框架

| 技能 | 版本 | 描述 |
|---|---|---|
| [`indepth-perspective`](./indepth-perspective/SKILL.md) | 1.0.1 | 可复用的深度文章写作框架，用于构建具有结构化观点、叙事弧线和读者参与钩子的有说服力、情感丰富的文章 |

### 博客润色

| 技能 | 版本 | 描述 |
|---|---|---|
| [`blog-polish-zhcn`](./blog-polish-zhcn/SKILL.md) | 1.0.13 | 润色技术草稿并翻译为简体中文（1200–1400 字，4–5 个章节）；保留代码块和技术术语 |
| [`blog-polish-en-astro-cn`](./blog-polish-en-astro-cn/SKILL.md) | 1.0.12 | 将草稿润色为英文和简体中文双版本，并将英文版转换为带 YAML 前言和 `images/` 目录结构的 Astro Markdown 格式 |
| [`blog-polish-eng-single-image`](./blog-polish-eng-single-image/SKILL.md) | 1.0.5 | 以自然英语润色技术博客（1000–1200 字），并生成一个概括全文的封面图提示词 |
| [`blog-polish-eng-multi-images`](./blog-polish-eng-multi-images/SKILL.md) | 1.0.2 | 以英文润色技术博客（1000–1200 字），生成封面图及各章节配图提示词，保持视觉风格统一 |
| [`blog-polish-zhcn-images`](./blog-polish-zhcn-images/SKILL.md) | 1.0.5 | 润色并翻译为简体中文（800–1000 字，3–4 个章节），附封面图和各章节配图提示词 |

### 图片生成

| 技能 | 版本 | 描述 |
|---|---|---|
| [`blog-image-embedder`](./blog-image-embedder/SKILL.md) | 1.0.4 | 分析润色后的简体中文 Markdown 文件，生成封面图和各章节配图提示词，并在输出的 Markdown 中嵌入 `[image:x]` 占位符 |
| [`blog-image-enricher`](./blog-image-enricher/SKILL.md) | MIT-0 | 通过生成 1500×500 页头图和 1200×675 章节配图来丰富现有 Markdown 文件；写入新的 `*_img.md` 文件，不修改原始文件 |

### 视频生成

| 技能 | 版本 | 描述 |
|---|---|---|
| [`image-to-video-gen`](./image-to-video-gen/SKILL.md) | 3.0.1 | 使用 Google Veo-3.0 异步 API 从本地图片生成 5 秒电影级 MP4；由 Gemini Vision 分析图片并构建增强动态提示词 |

### 藏传佛教内容

| 技能 | 版本 | 描述 |
|---|---|---|
| [`tibetan-buddhist-product-article-generator`](./tibetan-buddhist-product-article-generator/SKILL.md) | 1.1.0 | 生成经网络检索核实的约 1000 字藏传佛教产品中文介绍文章，附封面图和各章节 PNG 配图 |
| [`tibetan-cinematic-video`](./tibetan-cinematic-video/SKILL.md) | 1.1.0 | 根据输入图片和恰好 3 个中文主题词，生成地道的藏传风格电影视频（9:16 竖版，Google Veo）；强制执行文化规范（寺院、转经轮、唐卡等——禁止西方奇幻风格） |

---

## 安装

### 从 ClawHub 注册表安装

```bash
# 安装单个技能
openclaw skill install <skill-name>

# 示例
openclaw skill install blog-polish-zhcn
```

### 从本地仓库安装

```bash
git clone https://github.com/negtivSpaz/openclaw-custom-skills.git
cd openclaw-custom-skills

# 从本地路径安装技能
openclaw skill install ./blog-polish-zhcn
```

---

## 使用方法

每个技能均通过 OpenClaw CLI 调用。请参阅各技能的 `SKILL.md` 了解完整的输入/输出规范。

```bash
# 交互式运行技能
openclaw run blog-polish-zhcn

# 内联传入参数
openclaw run blog-polish-zhcn --input draftPath=./my-draft.md outputDir=./out
```

**各技能通用的工作区路径规范：**

| 路径 | 用途 |
|---|---|
| `~/.openclaw/workspace/tibetanDraft/` | 藏传佛教技能的输入草稿 |
| `~/.openclaw/workspace/tibetanProc/` | 处理后的输出文件（文章、图片、视频） |

输出文件名遵循 `yymmddHHMM_<标题>.<扩展名>` 格式，便于按时间排序。

---

## 贡献指南

1. Fork 本仓库并创建功能分支：`git checkout -b feat/my-skill`
2. 在技能目录下添加有效的 `SKILL.md`
3. 使用 `openclaw run ./my-skill` 进行本地测试
4. 向 `main` 分支提交 Pull Request，并清晰描述该技能的功能

`SKILL.md` 文件至少应声明：`name`（名称）、`version`（版本）、`author`（作者）、`description`（描述）、`inputs`（输入）、`outputs`（输出）以及逐步的 `workflow`（工作流程）。

---

## 许可证

本项目遵循 [MIT 许可证](LICENSE)。  
各技能可在其 `SKILL.md` 内声明独立的许可证。
