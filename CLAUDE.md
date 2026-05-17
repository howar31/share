# share

Ad-hoc public hosting for occasional static files (HTML reports, demos, etc.),
served via GitHub Pages.

## Layout

- Root `index.html` is a fixed, non-informational ambient placeholder. Do not
  turn it into an index or link it to the items — the site root must reveal
  nothing.
- One directory per shared item; the item's entry file is `index.html` inside
  it, so the item is reached at `/<item>/` — no `.html`, no `index.html` in the
  path.
- No master index is maintained anywhere (no list in `README.md`, no links
  between items). Each item is standalone and appears unrelated to the others.
  This is deliberate — it avoids index maintenance and gives slight obscurity.
- No category or year subdirectories. Keep the structure flat at one level until
  a real need appears (avoid premature abstraction).
- Each item must be self-contained. Single-file HTML (inlined CSS/JS) is preferred;
  if an item needs assets, keep them inside that item's own directory.
- Do not add a `CNAME` file to this repo — Pages custom-domain routing is
  configured at the account level, not per-repo.

## Privacy

- This repository and its full commit history are public; only the GitHub Pages
  surface is kept quiet (no served index, items unlinked).
- Only commit content safe to be fully public. No employer-internal material,
  no personal identifying information, no secrets.
- Rewriting public history to remove a mistake is painful — verify before committing.

## Adding an item

1. Create `<item>/index.html` (plus any assets in the same directory).
2. Commit and push to `main`; GitHub Pages redeploys automatically.
3. Share the item's `/<item>/` URL directly with whoever needs it. Nothing links
   to it, so it is only reachable by that URL.
