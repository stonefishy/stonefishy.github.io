# CLAUDE.md — stonefishy.github.io

## Project overview

Personal tech blog built with **Hexo 6** using the `hexo-theme-bamboo` theme. Deployed to GitHub Pages at `https://stonefishy.github.io`. The blog covers software engineering, cloud-native, DevOps, AI tooling, and related topics.

## Common commands

```bash
# Start local dev server (http://localhost:4000)
npm run server

# Generate static files into public/
npm run build

# Clean generated files
npm run clean

# Deploy to GitHub Pages
npm run deploy
```

## Repository structure

```
_config.yml          # Main Hexo config (site title, theme, plugins)
_config.landscape.yml
package.json
scaffolds/           # Post/page/draft templates
source/
  _posts/            # All published blog posts (Markdown)
  assets/            # Static assets (images, etc.)
  medias/
  categories/
  tags/
themes/
  hexo-theme-bamboo/ # Active theme
public/              # Generated output (do not edit manually)
```

## Writing posts

### File naming

Posts follow the pattern: `YYYY-MM-DD-Title-With-Hyphens.md`

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

- `tags` is a YAML list
- `categories` is a single string (not a list)
- Date is set at creation time and not changed after publishing

### Content conventions

- Write in English
- Use `##` for top-level sections (not `#`, which is reserved for the title)
- Images use the Hexo tag: `{% image /assets/images/subdir/image.png, alt="Alt text" %}`
- Keep posts focused on a single topic with practical, example-driven content
- Code blocks use fenced Markdown with language identifiers

### Creating a new post

```bash
hexo new post "Your Post Title"
```

This uses `scaffolds/post.md` as the template and generates the file with today's date prefix.

## Plugins installed

| Plugin | Purpose |
|---|---|
| hexo-generator-search | Site search (`search.xml`) |
| hexo-generator-feed | RSS feed |
| hexo-permalink-pinyin | Pinyin transliteration in URLs |
| hexo-math | KaTeX / MathJax support |
| hexo-wordcount | Word count display |
| hexo-deployer-git | `hexo deploy` to GitHub Pages |

## Theme

**hexo-theme-bamboo** — located in `themes/hexo-theme-bamboo/`. Do not edit theme files unless specifically working on theme customization. Theme-level config is managed inside the theme directory or via `_config.yml` overrides.

## Notes

- `public/` is generated output — never edit it directly; changes will be overwritten on next build.
- `db.json` is Hexo's internal cache — do not commit it if avoidable.
- The site uses `permalink: :year/:month/:day/:title/` — changing a post's filename or title changes its URL.
