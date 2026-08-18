# Handoff: Blog Design Refinement — Round 2

## Status

Round 1 (this session) is done and committed to the working tree (not yet
pushed — see "Left to do"). The specimen-catalog concept is unchanged;
this pass tightened color, type, motion, and accessibility inside it, per
the Impeccable skill's discipline (`C:\Workspace\Archer\.claude\skills\impeccable`
— scoped to the Archer repo, not this one, so it was applied by reading
its reference docs directly rather than invoking it as a slash command).

## What changed

All in `assets/css/main.css` unless noted.

1. **Palette converted to OKLCH**, with the old hex values kept as the
   fallback (browsers without OKLCH support silently keep the hex; see
   the `@supports (color: oklch(0% 0 0))` block). Same hues/intent as
   before — this was a verification and cleanup pass, not a recolor.
2. **Contrast was verified, not eyeballed.** Computed via a small Node
   script (WCAG relative-luminance formula) against the literal values
   now in the CSS — every text/background pair in use clears AA (4.5:1),
   most clear AAA (7:1). Notably: `--paper-dim` on `--ink`, the pair the
   original brief specifically flagged as unverified, was already at
   7.7:1 — it turned out fine. I didn't find a real contrast failure
   anywhere in the existing palette; the value of this step was turning
   "picked by eye" into "measured," not fixing a defect.
3. **`--surface` / `--surface-raised` were dead tokens** (declared, never
   used anywhere). Gave them a real job: the liked state of the like
   button now fills with `--surface-raised`, so the elevation ladder
   means something instead of sitting unused.
4. **Real fluid type scale**, Utopia-style `clamp()` derived from a
   320px→760px viewport range (760 chosen because the reading column is
   680px + padding — past that, growing type further doesn't track
   anything). Replaces the old `clamp(1.9rem, 5vw, 2.6rem)`-style
   ad-hoc values. Three roles: `--step-catalog-title`,
   `--step-post-title`, `--step-site-title`. Verified by hand-computing
   the interpolated px output at 320/375/414/680/760/1024/1440 — smooth
   ramp, flat past 760px, no runaway values.
5. **One motion moment**, per the brief's "only if it earns it": liking a
   post. The star icon does a short scale pop-and-settle
   (`@keyframes like-stamp`, 380ms, `cubic-bezier(0.16, 1, 0.3, 1)`,
   no bounce/elastic easing) meant to read as a specimen stamp coming
   down — tied to the catalog identity, not a generic hover effect.
   Everything else (link hovers, catalog row hover) stays as fast, quiet
   feedback on purpose; this is a reading surface, not a marketing page.
   `_layouts/post.html`'s inline script now removes/reflows/re-adds the
   `like-button--liked` class so the animation replays on repeat clicks
   instead of only firing once — the only JS change; all API calls, ids,
   and classes blog-mcp depends on are untouched.
6. **Accessibility pass:**
   - `prefers-reduced-motion` now also kills the like-stamp animation
     and the like-button color/border transition, while leaving the
     `--surface-raised` fill and color change themselves intact (state
     still changes, it just doesn't animate there — per Impeccable's
     "reduce, don't erase" guidance).
   - `color-scheme: dark` added to `:root` so native browser chrome
     (scrollbar, form control, selection defaults) matches the site
     instead of defaulting to light.
   - `::selection` themed to brass-on-ink instead of the browser default.
   - `text-underline-offset` added to links for cleaner underlines.
   - Like-button padding increased (`0.5rem 0.9rem` → `0.8rem 0.9rem`)
     to bring it close to the 44px touch-target minimum. Share-link/
     copy-link were deliberately left as plain inline text controls —
     they're short inline links inside a text row, which is the
     conventional exception to the 44px rule, not an oversight.
   - Focus-visible states were already present and correct; left as-is.
