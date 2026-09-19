# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project

Website for **Savitaipaleen Matkailu SAMAT ry** ("Samat"), a Finnish association that
was previously named *Savitaipaleen aurinkosähkö ry* and ran the site sasry.fi.

Two workstreams:

1. **Archive** (done) — a complete, faithful copy of the old sasry.fi site, so the new
   site can link to it from an archive section.
2. **New site** (pending) — a simple static site whose structure is to be derived from
   the association's new bylaws (*säännöt*), which the user will supply. Do not invent
   that structure before the bylaws arrive.

Published via **GitHub Pages** from `https://github.com/teppotk/samatry` (branch `main`,
root directory). The user writes in Finnish; all site content is Finnish.

## Commands

```sh
# Local preview (no build step — this is plain static HTML)
python3 -m http.server 8000     # http://localhost:8000/

# Re-capture the old site (see "Archive" below before doing this)
wget --mirror --page-requisites --convert-links --no-parent \
  --span-hosts --domains=sasry.fi,www.sasry.fi \
  --no-host-directories --restrict-file-names=windows \
  --directory-prefix=archive/sasry.fi \
  --wait=0.3 --random-wait -e robots=off \
  https://www.sasry.fi/
```

There is no build, no package manager, no test suite. Anything added should keep it that
way unless the user asks otherwise — GitHub Pages serves these files directly.

## Archive (`archive/sasry.fi/`)

Captured 2026-09-19 with the wget command above. 22 pages, ~26 MB.

The original was a Zoner WebsiteBuilder site: Bootstrap 3 + jQuery, every page a
`wb_header` / `wb_main` / `wb_footer` stack of absolutely-positioned `wb_element` divs,
with per-page stylesheets `css/<n>.css`.

Things that will bite you if you touch this tree:

- **Treat it as read-only for content.** It is an archival record. Put anything
  explanatory in `archive/index.html` rather than rewriting the mirrored pages' text.
  Asset *plumbing* was repaired once (see next point) and may be repaired again.
- **Asset filenames no longer contain `@` — this was fixed 2026-09-19.**
  `--restrict-file-names=windows` had turned the original `?v=`/`?ts=` cache-busting
  query strings into filenames like `css/site.css@v=20240229101528`. Those names do not
  end in `.css`, so both `python3 -m http.server` and GitHub Pages served them as
  `application/octet-stream`, and browsers refuse a stylesheet with a non-CSS MIME type.
  **The whole archive rendered unstyled, locally and live, from the first commit.**
  The fix: strip the `@…` suffix from all 40 affected files and rewrite the 118
  references in 24 HTML/CSS/JS files. Duplicates that collapsed to the same name were
  verified byte-identical first. Verified afterwards: 204 local CSS/JS assets across all
  22 pages return 200 with a correct MIME type.
  **If you ever re-run the wget capture, you must redo this** — drop
  `--restrict-file-names=windows`, or strip the suffixes again afterwards.
- **Directory names contain Finnish characters** (`Tietoa-meistä/`,
  `Yhteistyötahot/`). Links reference them percent-encoded (`Tietoa-meist%C3%A4`).
- **`<base href="" />` remains in every page.** wget emptied the original
  `<base href="https://www.sasry.fi/">`. Leave it — removing it is unnecessary and an
  empty href correctly resolves to the document's own URL.
- Three images 404'd during capture (`img/flags_matrix.png`, `img/icon-logo-22x22.png`,
  `img/icon-logo-red-22x22.png`). They were already missing on the live site.
- `og:url` meta tags still point at the live `sasry.fi`. Intentional — that is what the
  page said.
- `archive/sasry.fi/gallery/` holds the old PDFs, including the **old** association's
  bylaws (`savitaipaleen_aurinkosahkoyhdistyksen_saannot.pdf`). These are historical;
  the new bylaws are a separate document.

## GitHub Pages notes

- `.nojekyll` at the repo root is required: Jekyll would otherwise drop files and
  directories the archive depends on.
- Pages is enabled on `main` / root. Live at `https://teppotk.github.io/samatry/`;
  the archive is at `/samatry/archive/`. All links inside the mirror are relative, so it
  also works under a custom domain or any other prefix.
- The site root currently 404s — there is no `index.html` yet. That page is part of the
  new-site workstream, pending the bylaws.
- Finnish directory names are stored NFC-normalized in git (`ä` = `\303\244`) and linked
  as `%C3%A4`. macOS hands back NFD from the filesystem, so a shell loop that pipes
  `find` output into `curl` will produce `%CC%88` and spurious 404s. Not a real failure —
  compare against git, not the filesystem.
