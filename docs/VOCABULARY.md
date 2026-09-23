# Technical Vocabulary — this project, three ways

The same story told three times: (1) in full industry jargon, (2) in plain
language with the matching technical term after each phrase, (3) as a
glossary table anchoring every term to where it appears in this project.
Read 1 to test yourself, 2 to decode it, 3 to make it stick.

This is a small project — one `index.html` of about 500 lines of its own code,
plus a vendored QR encoder — so the vocabulary is small too. Everything below
is something the file actually does.

---

## 1. The jargon-dense version

### What it is

QR Sign Maker is a **single-file web app**: all **markup**, **CSS**, and
**JavaScript** live **inline** in `index.html`, with one **vendored**
dependency, **no build step**, and **no network access** at runtime, so it
works fully **offline**. It's deployed as a **static site** on **GitHub
Pages** — a push to `main` is the whole **deployment pipeline**. The user types
text to encode (or loads an existing QR code image), adds optional top and
bottom text, and produces a print-ready Letter-size page, which the browser's
own **print pipeline** turns into a **PDF**.

### QR generation

The encoder is Nayuki's `qrcodegen`, an **MIT-licensed** **TypeScript**
library **transpiled** to plain JavaScript with `tsc` and pasted into a
`<script>` block with its license header — **vendoring** rather than a
**package manager**, so the file stays self-contained. `encodeText()` picks
the smallest **QR version** (symbol size) that fits the input in the most
compact **encoding mode** (numeric, alphanumeric, byte, kanji), at
**error correction level H**, the highest, so up to 30% of the symbol can be
damaged and still scan. The returned object exposes a **module matrix** via
`getModule(x, y)`; the app walks it and emits one `M x y h1v1h-1z` command per
dark module into a single SVG **path** — a hand-rolled **SVG renderer** of
about fifteen lines. The **viewBox** is padded by four modules on every side
to bake in the **quiet zone**. Input is **trimmed** first, and if it exceeds
the level-H **capacity** (about 1,270 bytes) the library throws a
`RangeError`, which is caught and surfaced as an inline warning.

### Input handling