7. **Removed the `.site-header__eyebrow` ("Field notes / catalog") label**
   above the site title in `_layouts/default.html`. This is the one
   content change beyond color/type/motion, and it's a deliberate call,
   not an accident: Impeccable's craft floor lists a kicker/label above
   a heading as a hard ban ("no brief earns it back... delete the label
   and let the heading speak"), and this one added no information the
   title + description didn't already carry. The specimen-tag on
   individual posts (catalog code + date, above the post title) is
   *not* the same pattern and was kept — it's functional metadata, not
   a decorative kicker, and the brief requires it to keep working.
   Flagging this explicitly in case it's not what was wanted; easy to
   revert (it's one line back in `default.html` plus the old CSS rule,
   preserved in git history at `9dae676`/earlier commits if needed).
8. Reading measure tightened slightly: `main` and `.site-header__inner`
   max-width `720px` → `680px`, bringing the body-text measure from
   ~83ch down to ~73ch, inside the 65–75ch target instead of just above
   it.

## What was checked, and how (be precise about this)

- **Contrast ratios**: computed programmatically (Node, WCAG relative
  luminance) against the exact hex strings written in `main.css` —
  reverified after writing the file, not just at the design stage.
- **Fluid type scale math**: computed programmatically at real viewport
  widths, in a second independent script after the first one had a unit
  bug (mixed rem/px without converting) — caught by getting a nonsense
  result (every width outputting the max value) and redoing it in
  pure-px space. Worth knowing about since it means the first version of
  this check was silently wrong and would have looked fine if not
  cross-checked.
- **CSS syntax**: brace-balance checked programmatically (58 open / 58
  close). Not a real parse — no CSS parser was available in this
  environment — so this is weak evidence, not proof the file is valid
  CSS. Read it yourself before trusting it further.
- **NOT checked**: actual rendering in a browser. I built two static
  preview HTML files (home list + single post, wired to the real
  `assets/css/main.css`) specifically to screenshot and visually verify
  contrast, the fluid scale, and the like-button animation — but no
  browser was reachable in this environment (Playwright's MCP wants a
  system Chrome install that isn't present and can't be installed
  without admin rights; a bundled Chromium was installed via
  `npx playwright install chromium` but the Playwright MCP server is
  hardcoded to the `chrome` channel and won't use it; the Zen-browser
  MCP expects a macOS path that doesn't exist on this Windows machine).
  The preview files were deleted before finishing rather than left in
  `assets/` (Jekyll would have copied them into the live site as
  real, unlinked pages). **This is the single biggest gap**: everything
  above is verified by computation and code review, not by looking at
  it. Open `_posts/2026-08-18-einsteins-blunder.md` locally or push to a
  branch and check GitHub Pages' preview before trusting this visually.
- **NOT checked**: `bundle exec jekyll build`. No Ruby/Bundler/Jekyll on
  this machine (`ruby`, `bundle`, `jekyll` all "command not found"). The
  Liquid changes are small and mechanical (removed one `<span>`, added
  three lines to an inline `<script>`) and I read the surrounding
  templates carefully, but this is a "parses in my head" claim, not a
  "built" one. Run the real build before trusting the Liquid is well-formed.
- Admin panel (`admin/config.yml`, `admin/index.html`): untouched,
  confirmed via `git status` — not in the diff at all.

## Left to do

1. **Actually look at it.** Push to a branch (or run locally with Ruby
   installed) and view both the home list and a post page. Specifically
   check: does the fluid title scale feel right at real phone widths;
   does the like-button stamp animation feel good or overwrought; does
   removing the eyebrow label read as "tighter" or "missing something."
2. **Decide on the eyebrow removal** (item 7 above) — I made a
   judgment call per the brief's "use your own design judgment" note,
   but it's the one change that isn't purely mechanical/verifiable, so
   it's the one most worth a second opinion.
3. Nothing is pushed yet — changes are local/uncommitted in the working
   tree (`_layouts/default.html`, `_layouts/post.html`,
   `assets/css/main.css`). Commit and push once reviewed.
4. If a future session has real browser access, do the visual QA pass
   this one couldn't: screenshot home + post at mobile/tablet/desktop
   widths, and specifically confirm the like-button animation looks
   like a "stamp" and not a jitter.

## Files touched

- `assets/css/main.css` — full rewrite of the design tokens and the
  rules that consume them; structure/selectors otherwise unchanged.
- `_layouts/default.html` — removed the eyebrow `<span>`.
- `_layouts/post.html` — three-line change to the like-button click
  handler (retrigger the animation on repeat likes).
- Not touched: `admin/config.yml`, `admin/index.html`, `_layouts/home.html`,
  `_posts/2026-08-18-einsteins-blunder.md`, `_config.yml`, `Gemfile`.
