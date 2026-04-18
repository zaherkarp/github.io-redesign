# CLAUDE.md
# github.io-redesign — persistent context for Claude Code

This file is read at the start of every Claude Code session.
Update it when decisions change. Do not let it drift from reality.

---

## Project overview

Personal portfolio site for Zaher Karp (zaherkarp.github.io).
Redesign from Astro/TypeScript/Tailwind to pure HTML/CSS.
No framework. No npm. No build step for the main site.
Blog pipeline has a build step (Python) — see Blog section below.

**Current status:** Mockup complete (v18). Implementation not started.
**Target repo:** github.io-redesign (new GitHub repo)
**Deployment:** GitHub Pages, project site at zaherkarp.github.io/github.io-redesign/
**Swap plan:** When ready, copy files into zaherkarp.github.io main repo and push.
The live site at zaherkarp.github.io stays untouched until the swap.

---

## Stack

- HTML/CSS only. No JavaScript except GoatCounter analytics.
- One Google Fonts import: EB Garamond (prose) + Courier New (system, data specimens).
- No external CSS frameworks.
- No preprocessors.
- No bundlers.

---

## File structure (target)

```
github.io-redesign/
├── index.html              # Main portfolio page (v18 mockup)
├── resume.pdf              # Manually maintained resume PDF — see TO-DOs
├── style.css               # Extracted from index.html if needed (keep inline for now)
├── blog/                   # Generated blog post HTML files
│   └── [slug].html
├── src/
│   └── content/
│       └── blog/
│           └── *.md        # Markdown source for blog posts
├── scripts/
│   └── build_blog.py       # Blog pipeline script
├── .github/
│   └── workflows/
│       └── build_blog.yml  # GitHub Action for blog pipeline
├── CLAUDE.md               # This file
└── data.yaml               # Canonical career data (reference only, not a build input)
```

---

## Design decisions — locked, do not change without discussion

