# share

Ad-hoc public hosting for occasional static files (HTML reports, demos, etc.),
served via GitHub Pages under https://REDACTED/share/.

## Layout

- One directory per shared item; the item's entry file is `index.html` inside it.
  The URL becomes `https://REDACTED/share/<item>/` — no `.html`, no
  `index.html` in the path.
- `README.md` is the index: one row per item with title, publish date, and link.
- No category or year subdirectories. Keep the structure flat at one level until
  a real need appears (avoid premature abstraction).
- Each item must be self-contained. Single-file HTML (inlined CSS/JS) is preferred;
  if an item needs assets, keep them inside that item's own directory.

## Custom domain

- This repo is served at https://REDACTED/share/ — a sub-path of the
  account's GitHub Pages custom domain.
- The custom domain `REDACTED` is owned by the `REDACTED`
  user-site repository, not this one. Do NOT add a `CNAME` file here — a project
  repo automatically inherits the account custom domain as `/<repo>/`.

## Privacy

- This repository and its full commit history are public.
- Only commit content safe to be fully public. No employer-internal material,
  no personal identifying information, no secrets.
- Rewriting public history to remove a mistake is painful — verify before committing.

## Adding an item

1. Create `<item>/index.html` (plus any assets in the same directory).
2. Add a row to `README.md`.
3. Commit and push to `main`; GitHub Pages redeploys automatically.
