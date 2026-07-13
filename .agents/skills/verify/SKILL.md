---
name: verify
description: Verify changes to the static homepage (index.html) by serving it locally and driving it with Playwright + system Chrome
---

# Verifying the homepage

This repo is a static GitHub profile homepage: a single self-contained `index.html` (vanilla JS, no build step) plus `assets/photos/`.

## Serve

```bash
python3 -m http.server 8931 --bind 127.0.0.1   # from repo root, background it
```

## Drive

No local Playwright install; install it in the scratchpad and use the system Chrome channel (the ms-playwright browser cache is version-mismatched):

```bash
cd <scratchpad> && npm init -y && npm install playwright --no-fund
# in scripts: chromium.launch({ channel: 'chrome' })
```

## Flows worth driving

- Typing terminal: wait for `#term` text to end with `> Turn Unthinkable to Reality.` (~10s).
- Viewfinder HUD: move pointer inside `#top`, `#af` shows `○ ONE SHOT`; after ~300ms idle it flips to `● FOCUS` and `#reticle`/`#level` turn green. `#tc` timecode must tick. HUD is display:none below 820px and on hover:none devices.
- Gallery: with no files in `assets/photos/`, all 3 `.photo-frame`s get the `empty` class (via img `onerror`), zoom buttons hidden, lightbox never opens. Drop a jpeg at `assets/photos/photo-1.jpg` (generate one by screenshotting a gradient page with Playwright, `type: 'jpeg'`), reload: frame fills, `⤢` opens `#lightbox`, ESC and backdrop click close it. Remove the test photo afterwards.
- GitHub stats: `#stat-repos`/`#stat-followers` come from `api.github.com/users/AngelMsger`; `#stat-stars` is stars *received* — the sum of `stargazers_count` over `/users/AngelMsger/repos?per_page=100` (confirmed with the user; the design mock's 540 was their starred-repos count, not this). Abort `https://api.github.com/**` routes to confirm the hardcoded fallback numbers stay.
- Mobile 375px: no horizontal scroll, `.about-grid`/`.work-grid` collapse to 1 column.

## Gotchas

- The two 404s for missing `photo-*.jpg` in the console are expected (they trigger the placeholder path).
- Global preference: `grep -n '[，。、；：？！“”（）]' index.html` must return nothing.
