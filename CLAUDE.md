# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

Jekyll-based personal academic homepage for **Tasmia Tahmida Jidney**, deployed via GitHub Pages. Content is data-driven (YAML) and rendered through a single homepage layout with section partials.

## Local Development

```bash
bundle install                  # install Jekyll + github-pages + webrick
bundle exec jekyll serve        # serve locally at http://127.0.0.1:4000
bundle exec jekyll build        # build to _site/
```

`webrick` is pinned in the `Gemfile` because Ruby 3+ no longer ships it by default — needed for `jekyll serve` to work.

## Architecture

The site uses the remote theme [`yaoyao-liu/minimal-light`](https://github.com/yaoyao-liu/minimal-light) (see `_config.yml`), so most theme HTML/CSS lives in that remote gem and is **not** in this repo. Only the files that override or extend the theme are checked in. When something visual isn't here, it's coming from the remote theme.

Local overrides and content sources:

- `_config.yml` — site-wide variables (title, position, affiliation, email, avatar, CV path, social links, canonical URL). `remote_theme` selects the base theme; `auto_dark_mode: true` toggles which CSS bundle [`_layouts/homepage.html`](_layouts/homepage.html) loads (`style.css` + `publications.css` vs. their `-no-dark-mode` variants).
- `index.md` — the only page. `layout: homepage`. Stitches together prose sections (About / News / Education / Work / Research / Projects / Skills) with two `{% include_relative %}` partials: `_includes/publications.md` and `_includes/services.md`.
- `_layouts/homepage.html` — overrides the theme's layout. Renders the avatar + name + affiliation + email header and a row of **plain text links** (CV / Google Scholar / GitHub / Linkedin) driven from `site.*` in `_config.yml`. Also wires a favicon-switcher (`assets/js/favicon-switcher.js`) but note `_config.yml` does not currently define `favicon` / `favicon_dark`, so the `<link rel="icon">` tags resolve to empty `href`s — set those keys if you want a working favicon.
- `_includes/publications.md` — iterates `site.data.publications.main` and renders each entry with theme classes (`pub-row`, `bibliography`, `badge`). Supported per-entry fields: `title`, `authors` (HTML allowed for `<strong>` on the site owner), `conference`, and optional `image`, `conference_short`, `pdf`, `code`, `page`, `bibtex`, `notes`, `others`.
- `_includes/services.md` — static "Honors & Awards" content (not data-driven).
- `_data/publications.yml` — list under the `main:` key, consumed by the publications partial.
- `_sass/` (`minimal-light.scss`, `minimal-light-no-dark-mode.scss`) and `assets/css/` (`style.scss`, `style-no-dark-mode.scss`, `publications.css`, `publications-no-dark-mode.css`) — style overrides. The two CSS bundles are mutually exclusive, selected at layout time by `auto_dark_mode`.
- `assets/img/`, `assets/files/` (`curriculum_vitae.pdf`), `assets/js/` (`favicon-switcher.js`, `scale.fix.js`) — static assets.

### Adding content

- New publication → append a YAML entry under `main:` in `_data/publications.yml`; no template changes needed.
- New top-level section → add a Markdown heading directly in `index.md`, or factor it into a new `_includes/*.md` partial if it iterates over data.
- Changing site metadata (name, affiliation, social links, CV path, canonical URL) → edit `_config.yml`, not the layout.

## Deployment

No CNAME file is checked in, and the directory is `tasmia.github.io-main` rather than the GitHub Pages user-site convention `tasmia.github.io`; the `canonical:` in `_config.yml` points at `https://tasmia008.github.io/`. If publishing to a user site, the repository on GitHub must be named `<user>.github.io` for Pages to serve at the root domain — confirm the actual GitHub repo name before assuming the deploy URL. No CI configuration in-repo; the GitHub Pages build runs the pinned `github-pages` gem.
