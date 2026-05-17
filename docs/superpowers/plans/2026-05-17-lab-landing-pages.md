# Unified `lab.howar31.com` Landing Page Implementation Plan

> **For agentic workers:** REQUIRED SUB-SKILL: Use superpowers:subagent-driven-development (recommended) or superpowers:executing-plans to implement this plan task-by-task. Steps use checkbox (`- [ ]`) syntax for tracking.

**Goal:** Replace both container-root landing pages (`lab.howar31.com` and `lab.howar31.com/share`) with a single byte-identical "ambient identity page" — an aurora-drift animation behind a centered `@howar31` link to GitHub.

**Architecture:** One self-contained `index.html` (inlined CSS/JS, no external assets). Strategy A — two independent copies kept byte-identical by hand, one per repo (`howar31/share`, `howar31/howar31.github.io`). A leading HTML comment and a CLAUDE.md rule in each repo record the twin-sync obligation.

**Tech Stack:** Static HTML, inline CSS, vanilla JS, Canvas 2D. No build step, no framework, no test harness.

**Note on testing:** Both repos are intentionally minimal collections of self-contained static files with no test framework. Adding a Node/Puppeteer harness and its dependencies would contradict that and is out of scope (YAGNI). Verification is a concrete manual browser checklist (Task 2), executed against the spec's verification section.

**Spec:** `docs/superpowers/specs/2026-05-17-lab-landing-pages-design.md`

---

## File Structure

| File | Repo | Responsibility |
|---|---|---|
| `index.html` | `share` | The ambient identity page (canonical authored copy) |
| `index.html` | `howar31.github.io` | Byte-identical twin of the above |
| `CLAUDE.md` | `share` | Revised root-page rule + twin-sync rule |
| `CLAUDE.md` | `howar31.github.io` | Twin-sync rule (file created if absent) |
| `.gitignore` | `share` | Already created this session — ignores `.superpowers/` and `.DS_Store` |

Branches: `share` default branch is `main`; `howar31.github.io` default branch is `master`. The `share` repo's recent history is direct commits to `main`; this plan follows that pattern. All commits require explicit user approval before running (per `~/.claude/CLAUDE.md`).

---

## Task 1: Build the ambient identity page in the `share` repo

**Files:**
- Modify (full replace): `/opt/projects/share/index.html`

- [ ] **Step 1: Replace `index.html` with the ambient identity page**

Overwrite `/opt/projects/share/index.html` entirely with this exact content:

