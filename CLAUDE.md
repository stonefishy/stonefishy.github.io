# Claude instructions: stonefishy.github.io

## Shared project guide

@AGENTS.md

[AGENTS.md](./AGENTS.md) is the authoritative shared guide for this repository.
Read it before making changes. It defines the source layout, npm commands,
post metadata, content conventions, validation, and deployment boundaries.
If your client does not support the import above, open that file explicitly.

Keep repository-wide instructions in `AGENTS.md` rather than duplicating them
here. This file is only the Claude entry point and any future Claude-specific
guidance; it does not override the shared guide.

## Main task: author posts with Hexo

This is an existing blog site. A request to write a blog normally means creating
and writing a post, not running `hexo init` or building a new site.

Follow the complete authoring workflow in [AGENTS.md](./AGENTS.md):

1. Run `npx hexo new post "Post Title" --slug post-title`.
2. Open the generated Markdown file reported by Hexo. Complete its title, date,
   single category, and inline tags array.
3. Write the full English post with an introduction, `<!--more-->`, practical
   examples, and topic-appropriate sections. Add images using Hexo image tags.
4. Preview with `npm run server`, then validate with `npm run build`.
5. Report the created post and validation results; do not deploy automatically.

For unpublished work, use the shared guide's `hexo new draft`, draft-preview,
and `hexo publish` instructions. Publishing a draft locally does not deploy it.

## Quick guardrails

- This is a Hexo static blog. Edit posts in `source/_posts/`, not generated HTML.
- Never hand-edit or commit `public/`, `db.json`, or `node_modules/`.
- Preserve existing publication dates, filenames, and titles unless the user
  explicitly requests a change.
- Modify `themes/hexo-theme-bamboo/` only for requested theme work.
- Use `npm run build` for content/configuration/theme validation. There is no
  root test or lint script. Agent-documentation-only edits do not need a build.
- Do not deploy, push, or commit without an explicit request.
