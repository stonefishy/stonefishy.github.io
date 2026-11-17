# Agent guide: stonefishy.github.io

## Scope and working rules

This file is the shared project guide for AI coding agents. It applies to the
entire repository; follow any more specific instructions in the directory being
edited. Claude-specific entry instructions are in [CLAUDE.md](./CLAUDE.md).

For writing, outlining, or substantial style rewrites of blog posts, use the
repository-local [stonefishy-blog-writing skill](./.github/skills/stonefishy-blog-writing/SKILL.md).
It captures style patterns from ten recent posts; this file remains the source
of Hexo and repository rules.

For browser previews, interactions, and screenshots, use the installed
[Playwright CLI skill](./.agents/skills/playwright-cli/SKILL.md).
If `playwright-cli` is not on PATH, invoke it with `npx --yes @playwright/cli`
followed by the command. The CLI requires Node.js 20 or newer; it is separate
from the blog's build dependencies.

- Read the relevant source and check `git status --short` before editing.
- Make the smallest change that satisfies the request. Preserve unrelated work.
- Work on source files, not generated output. Do not reformat old posts, rename
  taxonomy entries, upgrade dependencies, or modify the theme as incidental cleanup.
- Do not deploy, push, or commit unless the user requests it.
- Never put credentials, tokens, or private information in posts, images, or config.

## Project and source map

This is a Hexo 6 static technology blog, not a frontend application or API.
The active theme is the vendored `hexo-theme-bamboo` 3.2.9.
The public site is <https://stonefishy.github.io>.

The main agent workflow is to create and write blog posts using Hexo, then
preview and generate the site. This repository is already an initialized Hexo
site: do not run `hexo init` or scaffold another site inside it. A request to
"write a blog" normally means a new post in this site, not a new application.

| Path | Purpose and editing boundary |
| --- | --- |
| `source/_posts/` | Published Markdown posts; most content tasks belong here. |
| `source/_drafts/` | Optional unpublished drafts created by `hexo new draft`. |
| `source/assets/images/` | Post images, grouped by topic. |
| `source/medias/` | Site-wide media such as avatars and backgrounds. |
| `source/categories/`, `source/tags/` | Category and tag landing pages. |
| `scaffolds/` | Templates used by `hexo new`. |
| `_config.yml` | Site URL, permalinks, active theme, and plugin configuration. |
| `themes/hexo-theme-bamboo/_config.yml` | Theme UI: menus, sidebar, social links, and appearance. |
| `themes/hexo-theme-bamboo/` | Theme templates, scripts, styles, and assets; edit only for explicit theme work. |
| `package.json`, `package-lock.json` | npm scripts and dependency definitions; keep consistent when dependencies change. |
| `.github/workflows/pages.yml` | GitHub Pages build and deployment workflow. |
| `_config.landscape.yml` | Unused configuration for a different theme; leave alone. |
| `public/`, `db.json` | Generated site and Hexo cache; never hand-edit or commit. |

`node_modules/` and `.deploy*/` are also local/generated directories and must not
be committed.

## Setup and commands

Run commands from the repository root using Node.js and npm. The current CI
workflow selects Node.js 18; no Node version is pinned in `package.json`.
Do not change runtime or dependency versions unless the task calls for it.

| Command | When to use it |
| --- | --- |
| `npm ci` | Install the locked dependencies in a fresh checkout or restore missing dependencies. |
| `npm run server` | Preview the site at `http://localhost:4000`; stop the server when finished unless asked to leave it running. |
| `npm run build` | Run `hexo generate`; output goes to `public/`. |
| `npm run clean` | Remove generated output and cache when investigating stale rendering; then rebuild. |
| `npx hexo new post "your-post-title"` | Create a post from the scaffold; complete its metadata before use. |
| `npm run deploy` | Invoke Hexo's deployer, not the normal CI deployment path; requires explicit user authorization and deployment configuration. |

Do not reinstall dependencies for routine edits when the existing installation
works. There are no root `test`, `lint`, or type-check scripts; do not invent
commands or add tooling solely to validate a content change.

## Create and write a blog post with Hexo

### 1. Choose the topic and create the post

Read a recent related post to match terminology, tag spelling, and image layout.
Then use the repository's installed Hexo CLI, rather than creating an empty
Markdown file by hand:

```bash
npx hexo new post "Java Virtual Threads in Practice" --slug java-virtual-threads-in-practice
```

Replace the example title and slug with the requested topic. The title is the
human-readable heading; `--slug` supplies the lowercase, hyphenated filename slug.
Hexo uses `scaffolds/post.md` and the configured
`new_post_name: :year-:month-:day-:title.md` to create
`source/_posts/YYYY-MM-DD-java-virtual-threads-in-practice.md`.
Open the exact path reported by Hexo; `YYYY-MM-DD` above is a placeholder for
the creation date, not literal filename text.

Use `.md`, not `.markdown`. Do not use `--replace` to overwrite an existing
post. Older posts use mixed-case filenames; leave their names unchanged.

### 2. Complete the metadata and write the content

The [post scaffold](./scaffolds/post.md) supplies a title and date but leaves
tags empty and does not add a category. Fill these in before building.
Keep the generated date for a new post unless a different publication date was
requested. The following is an example of a complete post structure:

````markdown
---
title: 'Java Virtual Threads in Practice'
date: 2026-04-03 10:00:00
categories: Backend
tags: [Java]
---

