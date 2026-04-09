# The Dynamism Dossier

> A declassified-archive presentation of a16z's **American Dynamism** thesis — 10 interviews, 12 visual plates, one interactive thesis map, stitched into a single HTML file.

**Live** → https://augustave.github.io/dynamism/
**Source** → https://github.com/augustave/dynamism

`zero-build` · `single-file HTML` · `Canvas 2D` · `inline SVG` · `WebP` · `GitHub Pages`

---

## What it is

The Dynamism Dossier is a longform, interactive presentation of the American Dynamism thesis as articulated across ten a16z podcast episodes (7+ hours of primary source). It argues — through interviews with operators like Shyam Sankar, Alex Karp, Emil Michael, Bridgit Mendler, Chris Power, David Sacks, Marc Andreessen, Ben Horowitz and others — that the United States must dismantle peacetime bureaucracy and unleash capitalist dynamism to re-industrialize the nation across defense, space, biotech, energy, and public safety.

Rather than a standard deck or scrollable article, the webapp is framed as a **declassified WWII field dossier** in the visual language of a COMINCH P-series document — aged parchment, SECRET stamp, typewriter fonts, organic rotations, a CLASSIFICATION-CHANGED-TO-OPEN-SOURCE override, and an interactive thesis map rendered in an isometric "distant forces" grid.

Every layer cross-links. A quote drives the map. A map node opens an entity modal. A transcript launches the map at the right step *and* links out to the YouTube episode. The dossier is designed to be wandered through, not read front-to-back.

---

## Tour

| # | Section | What it does |
|---|---|---|
| 1 | **Cover** | Inline SVG facsimile of a COMINCH declassified document. Every element is individually rotated ±0.5°–4.5° to break grid illusion — the "organic stamp." |
| 2 | **Part I · Field Analysis** | Twelve archival plates rendered as a 3D **rolodex**. Center card in focus, neighbors peek at reduced scale/rotation/opacity, far cards dim. Click the center → lightbox zoom. |
| 3 | **Source Transcripts** | Ten interview cards, collapsed by default. Expand for full thesis, then either **Launch on Thesis Map** or **Watch Episode** on YouTube. |
| 4 | **Thesis Map** | Interactive Canvas 2D isometric graph. ~66 nodes (People, Orgs, Concepts) positioned on a shaded cube around a central "American Dynamism" sun. Step through 10 transcripts to watch the argument assemble. |
| 5 | **Quote Wall** | Twenty tactical quotes, 2-line clamped. Click to expand full context *and* auto-drive the thesis map to the corresponding transcript — the feedback loop with the graph at the center. |

---

## Research methods

The dossier rests on a disciplined corpus-to-structure pipeline. All of the data is baked into `index.html` as literal arrays; there is no database and no build step.