```html
<!DOCTYPE html>
<!--
  Ambient identity page for the lab.howar31.com container roots.
  TWIN COPY — this file is byte-identical in two repos:
    howar31/howar31.github.io   -> lab.howar31.com/
    howar31/share               -> lab.howar31.com/share/
  Any change MUST be applied to BOTH repos. See CLAUDE.md in either repo.
-->
<html lang="en">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1.0">
<meta name="robots" content="noindex, nofollow">
<link rel="icon" href="data:image/svg+xml,<svg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 32 32'><rect width='32' height='32' rx='7' fill='%230d1117'/><circle cx='16' cy='16' r='5' fill='%238aa9ff'/></svg>">
<title>howar31</title>
<style>
  html, body { height: 100%; margin: 0; }
  body {
    background: #0d1117;
    overflow: hidden;
    font-family: -apple-system, BlinkMacSystemFont, "Segoe UI", Helvetica, Arial, sans-serif;
  }
  #aurora { display: block; position: fixed; inset: 0; }
  .mark {
    position: fixed;
    inset: 0;
    display: flex;
    align-items: center;
    justify-content: center;
  }
  .mark a {
    font-size: 1.5rem;
    font-weight: 600;
    letter-spacing: -0.5px;
    color: #e6edf3;
    opacity: 0.82;
    text-decoration: none;
    transition: opacity 0.2s ease;
  }
  .mark a:hover {
    opacity: 1;
    text-decoration: underline;
    text-underline-offset: 4px;
  }
  .mark a .at { color: #8aa9ff; }
</style>
</head>
<body>
<canvas id="aurora"></canvas>
<div class="mark">
  <a href="https://github.com/howar31" target="_blank" rel="noopener noreferrer"><span class="at">@</span>howar31</a>
</div>
<script>
(function () {
  var canvas = document.getElementById('aurora');
  var ctx = canvas.getContext('2d');
  var DPR = Math.min(window.devicePixelRatio || 1, 2);
  var reduced = window.matchMedia('(prefers-reduced-motion: reduce)').matches;
  var raf = null;

  // Three drifting radial-gradient glows. sp = drift speed, ph = phase offset,
  // rad = radius as a fraction of the shorter viewport edge.
  var blobs = [
    { hue: '88,166,255',  ph: 0, sp: 0.00022, rad: 0.55 },
    { hue: '63,185,150',  ph: 2, sp: 0.00017, rad: 0.62 },
    { hue: '188,140,255', ph: 4, sp: 0.00029, rad: 0.48 }
  ];

  function resize() {
    canvas.width = window.innerWidth * DPR;
    canvas.height = window.innerHeight * DPR;
    canvas.style.width = window.innerWidth + 'px';
    canvas.style.height = window.innerHeight + 'px';
    ctx.setTransform(DPR, 0, 0, DPR, 0, 0);
  }

  function draw(t) {
    var w = window.innerWidth, h = window.innerHeight;
    ctx.fillStyle = '#0d1117';
    ctx.fillRect(0, 0, w, h);
    ctx.globalCompositeOperation = 'lighter';
    for (var i = 0; i < blobs.length; i++) {
      var b = blobs[i];
      var cx = w * (0.5 + 0.36 * Math.sin(t * b.sp + b.ph));
      var cy = h * (0.5 + 0.34 * Math.cos(t * b.sp * 1.3 + b.ph));
      var R = Math.min(w, h) * b.rad;
      var g = ctx.createRadialGradient(cx, cy, 0, cx, cy, R);
      g.addColorStop(0, 'rgba(' + b.hue + ',0.28)');
      g.addColorStop(1, 'rgba(' + b.hue + ',0)');
      ctx.fillStyle = g;
      ctx.beginPath();
      ctx.arc(cx, cy, R, 0, Math.PI * 2);
      ctx.fill();
    }
    ctx.globalCompositeOperation = 'source-over';
  }

  function step(t) {
    draw(t);
    raf = requestAnimationFrame(step);
  }
  function start() {
    if (reduced) { draw(0); return; }
    if (raf == null) raf = requestAnimationFrame(step);
  }
  function stop() {
    if (raf != null) { cancelAnimationFrame(raf); raf = null; }
  }

  resize();
  start();

  window.addEventListener('resize', function () {
    resize();
    if (reduced) draw(0);
  });
  document.addEventListener('visibilitychange', function () {
    if (document.hidden) stop(); else start();
  });
})();
</script>
</body>
</html>
```

- [ ] **Step 2: Verify the file has no syntax errors**

Run: `node --check <(sed -n '/<script>/,/<\/script>/p' /opt/projects/share/index.html | sed '1d;$d')`
Expected: no output (exit 0). If it errors, the inline JS has a syntax problem — fix before continuing.

Proceed to Task 2 for behavioral verification. Do not commit yet — Task 5 commits everything together.

---

## Task 2: Verify the page behavior in a browser

**Files:** none (verification only)

- [ ] **Step 1: Open the page**

Run: `open /opt/projects/share/index.html` (macOS — opens in the default browser).

- [ ] **Step 2: Confirm the visual checklist**

Verify each item by looking at the page:
- Background is near-black `#0d1117` with three soft colored glows (blue, teal, purple) slowly drifting and blending. No hard particles, no banding.
- `@howar31` is centered on both axes. The `@` is blue (`#8aa9ff`); `howar31` is near-white (`#e6edf3`).
- Hovering `@howar31` brings it to full opacity and shows an underline.
- No scrollbars; the canvas fills the whole viewport.

- [ ] **Step 3: Confirm the link target**

In DevTools, inspect the `<a>` element. Expected: `href="https://github.com/howar31"`, `target="_blank"`, `rel="noopener noreferrer"`. Clicking it opens `github.com/howar31` in a new tab.

