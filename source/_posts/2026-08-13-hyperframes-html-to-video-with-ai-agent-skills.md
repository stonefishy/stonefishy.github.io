---
title: 'HyperFrames: Turning HTML into Video with AI Agent Skills'
date: 2026-08-13 14:41:18
categories: AI
tags: [AI, Video]
---

Asking an AI coding agent to write a function or fix a bug is routine by now. Asking it to produce a finished explainer video, a product launch clip, or an animated chart has usually meant stepping outside the agent entirely and opening a timeline-based video editor. [HyperFrames](https://hyperframes.app) closes that gap: it is an open-source, agent-native system that turns HTML, CSS and JavaScript into deterministic MP4 output, and it ships as a family of AI agent skills so a coding agent can plan, build, check and render a video the same way it edits code.

{% pbg green, HyperFrames renders browser content frame by frame into video. An agent writes a composition as HTML/CSS/JS, the CLI seeks a headless browser to each paused frame, and every render of the same project produces the same pixels. %}

<!--more-->

This post walks through what HyperFrames is, the purpose of each bundled skill (`hyperframes`, `hyperframes-cli`, `hyperframes-registry`, `media-use`, `reverse-any-video`, `style-sampling`), how to install them, and what a first project looks like end to end. I have not run a render myself for this post, so the command walkthrough below is illustrative — it follows the documented workflow rather than reporting a captured result.

## What Problem HyperFrames Solves

Most programmatic video tools (Remotion, After Effects scripting, raw FFmpeg filter graphs) assume a human is driving the UI or writing imperative render code. HyperFrames instead treats a video project as a normal web project:

- `index.html` is the root composition. It declares the video's size, frame rate and total duration, and mounts one or more scenes from a `compositions/` folder.
- Every element that appears on screen is timed with a `class="clip"`, plus `data-start` and `data-duration` attributes in seconds — no scripted `play()`/`pause()` calls on media, no wall-clock timers, no unseeded randomness. Everything visible has to be a pure function of timeline time.
- Instead of recording a live browser, the CLI pauses the page and seeks it frame by frame with a headless browser, then stitches the captured frames into an MP4 with FFmpeg. Because nothing depends on real time, the same project renders to the same output every time.

That combination — plain web technology plus a strict, checkable timing contract — is what makes HyperFrames a good fit for an AI agent: the agent can lint and "snapshot" (screenshot) the timeline before committing to a full render, catching layout, contrast or motion mistakes the way a type-checker catches a bad function signature.

## The Skill Ecosystem

HyperFrames is distributed as a set of focused agent skills rather than one monolithic tool. Each one is scoped to a different part of the job:

| Skill | Purpose |
| --- | --- |
| `hyperframes` | The entry point. Documents the render contract (timing attributes, single timeline per composition, allowed/forbidden patterns) and the overall work loop: write a scene, check it, snapshot it, preview it, render it only once the person approves. |
| `hyperframes-cli` | The command reference for `npx hyperframes <command>` — project scaffolding, linting, timeline edits, snapshots, preview and render, plus project history and undo. |
| `hyperframes-registry` | A searchable catalog of roughly 450 ready-made blocks and components (transitions, lower thirds, title cards, charts, camera moves) that install as editable source via `catalog` and `add`, so an agent reuses a vetted effect instead of hand-rolling one. |
| `media-use` | Resolves or generates the media a video needs — music, sound effects, images, voiceover, even a talking avatar — from free sources, HeyGen's paid catalog, or local tools, and keeps everything as a local asset file so renders never depend on a live network call. |
| `reverse-any-video` | Rebuilds an existing video file (that the user has the right to use) as an editable HyperFrames project, checked frame by frame against the original. |
| `style-sampling` | Produces a new episode in one of several proven video styles (for example a "collage explainer" or "3D brand explainer") on a new topic, without reusing a creator's name, face or voice. |

In practice, an agent reads `hyperframes` first for the rules, leans on `hyperframes-cli` for the actual commands, and pulls in `hyperframes-registry` or `media-use` only when a scene needs a reusable effect or outside media. `reverse-any-video` and `style-sampling` are specialized entry points for "copy this video" and "make an episode like that series" requests respectively, and both are gated behind a paid HyperFrames Pro plan.

## Installing HyperFrames

HyperFrames has two layers: the CLI/engine (an npm package) and the agent skills that teach a coding assistant how to drive it.

### Prerequisites

- **Node.js 22+** and **FFmpeg** on the machine that will render.
- A headless **Chrome/Chromium** the CLI can launch (the CLI's `doctor` and `browser ensure` commands check and install this for you).

### The CLI

The CLI is the primary way to work with HyperFrames and can be installed globally or run on demand with `npx`:

```bash
npm install -g hyperframes
# or, without installing anything globally
npx hyperframes <command>
```

It is non-interactive by default — every input is a flag, and output is plain text or JSON — specifically so an AI agent can drive it without hitting an interactive prompt. Add `--human-friendly` to any command when you want the interactive spinners and menus instead.

### The agent skills

The skills themselves (`hyperframes`, `hyperframes-cli`, `hyperframes-registry`, `media-use`, `reverse-any-video`, `style-sampling`) are distributed as standalone skill packages that a coding agent loads from a skills directory, such as `~/.agents/skills/` on a per-user install, or a per-project `.github/skills/` equivalent depending on your agent host. Once installed standalone, they stay current with:

```bash
npx hyperframes skills update
```

Some agent hosts instead ship HyperFrames as a **plugin** (a manifest such as `plugin.json` alongside a bundled `skills/` folder). In that case the host's own plugin manager owns installation and updates, and you reload or update skills through the host rather than running `skills update` yourself. Either way, once the skills are loaded, you normally don't invoke HyperFrames commands directly — you ask your agent to build or edit a video, and it drives the CLI according to the skill instructions.

## A First Hands-On Walkthrough

The flow below is what the `hyperframes` and `hyperframes-cli` skills describe for a new project. Read it as an illustration of the documented loop rather than a transcript of an actual run.

### 1. Scaffold a project

```bash
npx hyperframes init my-video --example blank
```

This creates `my-video/` with a root `index.html` (1920×1080 at 30 fps by default), an empty `compositions/` folder, and an `assets/` folder for media and fonts.

### 2. Open the live preview

```bash
cd my-video
npx hyperframes preview
```

This starts HyperFrames Studio with hot reload in the browser — edits to `index.html` or a composition file show up immediately, the same loop as any other front-end dev server.

### 3. Reuse a registry block instead of hand-building an effect

Rather than writing a title card or transition from scratch, search the local catalog and install one as editable source:

```bash
npx hyperframes catalog --query "animated title card for a product name" --json
npx hyperframes add canopy-part-title --json
```

`add` writes the block to `compositions/` and prints the mount snippet (the `data-composition-src`, `data-composition-id`, `data-start` and `data-duration` to place on the root composition). It's then ordinary HTML you can restyle, shorten or recolor.

### 4. Validate before rendering

```bash
npx hyperframes lint
npx hyperframes check --json
```

`lint` catches structural issues as you write; `check` runs the fuller gate — runtime, layout and contrast audits — and fails fast with a `Fix:` line when something is wrong, such as a clip with no visible change over several seconds.

### 5. Look at it without rendering a full video

```bash
npx hyperframes snapshot --at 0,2,5
```

This captures PNG frames at the given timestamps so you (or the agent) can inspect the composition without paying for a full encode first.

### 6. Render

```bash
npx hyperframes render --output output.mp4 --fps 30 --quality standard
```

`--quality` accepts `draft`, `standard` or `high`, trading encode time for bitrate/CRF. The documented, agent-friendly contract means this same command run twice on an unchanged project should produce byte-for-byte comparable frames, which is the property that makes HyperFrames useful for automated or batch rendering rather than only one-off manual edits.

## Caveats Worth Knowing

- **Pro-gated features.** The core CLI, engine and registry are usable on a free tier, but `reverse-any-video`, `style-sampling` templates, and some HeyGen-backed media (premium voices, generated images, avatar video) require a paid HyperFrames/HeyGen plan and an authenticated account.
- **Local rendering needs real dependencies.** Node.js 22+, FFmpeg and a working headless Chrome are not optional; the `doctor` command exists specifically to diagnose a missing piece before you try to render.
- **The contract is strict by design.** No `Date.now()`, no unseeded `Math.random()`, no network fetches inside a composition, no manually calling `play()`/`pause()` on media — all of it exists to keep renders deterministic, which also means you can't treat a composition like a normal interactive web page.
- **This is a young, fast-moving tool.** Command names, flags and the skill set itself are likely to change; check `npx hyperframes --help` and the [official docs](https://hyperframes.app/docs) against anything here before relying on it.

## Summary

HyperFrames reframes video production as a code problem: write HTML/CSS/JS with explicit timing, let a headless browser capture it frame by frame, and get the same output every time. For developers already working with an AI coding agent, the bundled skill set means the agent can scaffold a project, reuse vetted effects from the registry, source or generate media, validate the timeline, and render — the same check-then-act loop it already uses for code. If your team needs to produce explainer videos, data visualizations or product demos on a recurring basis and wants that work to be automatable and reviewable rather than locked inside a timeline editor, HyperFrames is worth evaluating.
