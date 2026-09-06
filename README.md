# QR Sign Maker

A single-file, fully offline web app for printing a large QR code sign on Letter (8.5 × 11 in) paper.
Load a QR code image, add a title and caption, and save a print-ready PDF straight from the browser.

**Live:** https://pizzacatz.github.io/qr-sign-maker/

> **Status: complete.** This project is finished and not under active development. It works as-is and will not receive new features. Bug reports are welcome but may not be addressed.

No build step, no dependencies, no network access. Everything is in `index.html`.

## Features

- **Import** an SVG, PNG, JPG, or WebP QR code by clicking, drag-and-drop, or pasting (Ctrl+V). SVG markup pasted as text also works.
- **Vector output** — SVG QR codes are inlined and stay crisp at any size in the PDF. Raster images are scaled with pixel-sharp rendering, and you get a warning if the source is low resolution.
- **Top and bottom text**, each optional, with its own size, bold toggle, and color.
- **Layout controls:** QR size, font, portrait or landscape orientation, vertical alignment, page margin, and spacing between items.
- **Live preview** of the exact page, scaled to fit your window.
- **Overflow warning** if the content would run off the page.
- **Per-section reset** buttons in each panel header.
- **Remembers your settings** and the last loaded image in the browser between visits.

## Usage

1. Open the [live page](https://pizzacatz.github.io/qr-sign-maker/) or download `index.html` and open it locally.
2. Load your QR code image.
3. Edit the top and bottom text, or clear either one to hide it.
4. Adjust size, font, orientation, and margins to taste.
5. Click **Save as PDF / Print…** and in the browser dialog choose:
   - Destination: **Save as PDF**
   - Paper size: **Letter**
   - Margins: **None**
   - **Headers and footers: off**

The page already declares Letter size with zero margins, so Chrome and Edge usually pick these up automatically.

## Tips

- Generate your QR code as **SVG** wherever possible for the sharpest print.
- Keep a white quiet zone around the QR modules. The app does not add one.
- After printing, scan the sign from a few feet away to confirm it reads.
- Clearing browser site data resets all saved settings and the stored image.

## Development

There is nothing to install. Edit `index.html` and reload. Pushing to `main` deploys to GitHub Pages.
The project is complete, so feel free to fork it if you want to take it further.

## License

MIT — see [LICENSE](LICENSE).
