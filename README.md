# KH Pathways website

Static site for khpathways.com. Plain HTML and CSS: no framework, no build step, no dependencies. The deployed site is the `public/` folder.

```
CLAUDE.md, CONTENT.md   the brief and the page copy (CONTENT.md is the source of truth for wording)
README.md               this file
DEPLOY.md               Cloudflare setup and DNS cutover (checked against Cloudflare's docs)
wrangler.jsonc          tells Cloudflare to serve public/ (no Worker code)
GOOGLE-BUSINESS-PROFILE.md   Google profile and testimonial steps
Kai-To-Do.md            open items for the owner
source-assets/          original photos and logos. Never deployed, never edited in place.
public/                 the website
  index.html, financial-confidence/, services/, about/, disclaimer/   one folder per page, each with index.html
  404.html, robots.txt, sitemap.xml, _headers, favicon.svg
  css/style.css         all styling
  img/                  web-ready images and the SVG logos
```

## Preview locally

```
cd public
python3 -m http.server 8000
```

Open http://localhost:8000. If a change doesn't show up, hard-refresh (Ctrl+Shift+R, or Cmd+Shift+R on Mac). Serve from inside `public/` so links like `/services` and `/css/style.css` resolve.

## Edit a page

1. Open the page's `index.html` in any text editor (for example `public/about/index.html`).
2. Change the text between the tags. Keep the tags and class names.
3. Preview, then commit and push. Cloudflare redeploys automatically once it is connected (see DEPLOY.md).

Things to know:
- The **header and footer are repeated in every page**. If you change a nav link, the footer, or the booking link, change it in all five pages plus `404.html`. The booking link `https://calendar.app.google/NVaX8nhezWKmJPHC7` appears in several places; search for it.
- **Page titles and meta descriptions** are in each page's `<head>`. Keep descriptions to about 150 characters.
- **Brand color** is a single CSS variable, `--teal`, at the top of `public/css/style.css`.
- If you add a page, create `public/<name>/index.html`, add it to the nav, and add it to `public/sitemap.xml`.
- Wording changes should also go into `CONTENT.md` so it stays accurate.

## Images

- Put originals in `source-assets/`. Export web copies to `public/img/`, at no more than 2x the displayed size.
- **Strip metadata** from any photo before publishing (phone photos can contain GPS location):
  `exiftool -all= -overwrite_original public/img/photo.jpg`
- Every `<img>` needs `width`, `height` and an `alt`.
- Don't publish the original sweatshirt photo. It has another company's logo on it. The home page uses a cropped version.

## Logos

`public/img/logo-header.svg` (header), `logo-full.svg` (with tagline), `logo-icon.svg` and `favicon.svg` are vector files. The text in them is outlined, so no font is needed. They were traced and rebuilt from the original small logo files in `source-assets/`.

## Deploy

See `DEPLOY.md`. In short: a Cloudflare Worker with static assets (Cloudflare's current recommendation for new projects), free plan, no build command, serving `public/` as set in `wrangler.jsonc`. The canonical host is `https://www.khpathways.com`.