The script is wrapped in an **IIFE** so its names don't leak into the
**global scope**, and it wires the UI with **event listeners** on the **DOM**.
Images arrive three ways: a hidden **file input** (opened by clicking the drop
zone, which is also **keyboard-accessible** via `tabindex` and Enter/Space),
**drag and drop** (listeners on the whole `document`, calling
`preventDefault()` so the browser doesn't navigate to the dropped file), and
the **Clipboard API** `paste` event (image files, or raw SVG markup pasted as
text — skipped when the paste target is a text field). A **FileReader** reads
the file either as text (for SVG, detected by **MIME type** or `.svg`
extension) or as a base64 **data URL** (for **raster** formats).

### Vector vs raster

SVG input is parsed with **DOMParser**, checked for a `parsererror` node, then
normalized: a missing **viewBox** is synthesized from `width`/`height`,
fixed dimensions are stripped so it scales to its container,
**preserveAspectRatio** is set to `xMidYMid meet`, and **shape-rendering:
crispEdges** is applied. `<script>` elements are removed as a basic
**sanitization** step before the node is **inlined** into the page — which
is why SVG QR codes stay **vector** (resolution-independent) all the way into
the PDF. Raster images (PNG/JPG/WebP) are shown in an `<img>` with
**image-rendering: pixelated / crisp-edges**, i.e. **nearest-neighbor
upscaling**, so QR **modules** stay hard-edged; the app reads the image's
**natural resolution** and warns below 600 px wide. For imported images the
app does not add a **quiet zone** — that's the source image's job; generated
codes include one.

### Layout, preview, and print

A single **render function**, `apply()`, maps every control to **CSS custom
properties** (`--qr-size`, `--top-size`, `--font`, `--margin`, `--gap`, …) on
the root element, and the page is laid out by **flexbox** (column direction,
`justify-content` for vertical alignment, `gap` between items). Sizes use
**physical CSS units** — inches and points — so the on-screen page *is* the
printed page. Page size is declared with an **@page rule**; for landscape the
script **rewrites that stylesheet's text at runtime**, because `@page` can't
read custom properties. The **live preview** is **scaled to fit** the viewport
with a **CSS transform** recomputed on `resize`. An **overflow check** uses
**getComputedStyle** to sum child heights, gaps, and padding and compares
that to the page height. A **print stylesheet** (`@media print`) hides the
controls, cancels the preview scaling, and sets **print-color-adjust:
exact** so colors aren't dropped.

### State

A **mode selector** switches between generate and import; each mode's
**panel** is shown or hidden, and the last imported image is held in a
**module-level variable** so switching modes loses nothing. Settings (now
including the mode and encoded text) and the last imported image are
**persisted** to **localStorage** as **JSON** under the keys `qrSignMaker` and
`qrSignMaker:img`, and **restored** on load — with a **migration** check so
settings saved before the generator existed still open in import mode. Every storage call is wrapped in **try/catch** and fails **silently**
(e.g. a large image that exceeds the **storage quota** just isn't saved). A
`DEFAULTS` map plus a `GROUPS` map drive the **per-section reset** buttons.

---

## 2. The plain-language version

This is a web page that makes printable signs with a QR code on them. The
entire program is one file (**single-file web app**): the page's structure
(**HTML markup**), its look (**CSS**), and its behavior (**JavaScript**) are
all written directly inside that one file (**inline**). The one piece of
someone else's code it uses, the QR encoder, is copied into the file rather
than downloaded (**vendored**), there's no step that converts the code before
it runs (**no build step**), and it never talks to the internet
once loaded (**offline**). It's hosted for free by GitHub as plain files
(**static site**, **GitHub Pages**), and publishing a new version is just
uploading the file to the main branch (**deployment**). To get a PDF, it asks
the browser's normal print feature to do the work (**print pipeline**).

To make a QR code, you type some text. The encoder (**qrcodegen**, a free
library under the **MIT license**, written in **TypeScript** and converted to
ordinary JavaScript, **transpiled**, before being pasted in) works out the
smallest square grid that can hold your text (**QR version**) using the
tightest packing available for those characters (**encoding mode**), and adds
the maximum amount of redundant data so a scuffed or partly covered sign still
scans (**error correction level H**). It hands back a grid of black-and-white
cells (**module matrix**), and the app draws each black cell as a tiny square
in one long drawing instruction (**SVG path**), leaving a four-cell blank
border (**quiet zone**). Spaces at the ends of your text are removed first
(**trimmed**). If the text is too long for the grid to hold at that safety
level (**capacity**), the library refuses with an error (**RangeError**), which
the app catches and shows as a red note instead of crashing.

All the code is wrapped in a sealed-off bubble so its internal names can't
clash with anything else on the page (**IIFE**, avoiding the **global
scope**). It works by saying "when this happens, run that" (**event
listeners**) against the browser's live model of the page (**DOM**). Instead of
typing text you can switch modes (**mode selector**) and give it an image by
clicking a box that opens a file chooser (**file input**
— and the box also works with the keyboard, **accessibility**), by dragging a
file onto the window (**drag and drop**), or by pasting (**Clipboard API**).
A built-in browser helper opens the file (**FileReader**), either as text if
it's an SVG (recognized by its declared file kind, the **MIME type**) or as a
long text-encoded copy of the picture (**data URL**) if it's a photo-style
image.

There are two kinds of images. Some are stored as drawing instructions —
"a black square here, another there" (**vector**, **SVG**) — and stay
perfectly sharp at any size. Others are stored as a grid of colored dots
(**raster**: PNG, JPG, WebP) and get blurry or blocky when enlarged. For SVGs,
the app reads the instructions (**DOMParser**), tidies them so the drawing
stretches to fill its box without distorting (**viewBox**,
**preserveAspectRatio**) and keeps edges hard (**crispEdges**), removes any
embedded program code as a basic safety measure (**sanitization**), and puts
the drawing directly into the page (**inlining**) — so it's still a drawing,
not a picture of one, when it reaches the PDF. For dot-grid images, it tells
the browser to enlarge by copying dots rather than smoothing them (**image-
rendering: pixelated**, **nearest-neighbor upscaling**), so the QR code's
little squares (**modules**) stay sharp, and it warns you if the original is
too small (**natural resolution**). For imported images it does *not* add the
blank border a scanner needs around the code (**quiet zone**); generated codes
already have one.

One function re-applies every setting whenever you touch a control (**render
function**). It does this by setting named values the styling can look up
(**CSS custom properties**), and the page arranges its three items in a
stretchy column (**flexbox**). Sizes are in real-world inches and points
(**physical CSS units**), so what you see is exactly what prints. A special
styling rule tells the printer the paper size (**@page rule**); because that
rule can't read the named values, the app rewrites the rule's text directly
when you switch to landscape. The on-screen page is shrunk to fit your window
(**CSS transform: scale**), and the app adds up the real heights of
everything on the page (**getComputedStyle**) to warn you if it won't fit
(**overflow check**). A separate set of styles applies only when printing
(**print stylesheet**, `@media print`): it hides the control panel, undoes the
shrinking, and tells the browser not to drop colors to save ink
(**print-color-adjust**).

Your settings, the text you typed, and your last imported image are
remembered in a small storage area the browser keeps for this site
(**localStorage**), saved as structured text (**JSON**) and reloaded next visit
(**persistence**). The last imported image is also kept in memory while you
flip between modes, so nothing is lost. Settings saved by the older version of
the app, which had no text mode, are detected and opened in image mode
(**migration**). If saving fails — say
the image is too big for the browser's storage limit (**storage quota**) —
the app just carries on quietly (**try/catch**, **fail silently**). Each panel
has its own reset button that puts only that panel back to its starting values
(**per-section reset**, driven by a **defaults map**).

---

## 3. Glossary — term → meaning → where it happened here

### Web platform & delivery

| Term | Plain meaning | In this project |
|---|---|---|
| **HTML / CSS / JavaScript** | page structure / page look / page behavior | all three are inline in the one `index.html` |
| **single-file web app** | a whole program in one HTML file | `index.html` is the entire app; LICENSE and README are the only other files |
| **inline** (CSS/JS) | code written inside the page instead of separate files | the `<style>` and `<script>` blocks in `index.html` |
| **vendoring** | copying a library's code into your project instead of downloading it at build or run time | Nayuki's `qrcodegen` is pasted into a `<script>` block with its license header |
| **no build step** | needs no conversion before running | "Edit `index.html` and reload" (README → Development) |
| **transpiling** | converting code from one language to another at the same level, e.g. TypeScript → JavaScript | the encoder ships as `qrcodegen.ts`; it was compiled once with `tsc` and the output pasted in |
| **offline** | works with no internet connection | no fetches, fonts, or CDNs; download the file and open it locally |
| **static site** | a website made of plain files, no server-side program | the published `index.html` |
| **GitHub Pages** | GitHub's free static-site hosting | https://pizzacatz.github.io/qr-sign-maker/ |
| **deployment** | putting a new version live | pushing to `main` updates GitHub Pages |
| **DOM** | the browser's live, editable model of the page | `$('page')`, `qr.appendChild(...)`, `textContent` updates |
| **event listener** | code that runs when something happens | `click`, `input`, `paste`, `drop`, `resize` handlers |
| **IIFE** | a function that runs immediately, walling off its variables | the whole script is `(function () { ... })();` |
| **global scope** | names visible to every script on the page | the IIFE keeps `apply`, `fit`, `KEY`, etc. out of it |
| **accessibility (keyboard)** | usable without a mouse | the drop zone has `tabindex="0"` and opens the chooser on Enter/Space |

### QR generation

| Term | Plain meaning | In this project |
|---|---|---|
| **qrcodegen** | Nayuki's QR encoder library (MIT) | `qrcodegen.QrCode.encodeText(text, qrcodegen.QrCode.Ecc.HIGH)` |
| **QR version** | symbol size, 1 (21×21) to 40 (177×177) | chosen automatically; shown in the info line as "version N" |
| **encoding mode** | how characters are packed: numeric, alphanumeric, byte, kanji | `encodeText` picks the tightest mode that fits the text |
| **error correction level** | how much redundant data is added (L 7%, M 15%, Q 25%, H 30%) | fixed at H; the README explains this is for scanning a printed sign from a distance |
| **capacity** | the most data a symbol can hold at a given level | about 1,270 bytes at level H; exceeding it throws `RangeError` |
| **module matrix** | the grid of dark/light cells | read with `code.size` and `code.getModule(x, y)` in `qrToSvg()` |
| **SVG path** | one element describing a shape as move/line commands | every dark module appends `M x y h1v1h-1z`; one `<path>` draws the whole code |
| **quiet zone** (generated) | the blank border a scanner needs | `QUIET = 4` modules added to the viewBox on every side |
| **trimming** | removing leading and trailing whitespace | `$('qrText').value.trim()` so a stray newline can't silently change the code |
| **mode selector** | a control that switches between two ways of working | `<select id="qrMode">`; `renderQr()` shows one panel and hides the other |
| **migration** (of stored data) | adapting data saved by an older version | `restore()` opens in import mode when an image exists but no `qrMode` was saved |

### Input & files

| Term | Plain meaning | In this project |
|---|---|---|
| **file input** | the browser's "choose a file" control | hidden `<input type="file" id="file">`, opened by clicking `#drop` |
| **drag and drop** | dropping a file onto the page | listeners on `document`; `preventDefault()` stops the browser opening the file itself; `#drop` gets the `.over` highlight |
| **Clipboard API / paste event** | reading what the user pasted | Ctrl+V loads an image file, or SVG markup pasted as text; ignored inside text boxes |
| **FileReader** | browser helper that reads a chosen file | `readAsText` for SVG, `readAsDataURL` for raster |
| **MIME type** | a label saying what kind of file something is | `image/svg+xml` (or a `.svg` name) routes to the SVG path; `image/*` to raster |
| **data URL** | a whole file encoded as a long text string | raster images are displayed and stored as `data:image/...;base64,...` |

### Images & print

| Term | Plain meaning | In this project |
|---|---|---|
| **vector** | an image stored as shapes; sharp at any size | SVG QR codes are inlined and stay vector in the PDF |
| **raster** | an image stored as a grid of dots | PNG / JPG / WebP input |
| **SVG** | the web's vector image format (it's text) | generated codes are built as SVG; for imports the README recommends SVG |
| **DOMParser** | turns text into a structured document | `setSvg()` parses the SVG and checks for `parsererror` |
| **viewBox** | an SVG's internal coordinate box, which lets it scale | synthesized from `width`/`height` if missing, then those are removed |
| **preserveAspectRatio** | how an SVG fits its box without distorting | set to `xMidYMid meet` (centered, fit inside) |
| **shape-rendering: crispEdges** | tell the renderer not to soften shape edges | applied to every loaded SVG |
| **image-rendering: pixelated / crisp-edges** | enlarge by copying dots, not blurring | CSS on `#qr > img` so PNG modules stay square |
| **nearest-neighbor upscaling** | enlarging by repeating each dot | what `pixelated` asks the browser to do |
| **natural resolution** | an image's real pixel size | `img.naturalWidth`; under 600 px triggers the "low resolution" warning |
| **module** | one black or white square of a QR code | the reason for crisp-edge rendering on both paths |
| **quiet zone** (imported) | blank margin a scanner needs around a QR code | the app does *not* add one to imported images (README → Tips) |
| **sanitization** | removing dangerous parts of untrusted input | `<script>` elements are stripped from SVGs. *Partial only* — e.g. `onload=` attributes are not removed |
| **inlining** (SVG) | placing the image's own markup into the page | `document.importNode(svg, true)` into `#qr` |
| **print pipeline / Save as PDF** | the browser's built-in print-to-PDF | the **Save as PDF / Print…** button just calls `window.print()` |

### Layout & styling

| Term | Plain meaning | In this project |
|---|---|---|
| **CSS custom properties** | named values styles can look up and code can change | `--qr-size`, `--top-size`, `--font`, `--margin`, `--gap`, `--pw`/`--ph` |
| **flexbox** | CSS layout that arranges items in a flexible row or column | `#page` is a column; `.top`/`.bottom` classes change `justify-content` |
| **physical CSS units** | real-world sizes like inches and points | page is `8.5in × 11in`; text sizes in `pt`; margins and gaps in `in` |
| **@page rule** | CSS that sets printed paper size and margins | `<style id="pageSizeCss">`, rewritten for landscape since `@page` can't use custom properties |
| **print stylesheet (@media print)** | styles used only when printing | hides `#controls`, removes the preview scale and shadow |
| **print-color-adjust: exact** | ask the browser not to drop colors when printing | set on everything so colored text prints as chosen |
| **CSS transform: scale** | shrink or grow something visually | `fit()` scales `#page-wrap` so the whole page fits the window |
| **getComputedStyle** | read the final, actual styles of an element | `checkOverflow()` reads padding and gap to add up the page's content height |
| **overflow check** | detecting content that won't fit | shows the red "Content is taller than the page" warning |

### Data & state

| Term | Plain meaning | In this project |
|---|---|---|
| **render function** | one function that redraws everything from the current settings | `apply()`, called on every control's `input` event |
| **localStorage** | small per-site storage kept by the browser | keys `qrSignMaker` (settings, mode, and text) and `qrSignMaker:img` (last imported image) |
| **JSON** | a standard text format for structured data | settings and image are `JSON.stringify`'d before saving |
| **persistence / restore** | remembering state across visits | `save()` / `saveImage()` write; `restore()` reads on load. Clearing site data resets it |
| **storage quota** | the size limit on browser storage | a large raster data URL can exceed it — *inferred*, not observed |
| **try/catch, fail silently** | catch an error and carry on without complaining | every storage call is `try { ... } catch (_) {}` |
| **defaults map / per-section reset** | a table of starting values, reset one group at a time | `DEFAULTS` + `GROUPS` (qr · top · bottom · layout) behind each panel's **reset** button |

### Process & engineering practice

| Term | Plain meaning | In this project |
|---|---|---|
| **MIT License** | a short, permissive open-source license | `LICENSE`; the vendored encoder is MIT too, which is why it could be pasted in without changing the project's license |
| **license compatibility** | whether two licenses allow combining code | the first candidate (mini-qr) was GPL-3.0, which would have forced the whole project to GPL; the plain-modules requirement made it unnecessary |
| **project status: complete** | finished; no new features planned | declared at the top of the README, reaffirmed after adding generation |
