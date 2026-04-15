# openclaw-custom-skills

> A curated collection of custom skills for **OpenClaw** and **Claude Code** — covering content creation, multilingual blog publishing, AI-powered media generation, and productivity automation.

[![ClawHub Registry](https://img.shields.io/badge/registry-ClawHub-blue)](https://clawhub.ai)
[![License](https://img.shields.io/badge/license-MIT-green)](LICENSE)
[![OpenClaw Skills](https://img.shields.io/badge/openclaw%20skills-12-orange)](#openclaw-skills)
[![Claude Commands](https://img.shields.io/badge/claude%20commands-1-purple)](#claude-code-commands)

---

## Table of Contents

- [Overview](#overview)
- [Prerequisites](#prerequisites)
- [OpenClaw Skills](#openclaw-skills)
  - [Writing Framework](#writing-framework)
  - [Blog Polishing](#blog-polishing)
  - [Image Generation](#image-generation)
  - [Video Generation](#video-generation)
  - [Tibetan Buddhist Content](#tibetan-buddhist-content)
  - [Data & Productivity](#data--productivity)
- [Claude Code Commands](#claude-code-commands)
- [Installation](#installation)
- [Usage](#usage)
- [Contributing](#contributing)
- [License](#license)

---

## Overview

This repository hosts two types of automation assets:

**OpenClaw skills** — production-ready agent instruction sets (`SKILL.md`) published to the [ClawHub](https://clawhub.ai) registry. Each skill drives an OpenClaw agent through a specific content workflow.

**Claude Code commands** — slash commands (`.claude/commands/`) that extend [Claude Code](https://claude.ai/code) with project-specific automation. Drop the `.claude/` folder into any project and the commands become available instantly via `/command-name`.

**Key capabilities:**

- Polish and translate technical blog drafts to English (en-US) or Simplified Chinese (zh-CN)
- Generate hero and per-section images with consistent style across posts
- Convert polished content into Astro-compatible markdown with YAML frontmatter
- Produce cinematic MP4 videos from static images via Google Veo
- Create culturally verified Tibetan Buddhist product articles with matched imagery
- Export X/Twitter bookmarks to rich Markdown files for Obsidian, Notion, or any note-taking system

---

## Prerequisites

### For OpenClaw skills

- [OpenClaw CLI](https://openclaw.ai) installed and authenticated
- ClawHub account (for installing registry skills)
- API access as required per skill:
  - Image generation: OpenAI DALL-E-3 or compatible
  - Video generation: Google Veo (via Vertex AI / Gemini API)
  - Vision analysis: Google Gemini 2.5 Flash

### For Claude Code commands

- [Claude Code](https://claude.ai/code) installed and authenticated
- Python 3.8+ (for commands that run scripts)

---

## OpenClaw Skills

### Writing Framework

| Skill | Version | Description |
|---|---|---|
| [`indepth-perspective`](./indepth-perspective/SKILL.md) | 1.0.1 | Reusable framework for building persuasive, emotionally layered articles with structured viewpoints, storytelling arcs, and reader-engagement hooks |

### Blog Polishing

| Skill | Version | Description |
|---|---|---|
| [`blog-polish-zhcn`](./blog-polish-zhcn/SKILL.md) | 1.0.13 | Polish a technical draft and translate to Simplified Chinese (1200–1400 words, 4–5 sections); preserves code blocks and technical terms |
| [`blog-polish-en-astro-cn`](./blog-polish-en-astro-cn/SKILL.md) | 1.0.12 | Polish draft into both English and Simplified Chinese, then convert the English version to Astro markdown with YAML frontmatter and an `images/` folder scaffold |
| [`blog-polish-eng-single-image`](./blog-polish-eng-single-image/SKILL.md) | 1.0.5 | Polish a technical blog in natural en-US (1000–1200 words) and produce one hero image prompt summarising the whole article |
| [`blog-polish-eng-multi-images`](./blog-polish-eng-multi-images/SKILL.md) | 1.0.2 | Polish a technical blog in en-US (1000–1200 words) and generate a hero image plus per-section image prompts with consistent visual style |
| [`blog-polish-zhcn-images`](./blog-polish-zhcn-images/SKILL.md) | 1.0.5 | Polish and translate to Simplified Chinese (800–1000 words, 3–4 sections) with hero and per-section image prompts |

### Image Generation

| Skill | Version | Description |
|---|---|---|
| [`blog-image-embedder`](./blog-image-embedder/SKILL.md) | 1.0.4 | Analyse a polished zh-CN markdown file, generate hero and per-section image prompts, and embed `[image:x]` placeholders into an illustrated markdown output |
| [`blog-image-enricher`](./blog-image-enricher/SKILL.md) | MIT-0 | Enrich any existing Markdown file by generating a 1500×500 header image and 1200×675 section images; writes a new `*_img.md` without modifying the original |

### Video Generation

| Skill | Version | Description |
|---|---|---|
| [`image-to-video-gen`](./image-to-video-gen/SKILL.md) | 3.0.1 | Generate a cinematic 5-second MP4 from a local image using the Google Veo-3.0 async API; Gemini Vision analyses the image to craft an enhanced motion prompt |

### Tibetan Buddhist Content

| Skill | Version | Description |
|---|---|---|
| [`tibetan-buddhist-product-article-generator`](./tibetan-buddhist-product-article-generator/SKILL.md) | 1.1.0 | Generate a web-researched ~1000-word Chinese article on a Tibetan Buddhist product plus a hero image and per-section PNG images |
| [`tibetan-cinematic-video`](./tibetan-cinematic-video/SKILL.md) | 1.1.0 | Generate an authentic Tibetan cinematic video (9:16, Google Veo) from an input image and exactly 3 Chinese theme words; enforces cultural guardrails (monasteries, prayer wheels, thangka — no Western fantasy) |

### Data & Productivity

| Skill | Version | Description |
|---|---|---|
| [`twitter-bookmarks-exporter`](./twitter-bookmarks-exporter/SKILL.md) | 1.2.0 | Export X/Twitter bookmarks into individual Markdown files; handles multi-page concatenated JSON, full text for regular/note/article tweets, t.co URL expansion, quoted tweets, media links, and engagement stats |

---

## Claude Code Commands

Claude Code commands live in `.claude/commands/` and are invoked as `/command-name` inside any Claude Code session. To use them, clone this repo and copy (or symlink) the `.claude/` folder into your project root.

| Command | Trigger | Description |
|---|---|---|
| [`export-twitter-bookmarks`](./.claude/commands/export-twitter-bookmarks.md) | `/export-twitter-bookmarks` | Check for `bookmarks.json`, run the Python exporter, verify output, and report total bookmarks exported with a sample file list |

---

## Installation

### OpenClaw skills — from ClawHub registry

```bash
# Install a single skill
openclaw skill install <skill-name>

# Example
openclaw skill install blog-polish-zhcn
```

### OpenClaw skills — from this repository (local)

```bash
git clone https://github.com/negtivSpaz/openclaw-custom-skills.git
cd openclaw-custom-skills

# Install a skill from a local path
openclaw skill install ./blog-polish-zhcn
```

### Claude Code commands

```bash
git clone https://github.com/negtivSpaz/openclaw-custom-skills.git

# Copy the .claude folder into your project
cp -r openclaw-custom-skills/.claude /your/project/

# Or use all commands globally
cp -r openclaw-custom-skills/.claude ~/.claude
```

Then inside a Claude Code session, type `/export-twitter-bookmarks` (or whichever command) to invoke it.

---

## Usage

### OpenClaw skills

Each skill is invoked through the OpenClaw CLI. Refer to the individual `SKILL.md` for the full input/output contract.

```bash
# Run a skill interactively
openclaw run blog-polish-zhcn

# Pass inputs inline
openclaw run blog-polish-zhcn --input draftPath=./my-draft.md outputDir=./out
```

**Workspace conventions** used across skills:

| Path | Purpose |
|---|---|
| `~/.openclaw/workspace/tibetanDraft/` | Input drafts for Tibetan Buddhist skills |
| `~/.openclaw/workspace/tibetanProc/` | Processed outputs (articles, images, videos) |

Output filenames follow the pattern `yymmddHHMM_<title>.<ext>` for chronological sorting.

### Claude Code commands

```
/export-twitter-bookmarks
```

Run this inside a Claude Code session from the project root. Claude will guide you through any missing prerequisites.

---

## Contributing

### Adding an OpenClaw skill

1. Fork this repository and create a feature branch: `git checkout -b feat/my-skill`
2. Add your skill directory with a valid `SKILL.md`
3. Test locally with `openclaw run ./my-skill`
4. Open a pull request against `main` with a clear description of what the skill does

Skill `SKILL.md` files should declare at minimum: `name`, `version`, `author`, `description`, `inputs`, `outputs`, and a step-by-step `workflow`.

### Adding a Claude Code command

1. Add your command file under `.claude/commands/<command-name>.md`
2. The filename becomes the slash command trigger (e.g., `my-tool.md` → `/my-tool`)
3. Open a pull request with a description and example invocation

---

## License

This project is licensed under the [MIT License](LICENSE).  
Individual skills may carry their own license declarations inside their `SKILL.md`.
