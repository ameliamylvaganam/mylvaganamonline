## Quick orientation

This repository is a hand-coded static portfolio site (HTML, CSS, vanilla JS). There is no build system—open the files in a browser or run a simple static server to preview changes.

High level:
- `index.html` is the main page and loads `script.js` and `style.css`.
- `script.js` holds most of the site's dynamic content as JS arrays/objects (not a templating system). Look for `projects`, `about`, and `greetings` arrays.
- Subpages live under `subpages/` and can have their own CSS/JS (for example `subpages/journal/functions.js` and `subpages/stylesheets/homecafestyle.css`).
- Many images and media are loaded from CDN URLs (Glitch) embedded directly in the JS objects.

## Important patterns and conventions (do these, not that)
- Content is data-driven inside `script.js` — to add or update a project, add an object to the `projects` array. Keys commonly used: `name`, `username`, `centeralign`, `leftalign` (array), `images` (array of HTML strings), `embedcode`, `leftagain`. Example item shape:

```js
{
  name: "Chiu Quon",
  username: "chiuquon",
  centeralign: "A glimpse into...",
  leftalign: ["camera & editing <b>Mylvaganam</b>"],
  images: ["<img src='...'>"],
  embedcode: "<iframe ...></iframe>"
}
```

- Navigation and anchoring: the page generates links using `id = '<username>link'` and section containers with `id = '<username>'`. The `imFeelingLucky()` function relies on that naming convention. If you add a `username`, ensure both the link and the target id exist (the generator code does this for you when `projects` includes the object).
- The project detail reveal uses CSS selector ` .textblock div { display: none }` and ` .textblock div:target { display: block }` — this means project sections are toggled via hash fragments (e.g. `#chiuquon`). Avoid changing this CSS without updating how anchors work.
- Index-based headings: `script.js` inserts section headings by checking array indices (e.g., `if (i == 0)` → FILM, `i == 4` → CINEMATOGRAPHY, `i == 8` → PERSONAL). This is brittle — if you reorder or insert items, headings may move. When adding lots of content, update those index checks or convert to a data-driven `category` field.

## Files worth reading first
- `script.js` — main site logic and content arrays (primary source of truth).
- `index.html`, `homecafe.html` — page structure and examples of subpage usage.
- `style.css`, `subpages/stylesheets/*` — global and subpage styling.
- `subpages/journal/functions.js` — example of a small standalone sub-app (uses `document.write` and a separate data array pattern).

## Developer workflow (how to preview and debug)
- Quick preview: open `index.html` in your browser. For live reload use VS Code Live Server or run a local server from the repo root:

```bash
# from repo root
python3 -m http.server 8000
# then open http://localhost:8000
```

- Debugging: use the browser console (the code relies on `window.onload = () => initMain()` in `index.html`). Typical issues show up as `Uncaught TypeError` when an element id is missing or data arrays are malformed.
- Check `script.js` for brittle patterns: direct `document.write`, index checks, and string-concatenated HTML. When changing content prefer editing the arrays rather than manually changing generated DOM.

## Common pitfalls to avoid
- Don't rename a `username` value in `projects` without ensuring the generated link (`<username>link`) and section container (`id=<username>`) still match — `imFeelingLucky()` and other logic expect that convention.
- `document.write()` (used in `subpages/journal/functions.js`) will clobber the document if called after load — review how/when these functions are called before refactoring.
- The code uses a small amount of brittle index-based logic for headings. If you add many projects, either update the hardcoded index checks in `script.js` or refactor items to include a `category` property and update the generator.
- Many assets live on an external CDN; network errors will break image/video embeds. Prefer local fallback during development if needed.

## Small refactors an AI assistant can safely propose or implement
- Convert index-based section headings to an explicit `category` property on each project object and generate headings from that.
- Move long HTML strings in arrays into small helper templates or functions to improve readability.
- Replace fragile `document.write` usage with DOM creation (e.g., `createElement`) in `subpages/journal/functions.js`.

## Where to ask for clarification
- If you need to change the content model (add `category` or rename keys), say so — I can refactor `script.js` to keep behavior stable.
- If the goal is to modernize (modules, bundler, templating), that is a larger change — summarize the target and I can sketch a migration plan.

If this looks good I can commit this file. Tell me if you want additional examples (e.g., a ready-to-paste new-project object) or if you'd like me to also implement one of the safe refactors.