**Color scheme:** Option B — Scientific Paper
  Light: #fafaf8 bg, #181818 text, #1a5fa8 blue accent
  Dark:  #14151a bg, #e6e6e2 text, #4d8fd4 blue accent
  Orange (#cb4b16 light / #d4693a dark): status field in psql block ONLY.
  Single accent color throughout UI. No yellow, no green, no cyan in UI layer.

**Typography:**
  Base font size: 21px
  EB Garamond: all prose, section labels, nav, footer
  Courier New: data specimens only (psql block, exp-stack lines, writing dates)
  Three sizes: --size-caption (0.78rem), --size-body (1rem), --size-name (clamp)

**Italic policy:**
  Italic reserved for: pull quotes, testimonials, hero claim only.
  No italic on secondary text, descriptions, labels, metadata.
  In blog prose, `<em>` and `<i>` render as non-italic weighted emphasis
  (`font-weight: 500`) so the italic reservation is absolute across the
  whole site. Blockquotes in blog posts are treated as pull quotes and
  retain italic. See `.blog-body em` in `blog.css`.

**Layout:**
  Single column. Max-width 640px. Centered. No grid. No annotation margin.
  This is the Yau pivot — narrow focused column, all content in reading flow.

**Career arc SVG:**
  ViewBox 0 0 800 320. Coordinates verified and explicit.
  Direct labels above bars. Proportional time axis (42.1px/year, 19 years).
  Acquisition connector: healthfinch → Health Catalyst (dashed, arrow).
  Bar colors: independent of UI scheme (warm palette, data-encoded).
  Do not change SVG coordinates without recalculating from scratch.

**Hero:**
  No h1 nameplate. Name appears in nav (anchor) and psql block (data) only.
  Sequence: domain sentence → claim (italic, green left border) → contact → psql.
  psql block: exact \x expanded display format. white-space: pre.
  Field alignment: name/title/focus padded to 6 chars so pipes align.

**Inline stack lines:**
  Each experience entry ends with a .exp-stack Courier line.
  Tools only. No methods (stay in prose). No facts (stay in prose).
  Color: --text-dim. No accent color.

**Footer:**
  Plain Garamond. Small caps labels. Blue links. No Courier.

**GoatCounter:**
  One script tag before </body>.
  Site code: TBD — Z to provide.
  Placeholder in v18: <!-- <script data-goatcounter="..."> -->

---

## Content — source of truth

The v18 mockup (tufte-concept-v18.html) is the canonical content source.
All prose, dates, links, and stack lines in v18 are correct and approved.

**Live site:** zaherkarp.github.io (Astro) — use for gap analysis only.
Do not copy structure or CSS from the live site.

**Email:** zaher@zaherkarp.com (confirm before shipping)

**Links:**
  Stars dashboard: links to blog post (no standalone demo URL yet)
  SkillSprout: https://zaherkarp.com/skillsprout
  Medicare Advantage Insight Engine: GitHub repo only
  ECDS Shock Index: GitHub repo only

**Project sites (independent repos, not affected by swap):**
  zaherkarp.github.io/skillsprout — own repo
  zaherkarp.github.io/life-in-weeks — own repo
  These are not part of this project.

---

## Blog pipeline

Blog posts live at src/content/blog/*.md (markdown + frontmatter).
Build script: scripts/build_blog.py
  Reads markdown files (skips any whose stem starts with `_`)
  Uses markdown-it-py + mdit-py-plugins + Jinja2 + python-frontmatter
  Applies templates in scripts/templates/
  Outputs to blog/<slug>/index.html (pretty URLs)
  Rebuilds blog/index.html listing (newest first)
  Regenerates sitemap.xml with homepage + all non-draft posts

Shared prose styles live in /blog.css (referenced by all generated pages).
Portfolio index.html keeps its CSS inline — do not extract.

Client-side CDN features on blog posts, loaded conditionally:
  KaTeX 0.16.11     — only when post contains `$...$` or `$$...$$`
  Mermaid 11        — only when post contains ```mermaid fenced blocks
  Prism 1.29.0      — only when post contains other fenced code blocks
No Python deps for any of these — they load at render time, not build time.
The main site (index.html) has no such scripts; no-build rule is intact.

Local build:
  pip install -r scripts/requirements.txt
  python scripts/build_blog.py

GitHub Action: .github/workflows/build_blog.yml
  Triggers on: push under src/content/blog/ or scripts/ or the workflow itself,
  plus manual workflow_dispatch
  Runs build_blog.py
  Commits generated HTML + sitemap.xml back to the repo
  Requires: Settings → Actions → Workflow permissions → Read and write

Underscore-prefix convention:
  Any src/content/blog/_*.md is skipped by the build.
  Used for fixture markers, meta-docs, and not-yet-ready drafts kept on disk.

Blog posts on live site (zaherkarp.github.io/blog) still need to be migrated.
Migration = copy .md source files into src/content/blog/.
Do not rewrite post content during migration.
See TO-DO #4 for the wipe-before-copy order of operations.

The portfolio writing section shows 6 recent posts with a "View all writing" link.
The link target: zaherkarp.github.io/blog (or /blog once on the new site).

---

## Resume

/resume.pdf is a manually maintained file. It is NOT generated.
It is committed to the repo as a static asset.
The footer links to it as a PDF download.

TO-DO: Decide whether to build resume.html with print CSS (print once to PDF,
commit the PDF, never touch the HTML again) or use existing resume PDF from
prior work. Do not build a generation pipeline for the resume — no build step.

Current resume versions exist from prior work (full, one-page, ATS-optimized).
Z to decide which to commit as /resume.pdf.

---

## TO-DOs (in priority order)

1. Create github.io-redesign repo and enable GitHub Pages
2. Copy v18 mockup as index.html — this is the starting point
3. Set up blog pipeline (build_blog.py + GitHub Action)
4. Migrate blog posts from live site to src/content/blog/
   (a) delete current test fixtures:
       find src/content/blog -maxdepth 1 -name '*.md' ! -name '_*.md' -delete
   (b) copy authoritative .md sources from the live Astro repo
   (c) fix building-my-site.md (no frontmatter in live source)
   (d) run python scripts/build_blog.py and spot-check output
   (e) delete src/content/blog/_FIXTURES_REMOVE_BEFORE_MIGRATION.md
5. Add GoatCounter script tag (Z to provide site code)
6. Confirm email address before shipping
7. Add scroll behavior to nav links (smooth scroll, active state)
8. Mobile nav: test wrapping behavior, consider hamburger if needed
9. Commit /resume.pdf — Z to decide which version
10. Swap: copy finished files into zaherkarp.github.io main, push

---

## What NOT to do

- Do not install npm, node, or any JS build tooling
- Do not add CSS frameworks (Tailwind, Bootstrap, etc.)
- Do not add React, Vue, or any frontend framework
- Do not create a generation pipeline for the resume
- Do not change the 640px max-width or reintroduce a multi-column grid
- Do not change the color scheme without discussion
- Do not change the career arc SVG coordinates without recalculating
- Do not copy CSS or structure from the live Astro site
- Do not add animations beyond the existing section reveal and cursor blink
- Do not conflate github.io-redesign (new) with zaherkarp.github.io (live, untouched)
- Do not commit the src/content/blog/*.md test fixtures as the migration — see
  src/content/blog/_FIXTURES_REMOVE_BEFORE_MIGRATION.md for the proper wipe-and-copy flow
- Do not add server-side syntax highlighting (Pygments, etc.) to the blog build;
  Prism runs client-side via CDN to keep the Python pipeline dependency-light

---

## Key people and context

Z = Zaher Karp. Lead Data Engineer at Baltimore Health Analytics.
BHA = Baltimore Health Analytics (current employer).
The portfolio targets: hiring managers for Director-level roles,
  peers in healthcare data engineering, recruiters in regulated healthcare.
Motivated reader register: long-form prose explaining decisions, not bullet scopes.

---

## Vocabulary

Stars = CMS Star Ratings (Medicare Advantage quality measurement program)
HEDIS = Healthcare Effectiveness Data and Information Set
CMS = Centers for Medicare & Medicaid Services
MA = Medicare Advantage
BHA = Baltimore Health Analytics
healthfinch = prior employer, acquired by Health Catalyst in 2020
Health Catalyst = prior employer (2020-2025)
Yau pivot = the design decision to use a single narrow column (640px)
  rather than the Tufte three-column grid. Named after Nathan Yau (FlowingData).
