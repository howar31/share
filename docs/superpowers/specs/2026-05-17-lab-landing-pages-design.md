# Design: Unified landing page for `lab.howar31.com` and `/share`

Date: 2026-05-17
Status: Approved (design phase)

## Goal

`lab.howar31.com` (repo `howar31/howar31.github.io`) and `lab.howar31.com/share`
(repo `share`) are both container roots: each hosts multiple unrelated themed
items underneath, monorepo-style. They are siblings in nature, so their root
landing pages should share one design.

Deliver a single "ambient identity page" used verbatim by both roots:

- An ambient animated visual (aurora drift) as the page body.
- A low-key, centered `@howar31` identity marker linking to GitHub.
- Reveals nothing about the items hosted underneath either root.
- The two pages are byte-identical.

## Decisions (locked during brainstorming)

| Topic | Decision |
|---|---|
| Root page nature | Ambient identity page — atmosphere as body, low-key identity marker, items never revealed |
| Identity marker | `@howar31` |
| Visual style | Aurora drift — soft radial-gradient glows slowly drifting; no particles |
| Two pages differ? | No — completely identical (same file, same colors) |
| Marker is a link? | Yes — links to `github.com/howar31` |
| Marker placement | Centered (both axes) |
| Search indexing | `noindex, nofollow` on both pages |
| Sync strategy | Two independent copies kept byte-identical manually (no tooling) |

## Page specification

Single file `index.html`, self-contained (inlined CSS/JS), no external assets.

### Document head

- `<!DOCTYPE html>`, `<html lang="en">` (content `@howar31` is language-neutral).
- `<title>howar31</title>`.
- `<meta name="viewport" content="width=device-width, initial-scale=1.0">`.
- `<meta name="robots" content="noindex, nofollow">`.
- Inline SVG favicon as a `data:` URI (dark field + a single accent dot) — avoids
  a 404 favicon request and keeps the page a single self-contained file.

### Background — aurora

- Background color `#0d1117`.
- One full-viewport `<canvas>`.
- 3 drifting radial-gradient glows, composited with `globalCompositeOperation =
  'lighter'`:
  - blue `rgb(88,166,255)`
  - teal `rgb(63,185,150)`
  - purple `rgb(188,140,255)`
- Each glow: core alpha ~0.28 fading to 0 at the edge; center position drifts on
  independent sin/cos cycles with different periods.
- `devicePixelRatio` capped at 2.
- Exact glow parameters (radius, speed, alpha) tunable during implementation.

### Identity marker

- `@howar31` centered on both axes.
- `@` colored `#8aa9ff`; the rest `#e6edf3`.
- System font stack, weight 600, tightened letter-spacing.
- Wrapped in `<a href="https://github.com/howar31" target="_blank"
  rel="noopener noreferrer">`.
- Hover: marker sits slightly translucent by default, reaches full opacity on
  hover plus a faint underline, ~0.2s transition; cursor `pointer`.

## Behavior

- Animation driven by `requestAnimationFrame`; `resize` listener re-sizes the
  canvas.
- `prefers-reduced-motion: reduce`: do not start the loop — draw one static
  aurora frame only.
- `visibilitychange`: stop the RAF loop when the tab is hidden, resume when
  visible (same approach as the current `share` root page).

## Repo changes and deployment

Strategy A — two independent copies, kept byte-identical by hand.

- `share/index.html` — replace the current drifting-dot field with the new page.
- `howar31.github.io/index.html` — replace the current name-card with the same
  page. Repo is not cloned locally; `gh repo clone howar31/howar31.github.io`
  first.
- Both `index.html` files get a leading HTML comment noting the twin copy exists
  and that any change must be applied to both repos.
- `share/CLAUDE.md` — revise the rule that the root is "non-informational" /
  "must reveal nothing": the root now shows the `@howar31` identity page, but
  still never reveals any item. Add the twin-sync rule.
- `howar31.github.io` — if it has no `CLAUDE.md`, add a minimal one recording
  the twin-sync rule; if it has one, append the rule.
- `noindex, nofollow` does not affect the `howar31.github.io/` →
  `lab.howar31.com/` 301 redirect (that is configured at the DNS / Pages layer).

## Verification

- Open both `index.html` files locally in a browser: aurora animates, marker is
  centered, the link points to `github.com/howar31`.
- DevTools emulate `prefers-reduced-motion: reduce` → static frame, no loop.
- Switch the tab to background → animation pauses; return → it resumes.
- After deploy, visit `lab.howar31.com` and `lab.howar31.com/share` → identical.
- Confirm the existing `share` items (`steam-screenshots-inventory-...`,
  `x-algorithm-report`) are still reachable by direct URL and still unlinked
  from the root.
- `diff` the two repos' `index.html` → byte-identical.

## Out of scope (YAGNI)

- Cross-repo automatic sync (build script, GitHub Action).
- Brand assets beyond the inline favicon.
- Any subtitle or tagline on the page.
