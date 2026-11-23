# n-c-taylor.github.io

Personal academic website for Nicholas C. Taylor (PhD candidate, Statistics, Baylor),
built with Quarto and hosted on GitHub Pages at https://n-c-taylor.github.io.

## Build and publish

- Quarto is not on PATH. Use RStudio's bundled binary:
  `/usr/lib/rstudio/resources/app/bin/quarto/bin/quarto render` (full site) or
  `... render <file>.qmd` (one page).
- Output goes to `docs/` (GitHub Pages serves `main` /docs). Commit sources **and**
  the re-rendered `docs/` together.
- The user commits and pushes via GitHub Desktop. Don't commit or push; when
  asked, provide a commit summary + description instead, and list new files that
  must be ticked.
- R is installed (knitr, gstat, fastkcv, pkgdown available). No node/pip; Python 3
  with PIL is available. Headless Firefox is available for screenshots.

## Visual checks

- Serve locally: `cd docs && python3 -m http.server 8765`, then
  `firefox --headless --no-remote --profile <fresh dir> --window-size W,H --screenshot out.png http://localhost:8765/page.html`.
  Use a fresh profile dir after CSS changes (Firefox caches styles.css).
- Headless screenshots run before async JS finishes (PDF.js CV, GitHub version
  fetch) and show no scrollbars, so check those and sideways overflow
  (`scrollWidth - clientWidth`) on the live site in the browser pane after a push.
- Light mode: copy `docs/` and inject
  `localStorage.setItem("quarto-color-scheme","default")` in `<head>`.

## Design system

- Dark navy/teal theme, dark by default with a light toggle.
  `theme-dark.scss` / `theme-light.scss` set Bootstrap variables and the CSS
  tokens (`--bg`, `--surface`, `--border`, `--text`, `--heading`, `--muted`,
  `--accent`, `--accent-rgb`, `--accent-fill`, `--accent-tint`, ...).
- `styles.css` holds all shared components and must use tokens only (no hard-coded
  theme colors): `.pill`, `.pill-neutral`, `.btn-accent`, `.btn-ghost`,
  `.feature-card`, `.card-grid`, `.icon-box`, `.stat-strip`/`.stat`,
  `.section-head` + `.eyebrow`, `.timeline`, `.cv-entry`, `.teaser-card`,
  `.role-card`, `.award-card`, `.course-tile`, `.quote-card`, `.citation-card`,
  `.software-card`, `.news-list`.
- Fonts: Bricolage Grotesque (headings) + Geist (body), from Google Fonts.
- Navbar: page links centered at >=1100px; the CV link is styled as the filled
  teal button via `.nav-link[href$="cv.html"]`.
- `#quarto-content { overflow-x: clip }` stops the home hero glow from causing
  sideways scroll; keep it.

## Content and data files

- `index.qmd`: home. Hero is two-column (text left, photo card right) and the
  page draws its own `<h1>`; Quarto's title block is hidden via `body-classes: home-page`.
  News listing comes from `news.yml` (`_news.ejs`, newest 5).
- `teaching.qmd`: stats, award cards, course tiles, role cards. "How I teach" and
  "Student feedback" are built but commented out until the user supplies text.
- `publications-conferences.qmd` + `.yml` + `_citation.ejs`: one listing with
  All/Papers/Talks filter chips, thumbnails (`thumb`, `thumb-fit: contain` for
  logos), Copy BibTeX (generated from YAML fields). Item fields are documented at
  the top of the YAML.
- `software.qmd` + `software.yml` + `_software.ejs`: project cards. `github:` makes
  the version pill read live from the repo's DESCRIPTION; `site:` adds a
  Documentation button. fastkcv docs: https://n-c-taylor.github.io/fastkcv/ (pkgdown,
  separate repo).
- `cv.qmd`: renders `files/cv.pdf` in-page with PDF.js (cdnjs, pinned 3.11.174).
  Update the CV by replacing `files/cv.pdf`.
- `research.qmd`: `draft: true`, intentionally hidden. Don't link to it (no
  `project:` links to research.qmd) until the user publishes it.
- `_includes/site-scripts.html`: copy-to-clipboard for `.copy-btn[data-copy]` and
  the GitHub version fetch. `_includes/icons.html`: PNG/apple-touch favicons.

## EJS listing templates

Build one HTML string and print it raw with `<%= html %>` (in Quarto, `<%=` is raw
and `<%-` escapes, the reverse of standard EJS), so Pandoc doesn't reinterpret
indentation as Markdown. Escape every field with the template's `esc`/`escapeHtml`.
Pandoc re-serializes attributes, so `&#10;` in attributes becomes real newlines
(fine for `data-copy`).

## Logo and favicon

- `images/nct-logo.svg`: interlocked NCT monogram (Bricolage Grotesque 800, teal C
  in front, navy rounded tile), stored as outlined paths with no font dependency.
  It is both the favicon and the navbar logo.
- PNG fallbacks in `images/icons/` and the root `favicon.ico` were generated from it
  (headless Firefox render at 512px, then PIL masking and resizing). Regenerate them if the logo
  changes. Rename the SVG when it changes so browsers drop their cached favicon.

## Conventions

- Don't invent the user's content (research claims, teaching philosophy, quotes,
  dates): ask, or leave clearly marked placeholders commented out.
- Prefer existing components and tokens over new one-off styles.
