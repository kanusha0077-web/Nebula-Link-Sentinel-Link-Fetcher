# Nebula Link Sentinel

**Nebula Link Sentinel** is a Tampermonkey userscript that acts as a **passive link intelligence radar**.

It quietly collects URLs from:

- HTML attributes (`href`, `src`, `srcset`, etc.)
- Raw HTML source
- Network requests (via Performance API + `fetch` wrapping)

…and gives you a small draggable panel with stats and one-click export options (text & JSON).

It’s built for:

- Recon / OSINT work
- Debugging resource loading
- Quickly extracting all URLs from a page or domain
- Copying network-only URLs for deeper analysis in other tools

---

## Features

### 🔭 Live “link radar” panel

A compact, draggable UI in the top-right corner:

- **Indexed URLs** — total unique URLs seen so far
- **This page** — URLs discovered on the current page
- **Network-sourced** — URLs seen from network requests
- **Unique domains** — count of distinct hostnames
- **Last scan** — time of last extraction
- **Host** — current page hostname

You can:

- Drag the panel around
- Collapse/expand the content
- Hide it completely with the close button

---

### 📦 URL collection sources

Nebula Link Sentinel aggregates links from three channels:

1. **DOM attribute scan**
   - Attributes: `href`, `src`, `content`, `data-src`, `data-href`, `action`, `cite`, `poster`, `background`, `srcset`
   - Handles `srcset` entries correctly (multiple URLs in one attribute)

2. **HTML source scan**
   - Regex-based extraction from the full HTML (`document.documentElement.outerHTML`)
   - Flexible patterns to catch URLs inside attributes, CSS `url(...)`, inline JS, etc.

3. **Network activity**
   - Performance entries (`resource`)
   - Wrapped `window.fetch` calls (when used)
   - Tracks URLs requested by the page itself (APIs, assets, etc.)

---

### 🧠 Smart URL handling

- Filters out obvious junk:
  - `javascript:`, `mailto:`, `tel:`, `data:`, `about:`, `void(0)`, `null`, `undefined`, etc.
- Length sanity check (too short or excessively long strings are ignored)
- Accepts:
  - Absolute URLs (`https://example.com/...`)
  - Protocol-relative (`//example.com/...`)
  - Absolute paths (`/path/file`)
  - Relative paths (`./file`, `../file`)
- Converts everything to **absolute URLs** using `new URL()` so the data is consistent.

For each URL, Nebula stores:

- `count` — how many times it’s been seen
- `firstSeen` / `lastSeen` timestamps
- `pages` — which pages it appeared on
- `domain` — hostname (e.g. `example.com`)

All of this is persisted via `GM_setValue`, under the key:

```js
nebulaLinkSentinelLinks
