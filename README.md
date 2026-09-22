# QR Sign Maker

A single-file, fully offline web app for printing a large QR code sign on Letter (8.5 × 11 in) paper.
Type the text or URL to encode (or load an existing QR code image), add a title and caption, and save a print-ready PDF straight from the browser.

**Live:** https://pizzacatz.github.io/qr-sign-maker/

> **Status: complete.** This project does what it set out to do and is not under active development. Bug reports are welcome but may not be addressed.

No build step, no network access. Everything is in `index.html`, including one vendored MIT library (Nayuki's [QR Code generator](https://www.nayuki.io/page/qr-code-generator-library)) that does the encoding.

## Features

- **Generate** a QR code from any text or URL, live as you type. Plain black-and-white modules, error correction level H (the highest), and a 4-module quiet zone. No styling options, by design: plain codes scan best from a distance.
- **Import** an SVG, PNG, JPG, or WebP QR code instead, by clicking, drag-and-drop, or pasting (Ctrl+V). SVG markup pasted as text also works.
- **Vector output** — generated codes and imported SVGs stay crisp at any size in the PDF. Raster images are scaled with pixel-sharp rendering, and you get a warning if the source is low resolution.
- **Top and bottom text**, each optional, with its own size, bold toggle, and color.
- **Layout controls:** QR size, font, portrait or landscape orientation, vertical alignment, page margin, and spacing between items.
- **Live preview** of the exact page, scaled to fit your window.
- **Overflow warning** if the content would run off the page.
- **Per-section reset** buttons in each panel header.
- **Remembers your settings**, the encoded text, and the last imported image in the browser between visits.

## Usage

1. Open the [live page](https://pizzacatz.github.io/qr-sign-maker/) or download `index.html` and open it locally.
2. Type the text or URL to encode, or switch to **Import an image** and load an existing QR code.
3. Edit the top and bottom text, or clear either one to hide it.
4. Adjust size, font, orientation, and margins to taste.
5. Click **Save as PDF / Print…** and in the browser dialog choose:
   - Destination: **Save as PDF**
   - Paper size: **Letter**
   - Margins: **None**
   - **Headers and footers: off**

The page already declares Letter size with zero margins, so Chrome and Edge usually pick these up automatically.

## Tips

- If you import a code rather than generating one, use **SVG** wherever possible for the sharpest print.
- Generated codes include a white quiet zone. Imported images need their own; the app does not add one.
- Text longer than about 1,270 bytes cannot be encoded at error correction H. Shorten it, or use a URL shortener.
- After printing, scan the sign from a few feet away to confirm it reads.
- Clearing browser site data resets all saved settings and the stored image.

## Development

There is nothing to install. Edit `index.html` and reload. Pushing to `main` deploys to GitHub Pages.
The QR encoder is Nayuki's `qrcodegen.ts` compiled to plain JavaScript and pasted in unmodified with its license header.
The project is complete, so feel free to fork it if you want to take it further.

## License

MIT — see [LICENSE](LICENSE). The bundled QR encoder is © Project Nayuki, also MIT.