Explain the problem, who this post helps, and what the reader will learn.

<!--more-->

## Prerequisites

List the required Java version and any tools needed to follow the example.

## A Working Example

```java
Thread worker = Thread.startVirtualThread(() -> System.out.println("Hello"));
worker.join();
```

Explain how to run the example, what output to expect, and why it works.

## Summary

Recap the practical takeaways and important limitations.
````

The example date is not a default. Replace instructional placeholder prose
with complete, practical English content; do not leave an outline unless the
user asked for one. Adapt sections to the topic, explain commands and expected
results, and verify technical claims against reliable sources where needed.
Do not add extra front matter fields unless requested.

### 3. Add images when useful

Place images in `source/assets/images/<topic>/` and reference them with the
Hexo image tag documented below. Check that each referenced file exists.
Do not create a per-post asset folder: `post_asset_folder` is disabled.

### 4. Preview and generate the site

```bash
npm run server
```

Open `http://localhost:4000` and navigate to the new post. Check the title,
metadata, excerpt, examples, images, and links. The development server watches
source changes; stop it with Ctrl+C when finished.

Then generate the static site:

```bash
npm run build
```

Check that generation succeeds and the post is included. Generated files belong
in `public/`; fix rendering problems in the Markdown or other source, never in
generated HTML. Creating a post in `source/_posts/` makes it eligible for the
next site build, but does not deploy it to the live site.

### Optional: work on an unpublished draft

Use this route only when a draft is requested or the post is not ready for
normal builds:

```bash
npx hexo new draft "Java Virtual Threads in Practice" --slug java-virtual-threads-in-practice
npm run server -- --draft
```

Hexo creates `source/_drafts/java-virtual-threads-in-practice.md`.
Complete its metadata and content using the same rules as a regular post.
Drafts are excluded from normal builds because `render_drafts` is false;
`--draft` includes them in the local preview. Do not change the global setting
just to preview a draft.

When asked to make the draft ready for publication, stop the preview server and run:

```bash
npx hexo publish post java-virtual-threads-in-practice
```

Use the draft's basename without `.md`, not its display title. Hexo moves it
into `source/_posts/` using the configured post naming pattern. Open the reported
output path, verify the publication date and other metadata, then preview and
build normally. `hexo publish` is a local draft-to-post operation, not a
deployment to GitHub Pages.

### Taxonomy

- `categories` is one scalar string, not an array or a hierarchy.
- For new posts, choose one approved category: `AI`, `Backend`, `Frontend`,
  `Mobile`, `Others`, `Resources`, `Testing`, `Tools`, `中间件`, `云计算`,
  `大数据`, `数据库`, `算法`, or `设计模式`.
- Older posts also contain legacy categories such as `AI/ML` and `Cloud Native`.
  Preserve them when editing those posts; do not migrate them without a request.
- `tags` is an inline YAML array, for example `[Java, Spring]`. Reuse existing
  tag names with exactly the same spelling and case. Search related post metadata
  before introducing a new tag.

### Markdown and assets

- Use `##` for top-level content sections; the theme renders the post title.
- Use fenced code blocks with a language identifier, such as `bash`, `java`,
  `json`, or `yaml`. Keep examples relevant and runnable where practical.
- Store post images under `source/assets/images/<topic>/`.
- Use the theme's Hexo image tag rather than Markdown image syntax:

  ```text
  {% image /assets/images/java/java-21.png, width=600px, alt="Java 21" %}
  ```

- Image URLs start at the site root (`/assets/...`), not `/source/assets/...`.
  Match filename case exactly: CI runs on Linux even if local development does not.
- Preserve existing Hexo tags and math markup when editing content. Math support
  is configured through `hexo-math`; verify rendering when changing formulas.

## Editing an existing post

- Preserve the publication `date`, filename, and title unless explicitly asked
  to change them. Fix only the requested content and directly related issues.
- Permalinks use `:year/:month/:day/:title/`, with pinyin transliteration enabled.
  The `:title` route token normally comes from the filename slug, not the display
  title. Treat filename and date changes as URL changes; do not assume that
  changing the visible title safely renames a URL.
- Preserve existing links, category/tag values, language, and asset paths unless
  the task specifically requires changing them.

## Validation and completion

- For post, asset, configuration, or theme changes, run `npm run build`.
  A successful command is required, but it does not prove that links or layout work.
- For visible changes, preview the affected page and check headings, excerpts,
  images, code blocks, and any math. Verify changed local links and asset paths.
- If rendering appears stale, run `npm run clean` followed by `npm run build`;
  never patch generated HTML to hide a source problem.
- Documentation-only edits to agent instructions do not require a site build.
- Before finishing, run `git diff --check` and `git status --short`. Ensure only
  intended source files changed and no generated files are included.
- Report the changes and checks actually performed. If validation fails, report
  the failure and distinguish pre-existing issues from problems caused by the edit.

## Deployment

The normal deployment path is [.github/workflows/pages.yml](./.github/workflows/pages.yml):
a push to `main` triggers dependency installation, `npm run build`, and publishing
of `public/` through `peaceiris/actions-gh-pages`.

The separate Hexo `deploy.type` in `_config.yml` is intentionally empty.
Do not fill it in or use `npm run deploy` to test a change. Local build validation
does not require deployment credentials or a push.