- [ ] **Step 4: Confirm reduced-motion behavior**

In Chrome DevTools: open the Rendering panel (⋮ → More tools → Rendering), set "Emulate CSS media feature prefers-reduced-motion" to `reduce`, then reload the page.
Expected: the aurora renders one static frame and does NOT animate.
Reset the emulation to "No emulation" afterward.

- [ ] **Step 5: Confirm visibility-pause behavior**

With the page animating, switch to another browser tab for a few seconds, then switch back.
Expected (verify in DevTools Performance or by observing): the animation pauses while the tab is hidden and resumes on return. A quick proxy check — in the Console, run `document.hidden` while focused (should be `false`); the `visibilitychange` handler stops/starts the RAF loop.

- [ ] **Step 6: Confirm resize behavior**

Resize the browser window. Expected: the canvas re-fills the viewport with no distortion and the marker stays centered.

If any check fails, return to Task 1 Step 1, fix the HTML, and re-run Task 2.

---

## Task 3: Update `share/CLAUDE.md`

**Files:**
- Modify: `/opt/projects/share/CLAUDE.md`

- [ ] **Step 1: Revise the root-page rule under `## Layout`**

In `/opt/projects/share/CLAUDE.md`, find this exact bullet:

```
- Root `index.html` is a fixed, non-informational ambient placeholder. Do not
  turn it into an index or link it to the items — the site root must reveal
  nothing.
```

Replace it with:

```
- Root `index.html` is an ambient identity page (aurora-drift animation +
  centered `@howar31` linking to GitHub). It must still reveal nothing about
  the items: do not turn it into an index or link it to them.
- Root `index.html` is byte-identical to `index.html` in the
  `howar31/howar31.github.io` repo (served at `lab.howar31.com/`). Any change
  to one MUST be applied to the other; verify with `diff`.
```

- [ ] **Step 2: Verify the edit**

Run: `grep -n "ambient identity page\|byte-identical" /opt/projects/share/CLAUDE.md`
Expected: two matching lines printed. No other change to the file.

Do not commit yet — Task 5 commits everything together.

---

## Task 4: Propagate the page to the `howar31.github.io` repo

**Files:**
- Create (clone): `/opt/projects/howar31.github.io/` (working copy)
- Modify (full replace): `/opt/projects/howar31.github.io/index.html`
- Create or modify: `/opt/projects/howar31.github.io/CLAUDE.md`

- [ ] **Step 1: Clone the repo**

Run: `gh repo clone howar31/howar31.github.io /opt/projects/howar31.github.io`
Expected: clone succeeds. Confirm `git -C /opt/projects/howar31.github.io branch --show-current` prints `master`.

- [ ] **Step 2: Copy the page verbatim**

Run: `cp /opt/projects/share/index.html /opt/projects/howar31.github.io/index.html`

This copies the file built in Task 1. The leading HTML comment already names both repos, so no per-repo edit is needed.

- [ ] **Step 3: Verify the two copies are byte-identical**

Run: `diff /opt/projects/share/index.html /opt/projects/howar31.github.io/index.html && echo IDENTICAL`
Expected: `IDENTICAL` (diff prints nothing, exit 0).

- [ ] **Step 4: Handle `howar31.github.io/CLAUDE.md`**

Check whether the file exists: `test -f /opt/projects/howar31.github.io/CLAUDE.md && echo EXISTS || echo MISSING`

If MISSING, create `/opt/projects/howar31.github.io/CLAUDE.md` with this exact content:

```markdown
# howar31.github.io

Account-level GitHub Pages site for `lab.howar31.com`. A container root: it
hosts unrelated themed items at sub-paths, monorepo-style. This repo holds only
the root page.

## Root page

`index.html` is an ambient identity page (aurora-drift animation + centered
`@howar31` linking to GitHub). It must reveal nothing about the items hosted
under the domain — no index, no links to them.

## Twin copy

`index.html` is byte-identical to `index.html` in the `howar31/share` repo
(served at `lab.howar31.com/share/`). Any change to one MUST be applied to the
other; verify with `diff`.
```

If EXISTS, append the following section to the end of the existing file instead (do not duplicate any heading it already has):

