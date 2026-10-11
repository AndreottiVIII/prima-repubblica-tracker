# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is (and isn't)

A **mirror**, not an engine. It publishes https://primarepubblica.net/ — who is still alive among parliamentarians and ministers of the Italian First Republic (Costituente 1946 → XI legislatura, plus the Ciampi and Dini governments).

All data, the scraping/cross-referencing scripts (Wikidata, Camera and Senato open data, itwiki articles), the page template and its documentation live in the source project **[AndreottiVIII/duri-a-morire](https://github.com/AndreottiVIII/duri-a-morire)**. That project renders one model into two editions that differ only in name. For this site it also builds the sober-named edition split into one HTML file per Televideo screen (`primarepubblica.tar.gz`, written by `scripts/pagine_statiche.py` there), and this repo fetches that archive and redeploys it here. The point is that the two sites can never diverge and the upstream sources are queried only once.

Consequence: **content, data or layout fixes to the page belong upstream**, not in `sito/index.html`. Editing that file here is pointless — the workflow overwrites it with the upstream copy on every run.

## Layout

- `sito/` — the Pages artifact root. What gets deployed is the upstream archive unpacked on top of it: one small `index.html` per screen (`/`, `/viventi/`, `/legislature/xi/`, `/onorevole/rino-formica/`, `/viventi/partito/dc/`…, about 4,800 files), each with its own title, description and canonical, all loading the shared `televideo.js` (~1.5 MB, the program plus the data) and `stile.css`, plus `404.html` (the same Televideo, which reads the path itself) and `sitemap.xml`. Every page carries a `"generato":"YYYY-MM-DD"` marker. None of this is committed: the committed `index.html` is an old single-file snapshot, overwritten at build time. Visually the site is the same single Televideo; navigating inside it uses `pushState` to the real paths without reloading, and old `#Q…`/`#p…` links still work and rewrite themselves to the real path. Once Google has indexed a path, don't rename it: add, don't rename. `favicon.png` and `anteprima.png` (the 1200×630 share preview) do belong here: the upstream tracker edition's `og:image` points at `https://primarepubblica.net/anteprima.png`, so deleting it breaks link previews. `robots.txt` lives here too and points at `sitemap.xml`, which now comes from upstream inside the archive (all ~4,800 URLs; `<lastmod>` only on `/`).

SEO itself (title, meta description, `canonical` pointing at the domain, JSON-LD `WebSite` + `Dataset`, `data-nosnippet` on the Televideo screen so Google stops quoting the grid in result snippets) is generated upstream in duri-a-morire's `genera_sito.py` and `modello.html`.
- `.github/workflows/aggiorna.yml` — the whole pipeline ("Rispecchia e pubblica").

The custom domain is set in the repo's Settings → Pages, not by a `CNAME` file (with Actions deploys GitHub ignores it). DNS lives at GoDaddy: apex A/AAAA to GitHub Pages, `www` CNAME to `andreottiviii.github.io`, plus the `_github-pages-challenge-andreottiviii` TXT that keeps the domain verified on the account and an apex `google-site-verification=` TXT that keeps it verified in Google Search Console. The old `andreottiviii.github.io/prima-repubblica-tracker/` URL 301s to the domain.

Visits are counted by GoatCounter (`primarepubblica.goatcounter.com`, cookieless). The snippet lives in duri-a-morire's `scripts/modello.html` and only fires when `location.hostname` is `primarepubblica.net`.

## The workflow

Triggers: cron at `47 5` and `47 14` UTC, `workflow_dispatch`, and any push to `main` (no `paths` filter, so every commit, docs included, redeploys). An empty commit is how a republish has been forced in the past.

Steps: `curl` the upstream `primarepubblica.tar.gz` → unpack into `nuovo/` → sanity-check → `cp -R nuovo/. sito/` → `upload-pages-artifact` → `deploy-pages`.

Guards that must be preserved (the principle: *better a site stuck at yesterday than a site worse than yesterday*):
- abort if the archive is under 500,000 bytes (an error disguised as an archive);
- abort if `index.html` is missing or doesn't contain the string `Prima Repubblica Tracker` (wrong edition);
- abort if `televideo.js` is under 500,000 bytes (the program without its data);
- abort if there are fewer than 4,000 `onorevole/*/index.html` schede;
- implicitly, abort if the `"generato":"YYYY-MM-DD"` marker is missing from `index.html`: the `VERSIONE=$(… | grep …)` line looks like logging, but under `set -e` (no `pipefail`) a failed final `grep` kills the step.

The rationale for the cron times is in the workflow's header comment. No version comparison exists: every run redeploys, even when upstream hasn't changed.

## Commands

No build, lint or tests. Useful checks:

```bash
gh workflow run aggiorna.yml
```

```bash
gh run list --workflow=aggiorna.yml
```

When the site looks stale, check upstream first: if duri-a-morire is updated and this one isn't, re-run the workflow.

## Conventions

- Everything user-facing (README, workflow step names, comments, echo messages, commit messages) is written in Italian, in plain explanatory prose; YAML comments avoid accented letters (`gia'`, `e'`).
- `.gitattributes` forces LF for `*.yml`.
