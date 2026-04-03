# AGENTS.md — stonefishy.github.io

Instructions for AI agents (Copilot, Cursor, Codex, Gemini, etc.) working in this repository.

## Project overview

Personal tech blog built with **Hexo 6** using the `hexo-theme-bamboo` theme. Deployed to GitHub Pages at `https://stonefishy.github.io`. Topics include software engineering, cloud-native, DevOps, AI tooling, and related areas.

Author: **Andrewsy** (Andrew Shi)

## Common commands

```bash
# Start local dev server (http://localhost:4000)
npm run server

# Generate static files into public/
npm run build

# Clean generated output
npm run clean

# Deploy to GitHub Pages
npm run deploy

# Create a new post
npx hexo new post "Your Post Title"
```

## Repository structure

```
_config.yml          # Main Hexo config (site title, theme, plugins)
package.json         # Dependencies and npm scripts
scaffolds/           # Templates for new posts, pages, drafts
source/
  _posts/            # All published blog posts (Markdown)
  assets/            # Static assets (images organized in subdirectories)
  medias/
  categories/
  tags/
themes/
  hexo-theme-bamboo/ # Active theme — do not modify unless requested
public/              # Generated output — never edit manually
db.json              # Hexo cache — do not commit
```

## Writing and editing posts

### File naming convention

```
YYYY-MM-DD-Title-With-Hyphens.md
```

Example: `2025-12-30-Using-Azure-Policy-Evaluate-the-Resource-Compliance.md`

### Frontmatter format

```yaml
---
title: Human-readable title
date: YYYY-MM-DD HH:mm:ss
tags: [Tag1, Tag2, Tag3]
categories: Category Name
---
```

Rules:
- `tags` — YAML list, always use bracket syntax
- `categories` — single string, not a list
- `date` — set at creation, do not change after publishing
- Do not add extra frontmatter fields unless explicitly requested

### Content conventions

- Language: **English**
- Use `##` for top-level sections; `#` is reserved for the post title (rendered from frontmatter)
- Practical, example-driven writing style with concrete commands and screenshots where helpful
- Code blocks use fenced Markdown with a language identifier:
  ````
  ```bash
  your command here
  ```
  ````
- Images use the Hexo tag helper (not standard Markdown `![]()` syntax):
  ```
  {% image /assets/images/subdir/image.png, alt="Alt text" %}
  ```

### URL stability

The post URL is derived from the filename and the permalink pattern `/:year/:month/:day/:title/`. Renaming a file or changing the `title` in frontmatter changes the live URL — avoid this for already-published posts.

## Plugins

| Plugin | Purpose |
|---|---|
| hexo-generator-search | Generates `search.xml` for site search |
| hexo-generator-feed | RSS feed |
| hexo-generator-archive | Archive pages |
| hexo-generator-category | Category index pages |
| hexo-generator-tag | Tag index pages |
| hexo-permalink-pinyin | Pinyin transliteration in URLs |
| hexo-math | KaTeX / MathJax math rendering |
| hexo-wordcount | Word count display in posts |
| hexo-deployer-git | `hexo deploy` pushes to GitHub Pages |

## What agents should and should not do

**Do:**
- Create new posts following the file naming and frontmatter conventions above
- Edit existing posts in `source/_posts/`
- Add images to `source/assets/images/` organized by topic subdirectory
- Run `npm run build` to verify no generation errors after changes

**Do not:**
- Edit files under `public/` — they are generated and will be overwritten
- Modify files under `themes/` unless the task explicitly involves theme changes
- Change the `date` field of already-published posts
- Commit `db.json` or `public/`
- Use standard Markdown image syntax (`![]()`) — use the `{% image %}` tag instead
