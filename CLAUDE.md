# share

Ad-hoc public hosting for occasional static files (HTML reports, demos, etc.),
served via GitHub Pages.

## Layout

- Root `index.html` is a redirect stub: it sends visitors to `howar31.com`
  (client-side — `location.replace` plus a `<meta refresh>` no-JS fallback).
  It is not a landing page and reveals nothing about the items.
- One directory per shared item; the item's entry file is `index.html` inside
  it, so the item is reached at `/<item>/` — no `.html`, no `index.html` in the
  path. Items may also be nested one level under a thematic group directory,
  reached at `/<group>/<item>/` (see grouping below).
- No repo-wide master index exists (the root only redirects; `README.md` lists
  no items). Standalone items stay unlinked and appear unrelated — deliberate,
  for slight obscurity. Exception: a group directory may carry its own scoped
  portal `index.html` that links only that group's own members.
- Prefer a flat, single-level structure (avoid premature abstraction). Once
  several items share a clear theme, that is the "real need" to group them under
  one single-level group directory — optionally with a scoped portal `index.html`
  as above. `gw2/` is the first such group (a portal over its member pages).
- Each item must be self-contained. Single-file HTML (inlined CSS/JS) is preferred;
  if an item needs assets, keep them inside that item's own directory.
- Do not add a `CNAME` file to this repo — Pages custom-domain routing is
  configured at the account level, not per-repo.

## Privacy

- This repository and its full commit history are public; only the GitHub Pages
  surface is kept quiet (the root serves only a redirect; items stay unlinked
  except within a group's own scoped portal).
- Only commit content safe to be fully public. No employer-internal material,
  no personal identifying information, no secrets.
- Rewriting public history to remove a mistake is painful — verify before committing.

## Adding an item

1. Create `<item>/index.html` (plus any assets in the same directory). To add to
   an existing group instead, create `<group>/<item>/index.html` and register the
   page in the group portal's own data list (e.g. the `ITEMS` array in
   `gw2/index.html`, which self-documents the steps).
2. Commit and push to `main`; GitHub Pages redeploys automatically. This repo is
   a single-page sharing container, not an evolving codebase, so the commit flow
   skips documentation sync: `README.md` keeps no item index (see Layout) and
   there is no `SPEC.md` to maintain.
3. Share the item's `/<item>/` URL directly with whoever needs it. Nothing links
   to it, so it is only reachable by that URL.

Optional — rich link previews (Discord / Slack / X): add Open Graph + Twitter Card
tags plus a 1200×630 `og.png` in the item's own directory. `og:image` and `og:url`
must be **absolute** URLs — this repo is served at `https://lab.howar31.com/share/`
(account-level custom domain, no per-repo `CNAME`), so e.g.
`https://lab.howar31.com/share/<item>/og.png`. Previews only resolve once deployed,
and consumers cache aggressively. See `gw2/` for a worked example.
