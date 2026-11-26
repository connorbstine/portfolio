# Connor Stine portfolio: notes for future changes

Live site: https://connorbstine.github.io/portfolio/ (GitHub Pages, deploys from `main`, root folder).
Plain static site: no build step. Push to `main` and it is live in 1 to 2 minutes.

## Layout

- `index.html` is the whole main site: welcome menu, tabs (Portfolio / Projects / Tools), cards, footer.
- Cards come from the `ITEMS` list near the top of the main script. Each item has `tab`, `group`
  (section heading), `title`, `desc`, `tag` and an optional `link`. A new group name makes a new section.
  Links starting with `http` open in a new tab; relative links (like `tools/heart/`) open in the same tab.
- `tools/<name>/index.html` are full standalone pages copied from Connor's Claude artifacts
  (heart, neuron, immune). Copy them byte-for-byte, then add the "Back to site" link just before
  `</body>` (see the existing pages for the exact snippet, it points to `../../#tools`).
- `vendor/swarm.js` is the intro animation.

## The swarm intro (main screen)

- Based on Swarm Rebuild (github.com/DhvanilPatel/swarm-cursors, v1.2.0), which is built on
  Joel Califa's Swarm. Both gave permission by email (Nov 2026).
- **Joel's condition: credit in the footer.** Keep the `<footer class="credit">` line in `index.html`.
  Dhvanil said no credit is needed for him.
- `index.html` sets `window.__swarmSite` before loading `vendor/swarm.js`. That turns on SITE MODE:
  no bottom toolbar (a small top-left pill instead: agents, layers, sound, Skip), no blank opening,
  auto-scroll while building, auto fade back to the real page, then scroll to top.
- Speed: `seconds` in `window.__swarmSite` is a time budget (currently 1.9s). The build runs at
  `minSpeed` (3x) and speeds up, up to `maxSpeed`, if a page has more layers than fit in the budget.
  Auto-scroll is paced to reach the bottom by about 70% of the budget. Measured Nov 2026: tap to real
  page in 2.4 to 3.0 seconds on every tab, laptop and phone. Connor wants it at 3 seconds or less.
- Every change to `vendor/swarm.js` is marked with a `SITE MODE` comment. If the upstream file is ever
  updated, re-apply those blocks. With `window.__swarmSite` unset it behaves like the extension.
- `USE_SWARM = false` in `index.html` switches back to the simpler built-in intro (the `build()` engine).

### How the swarm sees new content

The swarm snapshots whatever is on the page when it starts, so new cards and sections are picked up
automatically. Things to keep in mind when adding content:

- Images (`<img>`, SVG, CSS backgrounds): rebuilt properly. Give images a width and height (or an
  aspect ratio) so the layout is stable before they load.
- Self-hosted videos (`<video src>`): rebuilt as real muted videos. Add a `poster` image.
- `<iframe>` embeds (YouTube, etc.) and `<canvas>`: the website version cannot screenshot them, so they
  show as grey placeholder boxes during the build. Prefer a thumbnail image that links out, or accept
  the placeholder.
- Lazy-loaded content (`loading="lazy"`) below the first screen may not be captured, because site mode
  skips the pre-scan scroll. Use normal loading for anything on the main tabs.
- Only the visible tab is built. Very long tabs make the intro longer, since it auto-scrolls the whole tab.

## Checking a change

Serve the folder (`python3 -m http.server`), open it, press "Watch it build", and check that the build
starts at once, scrolls, fades back to the real page and returns to the top, with no console errors.
Check a phone-width viewport too (the top-left pill shrinks below 560px).
