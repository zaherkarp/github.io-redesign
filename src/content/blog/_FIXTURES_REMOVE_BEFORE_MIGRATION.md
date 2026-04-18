# Test fixtures — NOT the migration

The `.md` files in this directory (excluding this one) are **test fixtures**, not the content migration.

## What happened

On 2026-04-17, 44 blog post source files were copied from the live Astro site at
`/Users/zarp/git/zaherkarp.github.io/src/content/blog/` into this directory
to smoke-test `scripts/build_blog.py` end-to-end. The build succeeded (40 posts
rendered; 3 drafts skipped; 1 post with missing frontmatter skipped).

These files are retained so the pipeline remains testable during further
iteration. They are **not** committed as the real migration.

## Before running CLAUDE.md TO-DO #4 (real migration)

1. Wipe this directory (preserving this marker):

   ```bash
   find src/content/blog -maxdepth 1 -name '*.md' ! -name '_*.md' -delete
   ```

2. Copy the authoritative `.md` sources from wherever Z designates
   (likely the live Astro repo at the time of migration, or a branch/tag of it).

3. Fix `building-my-site.md` — it has no frontmatter in the live source and
   will be skipped by the build until a `title` / `description` / `publishDate`
   block is added.

4. Run:

   ```bash
   python scripts/build_blog.py
   ```

5. Confirm `blog/index.html` and `blog/<slug>/index.html` outputs match expectations.

6. Delete this marker file.

## Why the underscore prefix

`scripts/build_blog.py` skips any `.md` whose filename stem starts with `_`.
This keeps the marker file from being treated as a broken blog post (missing
frontmatter would otherwise produce a noisy warning on every build).

The same convention is available for future non-post markdown (drafts kept on
disk but not yet ready, meta-docs about the blog directory, etc.).
