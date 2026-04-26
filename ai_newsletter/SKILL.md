---
skill: ai_newsletter_daily
version: 1.0.0
name: Daily AI Newsletter (Brave + Firecrawl)
tags:
  - newsletter
  - ai-news
  - brave
  - firecrawl
  - web-search
  - web-scrape
triggers:
  - schedule.daily
author: Jeff Yang (https://github.com/j3ffyang)
license: MIT
description: |
  A daily AI‑focused newsletter pipeline that uses Brave Search to discover AI news,
  then Firecrawl to scrape and summarize selected articles, producing ≈20 curated items.
---

# 🤖 Daily AI Newsletter (Brave + Firecrawl)

An OpenClaw skill that:

- Uses **Brave Search** (`web_search`) to find recent AI‑related news.  
- Filters and ranks results to keep ≈20 strong‑relevance items.  
- Uses **Firecrawl** (`web_scrape`) to scrape each chosen article.  
- Summarizes each article with the model (`language_model`).  
- Produces a structured `newsletter_items` list, plus `markdown_newsletter` and `json_newsletter` outputs for your newsletter engine.

---

## 🧩 Prerequisites

Before this skill runs, you must:

1. **Install OpenClaw** (Docker, pip, or GitHub clone).  
2. **Expose MCP‑compatible tools** or configure them in `config.yaml`.  
3. **Register Brave Search API**:
   - Account at `https://search.brave.com/api`.  
   - Set `BRAVE_API_KEY` in your environment or config.  
   - In `config.yaml`:
     ```yaml
     tools:
       web_search:
         backend: brave
         brave:
           api_key: YOUR_BRAVE_API_KEY
           api_url: https://api.search.brave.com
           mode: fresh
     ```  
4. **Register Firecrawl**:
   - Account at `https://firecrawl.dev`.  
   - Set `FIRECRAWL_API_KEY`.  
   - Configure a `web_scrape` tool:
     ```yaml
     tools:
       web_scrape:
         backend: firecrawl
         firecrawl:
           composio_service: firecrawl
           # or
           # mcp_url: https://mcp.firecrawl.dev/...
           # api_key: YOUR_FIRECRAWL_API_KEY
     ```  
5. **(Optional)** Install and authorize Composio:
   ```bash
   pip install composio_pydantic
   composio login
   composio add firecrawl
   ```  
6. **Define two helper tools** (or ensure they exist in your OpenClaw):
   - `filter_ai_news` – filters and ranks search results.  
   - `extract_urls` – extracts URLs from a list of articles.  

---

## 🧭 Skill Overview

- **Goal**: Produce a daily AI‑focused newsletter with **≈20 articles**.  
- **Method**:
  1. **Search** latest AI news with Brave (`web_search`).  
  2. **Filter & rank** results (`filter_ai_news`).  
  3. **Extract URLs** of selected articles (`extract_urls`).  
  4. **Scrape & enrich** each URL with Firecrawl (`web_scrape`) + summarization (`language_model`).  
  5. **Compile** into `markdown_newsletter` and `json_newsletter` for your engine.

---

## 🪄 Skill Definition

```text
skill: ai_newsletter_daily
description: Daily AI‑focused newsletter via Brave + Firecrawl.
version: 1.0.0

inputs:
  - name: target_news_count
    type: integer
    default: 20
    description: Number of AI articles to include (must be ≥ 1).

  - name: search_query
    type: string
    default: "latest AI news today"
    description: Query to send to Brave Search.

  - name: include_domains
    type: list[string]
    default: []
    description: Optional whitelist of domains (e.g., "arxiv.org").

  - name: exclude_domains
    type: list[string]
    default: ["youtube.com", "reddit.com", "facebook.com", "twitter.com"]
    description: Domains to skip.

outputs:
  - name: newsletter_items
    type: list[object]
    description: |
      Up to `target_news_count` AI‑related articles, each with
      title, URL, domain, summary, published_at, and relevance_score.

  - name: markdown_newsletter
    type: string
    description: Markdown‑formatted newsletter body for your engine.

  - name: json_newsletter
    type: object
    description: |
      JSON‑structured newsletter with date and article list
      for your email / RSS / CMS pipeline.

parameters:
  - name: max_brave_results
    type: integer
    default: 50
    description: Max number of results from Brave before filtering.

  - name: filter_by_ai_keywords
    type: boolean
    default: true
    description: Whether to score relevance by AI‑related keywords.
```

---

## 🧩 Step 1: Search for AI news (Brave)

Use `web_search` to get raw AI‑related links from Brave.

```text
task:
  name: search_ai_news
  description: Fetch raw AI news results from Brave Search.

steps:
  - call:
      tool: web_search
      args:
        query: "{{ search_query }}"
        count: "{{ max_brave_results }}"

  - set:
      var: raw_results
      value: result.tool_output.results
      # or `tool_output.items` depending on your Brave adapter shape
```

---

## 🧩 Step 2: Filter and rank results

Run a helper tool that filters and ranks the raw results.

```text
task:
  name: filter_ai_articles
  description: Filter and rank AI‑related articles from raw results.

depends_on:
  - search_ai_news

steps:
  - call:
      tool: filter_ai_news
      args:
        results: raw_results
        targets: target_news_count
        include_domains: include_domains
        exclude_domains: exclude_domains
        filter_by_ai_keywords: filter_by_ai_keywords

  - set:
      var: kept_articles
      value: result.tool_output.filtered_results
```

> 🔧 `filter_ai_news` implementation (in your code, not in YAML) should:
> - Remove duplicates by hostname+path.  
> - Filter by `include_domains`/`exclude_domains`.  
> - Score relevance to AI (using LLM or keyword‑based scoring).  
> - Sort by relevance + freshness.  
> - Take only `targets` items.

---

## 🧩 Step 3: Extract URLs

Turn filtered `kept_articles` into a plain URL list.

```text
task:
  name: extract_urls
  description: Extract article URLs from filtered results.

depends_on:
  - filter_ai_articles

steps:
  - call:
      tool: extract_urls
      args:
        articles: kept_articles

  - set:
      var: article_urls
      value: result.tool_output.urls
```

> 🔧 `extract_urls` implementation:
> - Should map `[{ title, url, ... }] → urls` (i.e., `urls = [article.url for article in articles]`).

---

## 🧩 Step 4: Deep‑dive scrape with Firecrawl

For each URL, scrape with Firecrawl and summarize with the model.

```text
task:
  name: scrape_and_enrich_articles
  description: Scrape and enrich each selected article.

depends_on:
  - extract_urls

steps:
  - for_each:
      items: article_urls
      item_var: article_url
      steps:
        - call:
            tool: web_scrape
            args:
              url: "{{ article_url }}"

        - set:
            var: raw_content
            value: result.tool_output
            # Assumed to have metadata: { title, published_at, ... }

        - call:
            tool: language_model
            args:
              model: gpt‑4
              prompt: |
                You are a summarizer for an AI‑focused newsletter.
                Given this article content from Firecrawl:
                ---
                {{ raw_content }}
                ---
                Summarize it in exactly one short paragraph.
                Do not include code or technical details unless crucial.
                Return only the paragraph, no headings or extra text.

        - set:
            var: summary
            value: result.tool_output.text

        - call:
            tool: enrich_article
            args:
              article:
                title: raw_content.metadata.title
                url: article_url
                domain: raw_content.metadata.url_domain || extract_domain(article_url)
                summary: summary
                published_at: raw_content.metadata.published_at || "unknown"

        - set:
            var: enriched_item
            value: result.tool_output.article

        - append:
            list: enriched_articles
            value: enriched_item
```

> 🔧 `enrich_article` implementation:
> - Should fill in `domain` from `url` and compute `relevance_score` (e.g., via keyword‑pattern or lightweight LLM scoring).

---

## 🧩 Step 5: Compile the newsletter (20 items)

Sort and truncate to `target_news_count`, then render Markdown and JSON.

```text
task:
  name: compile_newsletter
  description: Turn enriched articles into structured newsletter output.

depends_on:
  - scrape_and_enrich_articles

steps:
  - sort:
      list: enriched_articles
      by: "relevance_score DESC, published_at DESC"

  - set:
      var: final_list
      value: enriched_articles[0:target_news_count]
      # or via `tool: limit_list` if your runtime prefers tool calls

  - set:
      var: markdown_output
      value: |
        # 🤖 Daily AI Newsletter — {{ today }}

        {% for item in final_list %}
        ## {{ item.title }} ({{ item.domain }})

        {{ item.summary }}

        [Read more]({{ item.url }})  
        {% endfor %}

  - set:
      var: json_output
      value: |
        {
          "date": "{{ today }}",
          "articles": final_list
        }

  - output:
      newsletter_items: final_list
      markdown_newsletter: markdown_output
      json_newsletter: json_output
```

---

## 🚀 How to deploy on ClawHub

1. Save this file as:
   ```bash
   skills/ai_newsletter_daily.md
   ```
   in your OpenClaw project or ClawHub skill folder.

2. Make sure your OpenClaw stack exposes these tools:
   - `web_search` → Brave Search.  
   - `web_scrape` → Firecrawl.  
   - `language_model` → your LLM provider.  
   - `filter_ai_news`, `extract_urls`, `enrich_article` → helper tools implemented in your codebase.

3. In ClawHub, trigger the skill:
   - As a **daily cron job** (e.g., `0 9 * * *`).  
   - Or via a webhook that overrides `search_query` and `target_news_count`.

4. Consume outputs:
   - `markdown_newsletter` → send to your Markdown‑based newsletter engine.  
   - `json_newsletter` → pipe into your email engine, RSS generator, or CMS.

---

## ✅ Expected behavior

- When `ai_newsletter_daily` runs:
  - Brave returns ≈50 AI‑related links.  
  - `filter_ai_news` keeps ≈20 high‑relevance items.  
  - `web_scrape` + `language_model` enrich each article.  
  - `compile_newsletter` yields:
    - `newsletter_items` – structured list of 20 AI‑focused items.  
    - `markdown_newsletter` – ready‑to‑render Markdown.  
    - `json_newsletter` – JSON‑endpoint‑ready payload.

You can now land this as a production‑ready ClawHub‑style skill and extend `filter_ai_news`, `enrich_article`, etc., in your OpenClaw code.