```markdown

## Twin copy

`index.html` is an ambient identity page byte-identical to `index.html` in the
`howar31/share` repo (served at `lab.howar31.com/share/`). Any change to one
MUST be applied to the other; verify with `diff`.
```

- [ ] **Step 5: Verify the CLAUDE.md edit**

Run: `grep -c "Twin copy" /opt/projects/howar31.github.io/CLAUDE.md`
Expected: `1`.

Do not commit yet — Task 5 commits both repos.

---

## Task 5: Commit both repos

Both commits require explicit user approval before running. Present a summary and wait.

- [ ] **Step 1: Stage and review the `share` repo**

Run: `git -C /opt/projects/share add -A && git -C /opt/projects/share status --short`
Expected staged files: `.gitignore`, `index.html`, `CLAUDE.md`, `docs/superpowers/specs/2026-05-17-lab-landing-pages-design.md`, `docs/superpowers/plans/2026-05-17-lab-landing-pages.md`.

- [ ] **Step 2: Commit the `share` repo**

Prefer the `/commit` skill. If committing directly, use this message:

```bash
git -C /opt/projects/share commit -m "$(cat <<'EOF'
feat: replace root with ambient aurora identity page

Replace the drifting-dot placeholder with an ambient identity page:
aurora-drift canvas animation behind a centered @howar31 link to GitHub.
Byte-identical twin of howar31.github.io/index.html. Includes the
brainstorming design spec and implementation plan.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 3: Stage and review the `howar31.github.io` repo**

Run: `git -C /opt/projects/howar31.github.io add -A && git -C /opt/projects/howar31.github.io status --short`
Expected staged files: `index.html`, `CLAUDE.md`.

- [ ] **Step 4: Commit the `howar31.github.io` repo**

```bash
git -C /opt/projects/howar31.github.io commit -m "$(cat <<'EOF'
feat: replace landing page with ambient aurora identity page

Replace the name-card landing page with an ambient identity page:
aurora-drift canvas animation behind a centered @howar31 link to GitHub.
Byte-identical twin of the share repo's root index.html.

Co-Authored-By: Claude Opus 4.7 (1M context) <noreply@anthropic.com>
EOF
)"
```

- [ ] **Step 5: Push (only if the user asks)**

Per `~/.claude/CLAUDE.md`, push only on explicit request. If asked:

```bash
git -C /opt/projects/share push origin main
git -C /opt/projects/howar31.github.io push origin master
```

Both pages redeploy automatically via GitHub Pages after the push.

---

## Post-deploy verification (after push)

- [ ] Visit `https://lab.howar31.com/` and `https://lab.howar31.com/share/` — both show the identical aurora identity page.
- [ ] On `lab.howar31.com/share/`, confirm the existing items are still reachable by direct URL and not linked from the root: `https://lab.howar31.com/share/x-algorithm-report/` and `https://lab.howar31.com/share/steam-screenshots-inventory-2026-05-17-2022/`.
- [ ] View source on the live pages — confirm `<meta name="robots" content="noindex, nofollow">` is present.

---

## Self-Review

- **Spec coverage:** Head/meta/favicon → Task 1. Aurora → Task 1 + Task 2 Step 2. Marker + link → Task 1 + Task 2 Steps 2-3. Reduced-motion → Task 1 + Task 2 Step 4. visibilitychange → Task 1 + Task 2 Step 5. `share/index.html` replace → Task 1. `howar31.github.io/index.html` replace → Task 4. Twin comment → Task 1 Step 1 (leading comment). `share/CLAUDE.md` revision → Task 3. `howar31.github.io/CLAUDE.md` → Task 4 Step 4. Byte-identical diff → Task 4 Step 3. Item-reachability check → Post-deploy verification. All spec sections covered.
- **Placeholder scan:** No TBD/TODO. All code shown in full. All commands have expected output.
- **Type consistency:** Function names (`resize`, `draw`, `step`, `start`, `stop`) and the `blobs` array shape are defined once in Task 1 and referenced consistently. Branch names (`main` for `share`, `master` for `howar31.github.io`) consistent across Tasks 4-5.
