# Copilot instructions: stonefishy.github.io

## Shared project guide

Read [AGENTS.md](../AGENTS.md) at the repository root before making changes.
It is the authoritative shared guide for the source layout, Hexo commands,
content conventions, validation, and deployment boundaries.

Keep repository-wide instructions in `AGENTS.md` rather than duplicating them
here. This file is the Copilot entry point and any future Copilot-specific
guidance; it does not override the shared guide.

## Main task: author posts with Hexo

This repository is an existing Hexo blog. A request to write a blog normally
means creating and writing a post, not running `hexo init` or building a new site.

Follow the complete authoring workflow in [AGENTS.md](../AGENTS.md):

1. Read a related post for terminology, tags, and writing style.
2. Run `npx hexo new post "Post Title" --slug post-title` from the repository root.
3. Open the generated Markdown file reported by Hexo. Complete its title, date,
   single category, and inline tags array; the scaffold does not supply all metadata.
4. Write the full English post with an introduction, `<!--more-->`, practical
   examples, and topic-appropriate sections. Use `##` for top-level sections.
5. Add images under `source/assets/images/<topic>/` using Hexo image tags,
   not Markdown image syntax.
6. Preview with `npm run server`, then validate with `npm run build`.
7. Report the created post and checks performed. Do not deploy automatically.

For unpublished work, follow the shared guide's `hexo new draft`, draft-preview,
and `hexo publish` instructions. Publishing a draft locally does not deploy it.

## Essential guardrails

- Edit source, never generated HTML. Do not hand-edit or commit `public/`,
  `db.json`, or `node_modules/`.
- Preserve existing publication dates, filenames, titles, and taxonomy unless
  the user explicitly requests changes.
- Modify `themes/hexo-theme-bamboo/` only for requested theme work. Theme UI
  configuration belongs in `themes/hexo-theme-bamboo/_config.yml`.
- Use `npm run build` for content/configuration/theme validation. There is no
  root test or lint script. Agent-documentation-only edits do not need a build.
- Do not deploy, push, or commit without an explicit request. The Hexo deploy
  target is intentionally unconfigured; normal deployment uses GitHub Actions.
- Preserve unrelated work and never add credentials or private information.