### 1. Corpus assembly
Ten a16z Podcast / Deep Dives episodes, totalling 7+ hours, were listened to and transcribed. Source playlist: [*American Dynamism*](https://youtube.com/playlist?list=PL9fqpZPNJX7eqQvH-Py-QN7bZL6V_PISC).

### 2. Entity extraction
~66 discrete entities were lifted from the corpus and categorized into three namespaces:

- **People** (`P_*`) — founders, operators, partners, historical exemplars (Rickover, Boyd, Cukor as heretical-founder prototypes)
- **Organizations** (`O_*`) — Palantir, DoD, Northwood, Hadrian, SpaceX, Navy, Army, Amplify, Chariot, Flock, a16z
- **Concepts** (`C_*`) — Defense Reformation, Permissionless Innovation, Vendor Lock, Eroom's Law, Intelligence-Based Policing, Attritable Mass, Signature Management, and so on

Each entity carries a `{role, bio, quotes, related}` record. Visible in `index.html` as the `ENTITY_META` map (~lines 1037–1087).

### 3. Thesis decomposition per episode
Each of the 10 transcripts was reduced to a structured `step` object:

```js
{id, tab, title, guest, thesis, quote, qctx, nodes, edges}
```

`nodes` is the subset of the ~66 entities that participate in *that* episode's argument, and `edges` are the pairwise relations to draw between them. Lives in `steps[]` (~line 631). Stepping through the map animates the assembling argument.

### 4. Quote curation
Twenty tactical quotes were pulled across the corpus, each tagged by `cat` (`people` / `strategy` / `bottleneck` / `outcome`) and by `t` (source transcript number). See `QUOTES[]` (~line 1091). The `cat` taxonomy lets the quote wall filter by argumentative function, not just by speaker.

### 5. Playlist verification
To wire every surface back to the original YouTube episode, I scraped the playlist via `curl` and a regex pass over `ytInitialData`, confirmed the playlist order matched the `steps[]` order 1:1, and reduced the result to a single lookup table:

```js
const YT={1:'1LcH4lP9XbA',2:'jHECsr9GCs8',3:'tL3sXpxpCPs', …};
const ytUrl=n=>`https://www.youtube.com/watch?v=${YT[n]}&list=PL9fqpZPNJX7eqQvH-Py-QN7bZL6V_PISC`;
```

Keeping `&list=` in every href means a single click drops the viewer *into the playlist in order* — "link the links."

---

## Design techniques

### Declassified-archive aesthetic
- Custom parchment palette (`--paper:#D8D0B8`, `--warm:#CFC7A4`, `--cool:#C8C0A0`) tuned so the cover SVG and the canvas tool share one background.
- Typeface triad: typewriter (`Special Elite`), serif body, monospaced meta.
- SECRET stamp, CLASSIFICATION-changed-to-OPEN-SOURCE override stamp, LIBRARY / RESEARCH ARCHIVE stamp, warning box, corner declass block — all inline SVG.
- **Organic stamping**: every SVG element is wrapped in its own `<g transform="rotate(deg cx cy) translate(dx dy)">` with a small, distinct angle between ±0.5° and ±4.5°. This deliberately breaks the perceptual vertical line that a rigid grid of stamps would otherwise create.

### Isometric thesis map
The "distant forces" graph is a Canvas 2D isometric projection rendered on an `rAF` loop:

```js
const COS30 = Math.cos(Math.PI/6), SIN30 = Math.sin(Math.PI/6);
function iso(x, h, z) {
  return { px: (x - z) * COS30, py: (x + z) * SIN30 - h };
}
```

Node height `h` is a function of the entity's role (`people: 78`, `org: 32`, `strategy: 52`, `outcome: -8`, `bottleneck: -48`, central sun: `0`), so the cube literally stratifies the thesis. The renderer draws three shaded cube faces, drop-stems from each node to the floor, radial spokes from the central `C_AmDyn` sun to every visible node, depth-sorts dots, and caps the sun with a radial-gradient glow.

### Hit-testing at parity
After the isometric rewrite, `pickNode()` was re-grounded to project to screen space using the *exact same* scale (`Math.min(W/860, H/720) * 0.78`) and translate (`W/2, H/2 + 36`) as `draw()`. Any divergence between the two causes clickable drift — so both functions share the constants.

### CSS variable scoping
The thesis-map tool has its own palette (`--tt-ink`, `--tt-accent`, `--tt-paper`, …) scoped under `.thesis-tool`. This avoids collision with the root `:root` variables used by the cover SVG, which was the original culprit for a two-tone background bug early in the build.

### The 3D rolodex
Part I · Field Analysis is built as a pure-CSS perspective rolodex — no 3D library, no JS animation loop.

```css
.fa-rolo { perspective: 1600px; }
.fa-card[data-pos="center"]    { transform: translate(-50%,-50%) rotateY(0)     scale(1);    opacity: 1;    z-index: 5; }
.fa-card[data-pos="left"]      { transform: translate(-92%,-50%) rotateY(28deg) scale(.82);  opacity: .55; z-index: 3; }
.fa-card[data-pos="right"]     { transform: translate(-8%,-50%)  rotateY(-28deg) scale(.82); opacity: .55; z-index: 3; }
.fa-card[data-pos="far-left"]  { …scale(.66); opacity: .18; }
.fa-card[data-pos="far-right"] { …scale(.66); opacity: .18; }
.fa-card[data-pos="hidden"]    { opacity: 0; }
```

On navigate, every card's `data-pos` is recomputed from its distance to the current index as `(i - cur + N) % N`. The CSS transitions do all the work. Keyboard (← →), click on a side card, prev/next buttons, and touch-swipe all feed the same `go(d)` function. Clicking the center card opens a full-screen lightbox for zoom.

### The feedback loop
The graph is the center of the webapp; every other surface is a way to seed it.

- **Quote click** → expands the card → auto-drives the thesis map to the matching step (`window.goTo(step)`)
- **Transcript expand** → reveals an explicit "Launch on Thesis Map →" button *and* a "Watch Episode" link
- **Map node click** → opens the entity modal with bio, quotes, and related nodes
- **Every card** → has a watch link back to YouTube via the single `YT{}` table

### Performance
- `requestAnimationFrame` is gated behind an `IntersectionObserver` — the map only redraws when the `#tool` section is on screen.
- Canvas `resize()` is a one-shot on first intersection (avoids layout thrash).
- The 12 Field Analysis plates are converted from PDF → PNG (via `sips`) → WebP (via `cwebp -q 78`), and loaded `lazy`. Total image weight: **~1.2 MB for all 12 plates**.
- Quote card expansion is mutually exclusive (`open` class removed from siblings) so DOM reflow stays bounded.

### Accessibility
- ARIA roles: `tablist` / `tab` on the step progress, `dialog` on entity + plate modals, `region` on the rolodex.
- Keyboard navigation: ← → for map steps *and* rolodex, `0–9` for direct step jump, `Esc` for modal dismiss.
- `sr-only` live region (`#tool-a11y`) mirrors the current map step for screen readers.

### Persistence
URL hash deep-linking (`#t1`, `#t7`, `#analysis`, `#dossier`, `#quotes`) is written via `history.replaceState`, never `location.hash =`, so cold-loading a link *doesn't* auto-scroll the page away from the cover before the user has seen it.

---

## Progression — commit by commit

Each commit represents a meaningful design iteration, not just a mechanical edit.

| # | SHA | Phase | Summary |
|---|---|---|---|
| 1 | [`abd57f9`](https://github.com/augustave/dynamism/commit/abd57f9) | **Initial commit** | Merged v5 prototype with a new inline-SVG cover facsimile of the COMINCH P-006 D-Day declass, isometric Canvas 2D thesis map with ~66 nodes, entity modal, 10 transcript cards, quote wall, full research corpus. |
| 2 | [`a7464a8`](https://github.com/augustave/dynamism/commit/a7464a8) | **Collapse + feedback loop** | Transcripts and quote cards started fully expanded — too much prose on load. Compressed both by default; click to expand; quote clicks auto-drive the thesis map. Establishes the graph-centered feedback loop. |
| 3 | [`30d68bb`](https://github.com/augustave/dynamism/commit/30d68bb) | **Watch links** | Scraped the a16z YouTube playlist, verified 1:1 ordering against `steps[]`, added a single `YT{}` lookup, surfaced "Watch" actions on every transcript card, quote card, and map step. Every `&list=` param drops viewers into the playlist in order. |
| 4 | [`288305c`](https://github.com/augustave/dynamism/commit/288305c) | **Part I · Field Analysis** | Twelve archival plates (COVID/TSMC catalysts, Legacy Prime vs. Dynamism model, the triangle, exquisite vs. attritable, Space 1.0→2.0, intelligence-based policing, energy sovereignty, the ecosystem cycle, the "more like us" closer) added as an inline visual essay. PDF → PNG (`sips`) → WebP (`cwebp -q 78`). |
| 5 | [`3ce711f`](https://github.com/augustave/dynamism/commit/3ce711f) | **Lightbox grid** | The inline essay doubled page length. Collapsed to a compact 4-column thumbnail grid with a full-screen lightbox modal — prev/next, keyboard, `Esc`. |
| 6 | [`6ed3ebf`](https://github.com/augustave/dynamism/commit/6ed3ebf) | **3D rolodex** | Thumbnail grid reframed as a perspective-3D rolodex: one plate centered, two plates peeking on each side, far plates dimmed. Click a side card → flip; click the center → lightbox zoom. Arrow keys, buttons, and touch-swipe all drive the same transition. |

---

## Repository layout

```
index.html                        # single-file app (~1400 lines, zero dependencies)
img/frame*.webp                   # 12 Field Analysis plates (total ~1.2 MB)
IMG-12/Frame *.pdf                # raw source artwork for the plates
A16Z-SMALL.md                     # condensed research corpus
Transcript A16Z  - JSON.md        # full transcript research
a16z-dynamism-v5.html             # v5 prototype (pre-merge reference)
.claude/launch.json               # dev server config (python3 -m http.server 8000)
README.md                         # this file
```

---

## Running locally

```bash
python3 -m http.server 8000
# then open http://localhost:8000/
```

If you're using Claude Code, `preview_start` reads `.claude/launch.json` and does the rest.

---

## Deployment

Deployed via **GitHub Pages** from `main` / `/` root. Every push to `main` triggers a Pages rebuild. Live at **https://augustave.github.io/dynamism/**.

---

## Credits

- **Research + concept + execution**: Ebenz Augustave · ANP Studio
- **Design inspiration**: a16z Design · Greg Truesdell
- **Source material**: a16z Podcast Network · American Dynamism Practice
- **Built with**: [Claude Code](https://claude.com/claude-code) (Opus 4.6)

> *"How do we win the next century? Not by becoming more like them. By becoming more like us."* — Plate 10
