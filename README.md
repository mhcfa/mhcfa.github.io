# mhcfa.github.io

Personal blog and open-source research showcase built with [Astro](https://astro.build) and hosted on GitHub Pages at **`https://mhcfa.github.io`**.

## Local Development

```bash
# Start local dev server with hot-reload (http://localhost:4321)
pnpm dev

# Build static site into ./dist
pnpm build

# Preview production build
pnpm preview
```

## Writing a New Post

Create a new `.md` file in `src/content/blog/` with frontmatter:

```markdown
---
title: "Your Post Title"
subtitle: "Optional subtitle shown under the title"
description: "Short summary shown on the home page card"
pubDate: 2026-11-20
readingTime: "5 min read"
tags:
  - "Autonomous Agents"
  - "f8-engine"
---

Write your post in standard Markdown here...
```

## Deploying to GitHub Pages

1. Create a public GitHub repository named **`mhcfa.github.io`** under your `mhcfa` account.
2. In the repo on GitHub, go to **Settings → Pages → Build and deployment** and set **Source** to **GitHub Actions**.
3. Push this repository:

```bash
git remote add origin https://github.com/mhcfa/mhcfa.github.io.git
git push -u origin main
```

Every subsequent `git push` to `main` automatically compiles and deploys the site via `.github/workflows/deploy.yml`.
