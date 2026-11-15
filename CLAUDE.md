# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is **Nirmal Prasad Panta's personal academic website** — a live site created from the
[al-folio](https://github.com/alshedivat/al-folio) Jekyll template. It is **not** the al-folio starter
repo itself.

- Deploys to **`https://nirmalprasadpanta.com.np`** (custom domain via `CNAME`), built and pushed by
  `.github/workflows/deploy.yml` on every push to `master`.
- `_config.yml` sets `url: https://nirmalprasadpanta.com.np` and an **empty `baseurl`**.
- The day-to-day work here is **authoring content** — blog posts, course notes, book notes, CV data,
  bibliography — not modifying site runtime.

@AGENTS.md

### How this repo differs from the imported `AGENTS.md` / `docs/`

`AGENTS.md`, `.github/copilot-instructions.md`, and `docs/ARCHITECTURE.md` were imported from upstream
al-folio and describe contributing **to the al-folio project**. When they conflict with the points
below, the points below win for this repo:

- **Baseurl is empty, not `/al-folio`.** Build with a plain `bundle exec jekyll build`; the dev server
  is at `http://localhost:4000/` (no `/al-folio/` path). Never pass `--baseurl /al-folio` here.
- **The "stop sign" on `_layouts/`, `_includes/`, `_sass/`, `_scripts/` is a user-site right, not a
  ban.** al-folio explicitly allows a site created from the template to shadow gem-owned files. The
  catch: `test/style_contract.js` and `test/integration_*.sh` were copied in from upstream and still
  run in `.github/workflows/unit-tests.yml`, so adding those dirs will fail this repo's own CI until
  you also adjust/remove that workflow. Prefer `_config.yml` + content changes; treat a local override
  as a deliberate, CI-aware decision.
- The upstream "route runtime changes to a gem repo" rule does not apply — there is no gem checkout
  here and no upstream PR to open. Runtime changes mean either a local override or a `Gemfile`
  version bump.

## Daily dev loop

```bash
bundle install                        # Ruby gems (Jekyll + jekyll-* + al_* plugin gems)
bundle exec jekyll serve              # dev server → http://localhost:4000/  (live reload)
bundle exec jekyll serve --future     # also render posts dated after today
bundle exec jekyll build              # production-style build to _site/
npm ci                                # dev tooling (prettier, playwright) — only if linting/visual tests
npm run lint:prettier                 # Prettier check; `npx prettier . --write` fixes
```

Prettier uses `@shopify/prettier-plugin-liquid` with `printWidth: 150` (`.prettierrc`). CI
(`prettier.yml`) enforces it — run it before pushing. The last two commits on `master` were prettier
and integration-test fixups, so this is a real gate.

### Optional toolchains

- **Jupyter posts** (`jekyll-jupyter-notebook`): needs `jupyter` + `nbconvert` on `PATH`. Missing
  binaries → notebook rendering is silently skipped, no error.
- **Responsive images**: `imagemagick.enabled: true` in `_config.yml` requires ImageMagick `convert`
  on `PATH`, or the build errors on image-bearing pages.
- **CV rendering** (`render-cv.yml`, `al_folio_cv`): `pip install "rendercv[full]"`.
- **Scholar citations**: `bin/update_scholar_citations.py` needs `scholarly`; normally run by
  `update-citations.yml`, not locally.

## Authoring content

| Content                    | Where                                 | Notes                                                                                                                                                          |
| -------------------------- | ------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Blog posts / notes         | `_posts/YYYY-MM-DD-slug.md`           | `layout: post`; permalink is `/blog/:year/:title/`. Front matter `tags:` drives the tag archive. Course-note series share a tag (e.g. `Theory of Elasticity`). |
| Book notes                 | `_books/*.md`                         | `books` collection (`output: true`), surfaced on `_pages/books.md`.                                                                                            |
| Pages / nav                | `_pages/*.md`                         | `about.md` is the landing page. Nav order/visibility is front matter on each page.                                                                             |
| CV                         | `_data/cv.yml` (+ `_pages/cv.md`)     | RenderCV YAML; also feeds the `al_folio_cv` layout.                                                                                                            |
| Publications               | `_bibliography/*.bib`                 | `jekyll-scholar`; rendered by `_pages/publications.md`. Citation counts in `_data/citations.yml`.                                                              |
| News / projects / teaching | `_news/`, `_projects/`, `_teachings/` | Each is a collection with `output: true`.                                                                                                                      |
| Post images                | `assets/img/<topic>/`                 | Reference via `{% include figure.liquid %}` (al-folio tag). ImageMagick generates responsive variants at build.                                                |

Math in posts uses `$…$` / `$$…$$` (MathJax via `al_math`; `enable_math` is on). Diagrams via
`al_charts` (mermaid, chart.js, plotly, vega) with per-page front matter opt-in
(`mermaid:`, `chart:`, etc.).

**Course-note writing conventions:**

- Whenever a post introduces a concept, definition, or classification rule (e.g. linear vs.
  non-linear, homogeneous vs. non-homogeneous, IVP vs. BVP), always follow the rule with **both
  worked examples that satisfy it and counter-examples that don't** — and, where relevant, the
  tricky edge cases (variable coefficients, coupled unknowns, change-of-variable conversions). The
  abstract rule alone is not enough; the examples/counter-examples are what make it usable.
- **Do not add a "What's next" / "What's coming" section** to posts. It is not wanted.

## Committing

**Never run `git commit`, `git push`, or otherwise commit changes.** The user commits everything
themselves. Make the edits, report what changed, and stop.

## Architecture essentials

al-folio v1 is a **thin starter**: layouts, includes, Sass, Liquid tags, filters, and feature JS all
live in versioned `al_*` gems (`al_folio_core`, `al_math`, `al_charts`, `al_search`, …), not in this
repo. You will **not** find `_layouts/` or `_sass/` in a fresh checkout — they come from the gems.

Two things that fail with **no error message**:

1. **A feature needs three things aligned:** its gem loaded, its site flag on in `_config.yml`
   (`search_enabled`, `enable_math`, `analytics:` IDs, …), and the page's front matter opting in.
   Missing any one → the Liquid tag renders an empty string. Debug in that order.
2. **`Gemfile` and `_config.yml` are two lists that must agree.** A plugin gem must appear in
   `Gemfile` (`group :al_folio_plugins`, exact version pin) **and** in the `plugins:` list of
   `_config.yml`. In only one → inert. Note: `al_marimo` and `al_rtl` are pinned in the `Gemfile` but
   **not** in `_config.yml`'s `plugins:` list, so they are currently inactive.

Read the actual gem versions from `Gemfile` — do not trust any version quoted in prose (upstream docs
included). Bumping a gem = editing the pin in `Gemfile`, `bundle install`, rebuild, verify locally.

Full detail (upstream-oriented but accurate on mechanics): `docs/ARCHITECTURE.md` (wrapper→tag→gem
delegation table, config contract), `docs/BOUNDARIES.md` (which gem owns what).

## CI workflows (`.github/workflows/`)

- `deploy.yml` — builds and deploys to the live domain on push to `master`. This is the one that
  matters day to day.
- `prettier.yml` — Prettier + liquid plugin, `printWidth: 150`.
- `unit-tests.yml` — style contract + `test/integration_*.sh` (inherited from upstream; see the
  override caveat above).
- `render-cv.yml`, `update-citations.yml` — regenerate CV PDF and scholar citation counts.
- `update-tocs.yml` — rewrites `<!--ts-->…<!--te-->` TOC blocks in changed root/`docs/` Markdown;
  expect a follow-up auto-commit if you add or rename a heading there.
- `broken-links-site.yml`, `axe.yml`, `visual-regression.yml`, `lighthouse-badger.yml` — quality
  gates carried over from upstream.

## Agent config

`.claude/skills` and `.codex/skills` are symlinks to `.agents/skills/` (al-folio bootstrap + v1
migration skills). `.gemini/settings.json` only lists context files. There are no MCP servers or
custom slash commands to import.
