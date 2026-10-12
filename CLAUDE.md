# KH Pathways website rebuild

Rebuild khpathways.com (currently on Wix, $25/month) as a small static site hosted free on Cloudflare Pages. This file is the brief. The page copy is in CONTENT.md. Read both fully before writing any code.

## About the site

- Owner: Kyle Huber. Business: KH Pathways LLC, a financial confidence coaching practice in Seattle, WA.
- The site is a credibility piece. It gets a few visitors a month. All booking happens through a Google Calendar appointment link (in CONTENT.md). There is nothing to sell, no logins, and no payments.
- Tone: calm, trustworthy, plain-spoken. Not flashy, not "finance bro." The brand is a deep teal and white mountain-and-winding-path logo.
- Priorities, in order: looks credible, loads fast, easy to edit later without tooling, nothing to maintain.

## Decisions already made

- **Plain HTML and CSS.** No framework, no build step, no npm dependencies. Add JavaScript only if something truly needs it (nothing here should).
- **Hosting is Cloudflare Pages (free plan).** Do not suggest Vercel Hobby or GitHub Pages for this site. Vercel Hobby is non-commercial only, and GitHub Pages' terms exclude running an online business. This is a business site.
- **Keep the existing URLs exactly:** `/`, `/financial-confidence`, `/services`, `/about`, `/disclaimer`. This preserves search rankings. Use `folder/index.html` so the extensionless URLs work.
- **No contact form.** Replace it with a mailto link and the booking button (details in CONTENT.md).
- **No analytics, cookies, or third-party scripts.** That means no cookie banner is needed. Use a system font stack or one self-hosted font. No Google Fonts or other external requests.
- **Do not invent anything.** No testimonials, statistics, credentials, prices, or marketing claims beyond what is in CONTENT.md. Do not reword the copy. The only deliberate copy changes are marked **[CHANGE]** in CONTENT.md.
- The legal disclaimer is verbatim apart from removing one duplicated section (marked in CONTENT.md).

## Repo layout

```
CLAUDE.md, CONTENT.md   this brief and the page copy
README.md               how to edit, preview locally, and deploy (write this)
DEPLOY.md               Cloudflare setup and DNS cutover (write this, see Deployment)
source-assets/          original images, never deployed, never edited in place
public/                 the deployed site (Cloudflare output directory, no build command)
  index.html
  financial-confidence/index.html
  services/index.html
  about/index.html
  disclaimer/index.html
  404.html
  css/style.css
  img/                  web-optimized images only
  favicon.svg and friends
  robots.txt, sitemap.xml
```

The repo already contains the image files. Move them into `source-assets/` with `git mv`. Never edit originals; always export copies into `public/img/`.

## Images: what each one is and what to do

The files may have generic names, so identify them by content.

1. **Wide logo, about 500px wide, white background.** "KH PATHWAYS LLC" with the tagline "GUIDING YOUR SUCCESS PATH" under it, and the icon at left. Source for the logo work below.
2. **Same wide logo on a pale grey-blue background.** Slightly different crop. Use it only as a second reference.
3. **Icon only, about 190px.** Teal circle containing mountains, a winding path, two trees, and a small hiker figure.
4. **Portrait in a black and white photo, dark crewneck sweatshirt.** Use it **only after cropping**. There is another company's logo (Wrapbook) printed on the chest. Crop to head and shoulders, roughly the top 60 to 65 percent of the frame. The logo sits in the lower third and the V-stitch below the collar is a good landmark. Crop so the bottom edge falls just below the collar. Then **look at the final image and confirm no part of the logo is visible.** If Kyle supplies a retouched version later, swap it in. Use it as the **home page** portrait.
5. **Portrait of Kyle outdoors against a wooden fence, striped short-sleeve shirt.** Use it as the **About page** photo. At about 890px wide it is fine for display up to roughly 440 CSS pixels (2x).
6. **Selfie in a glass-domed building with a green wall, lanyard, laptop.** Do **not** use it. It is a casual selfie with a conference lanyard.
7. **Cartoon finance doodle (stock art).** Do **not** use it. It is busy, multicolored, and clashes with the brand. Do not replace it with AI-generated art. In place of it, a simple on-brand graphic is welcome if the layout needs one. For example, a quiet SVG motif drawn from the logo's winding path in the brand teal. If a section looks fine without an image, leave it out.

The Services page gets no photo. Whitespace is fine.

**Image processing rules**

- Export web versions at no more than 2x their displayed width. Aim for under about 200 KB each. WebP with a JPEG fallback is fine, or just well-compressed JPEG.
- **Strip all EXIF metadata** from every published photo. These are phone photos and may contain GPS location. Verify with `exiftool` that nothing remains.
- Every `<img>` gets width and height attributes (prevents layout shift) and meaningful `alt` text. Decorative graphics get `alt=""`.

## Logo: recreate it as SVG

The existing logo files are too small to look sharp on high-density screens. Rebuild them as vectors.

1. **Icon.** It is a flat, single-color teal mark, so it should vectorize cleanly. Take the largest, clearest source, upscale it with a good resampling filter, threshold it to black and white, and trace it with `potrace` (or `vtracer`; install what you need with pip or a package manager). Output a single-fill SVG. Clean up stray nodes and rounded-corner artifacts. If tracing from the 190px file loses detail (the hiker figure and the trees are the risk), try the icon cropped out of the wide logo instead, which may have more pixels. If tracing is not good enough, hand-build the SVG from primitives (ring, mountain polygons, bezier path, trees, figure).
2. **Wordmark.** "KH PATHWAYS LLC" in a light-weight geometric sans-serif, all caps, wide letter spacing. The tagline sits underneath in smaller caps with even wider spacing, spanning the same width. Use an open-license font that closely matches (Montserrat, Jost, or similar). Convert the text to outlines in the final SVG so it does not depend on a font being installed.
3. **Files to produce** in `public/img/`:
   - `logo-icon.svg`: the icon alone
   - `logo-header.svg`: icon plus "KH PATHWAYS LLC", no tagline (the tagline is unreadable at header size)
   - `logo-full.svg`: icon, name, and tagline (footer, social image)
   - Also derive: `favicon.svg`, a 180x180 `apple-touch-icon.png`, and a 1200x630 `og-image.png` (full logo centered on a light background) for link previews.
4. **Verify.** Render each SVG to PNG at several sizes and compare side by side with the originals. Fix visible differences in shape, proportion, or spacing. Sample the exact teal from the original logo and use that same value as the site's brand color. Define it once as a CSS variable.

## Design direction

- Clean, airy, a lot of whitespace. One brand color (the logo teal) plus a near-black text color and white or off-white backgrounds. A soft tint of the teal is fine for section backgrounds.
- Header on every page: logo at left, nav at right (Financial Confidence, Work With Me, About). On mobile the nav should wrap or stack cleanly with no JavaScript.
- Readable body text: 17 to 19px base, line length around 65 characters, generous line height. Good contrast (WCAG AA at least) for the teal on white and for button text.
- The Book a Free Consultation button is the primary call to action. Make it clearly the most prominent button, and use the same style everywhere. Open the booking link in a new tab with `rel="noopener"`.
- Home: headline, intro, the black and white portrait beside it (stacked on mobile), the "Ready to Take Control" section, then "Get in Touch" with the email link and the button.
- Financial Confidence: the four questions work well as a simple list of headings with short paragraphs, or a quiet two-column grid. Do not use stock imagery. Three "For ..." items work as three simple cards.
- Services: the three packages as three cards or three clearly separated blocks. Make the numbering visible.
- About: the fence photo beside the text (stacked on mobile).
- Footer on every page, as listed in CONTENT.md.

## Technical requirements

- Semantic HTML (`header`, `nav`, `main`, `footer`, one `h1` per page). `<html lang="en">`. Skip-to-content link. Visible focus states.
- Responsive from 320px up. Test at mobile, tablet, and desktop widths.
- Per page: `<title>` exactly as given in CONTENT.md, a concise meta description (write it from that page's copy, 150 characters or fewer), a canonical URL on `https://www.khpathways.com`, and Open Graph and Twitter card tags using `og-image.png`.
- `robots.txt` and `sitemap.xml` listing the five URLs.
- A simple `404.html` in the site style with a link home.
- Add a `_headers` file in `public/` with sensible security headers (`X-Content-Type-Options`, `Referrer-Policy`, `X-Frame-Options` or a frame-ancestors policy). Keep it simple.
- No external requests at runtime. Everything is self-hosted.

## Deployment

Do not do the Cloudflare dashboard work yourself. Write `DEPLOY.md` as a short, accurate guide Kyle can follow. Before writing it, check Cloudflare's current documentation (the dashboard and product naming change; confirm whether a static folder is deployed through Pages or through Workers static assets, and use whichever the current docs recommend). It should cover:

1. Connecting this GitHub repo to Cloudflare, with no build command and `public` as the output directory.
2. Testing on the temporary `*.pages.dev` (or equivalent) URL while the Wix site stays live.
3. Cutting over the custom domain. The domain `khpathways.com` is registered at Wix. The site's canonical host is `www`. Check how Cloudflare handles a `www` CNAME versus the bare apex domain when DNS is not on Cloudflare, and whether the simplest route is moving DNS (nameservers) to Cloudflare, and say which you recommend and why. Include setting up the apex-to-www redirect.
4. Sequencing: new site live and verified first, then switch DNS, then cancel the Wix plan. Optionally transfer the domain registration to Cloudflare Registrar afterward. Wix reports a 60-day ICANN lock after registration, transfer, or contact-info change, and transferring away disconnects the Wix site, so do the domain transfer last and do not change the domain's contact details beforehand.
5. After launch: submit the sitemap in Google Search Console if Kyle has it set up.

## How to work

- Start with the logo SVGs, then build the pages. Show Kyle the local preview (`python3 -m http.server` from `public/`) and get a look before polishing.
- Commit in small, descriptive steps.
- When something is ambiguous or a decision is Kyle's (which photo goes where, retouching, brand tweaks), ask. Don't guess. The defaults in this brief are mine and Kyle may override them.
- Don't add features, pages, or dependencies that are not in this brief.

## Done means

- [ ] All five pages match CONTENT.md word for word (apart from the marked [CHANGE] items).
- [ ] Every link works, including the booking link, the mailto links, and the nav.
- [ ] No Wrapbook logo visible anywhere. No EXIF data on any published image.
- [ ] Logo SVGs render correctly at 24px, 48px, and 400px wide. Favicon and OG image exist.
- [ ] Mobile and desktop layouts both checked. Text contrast and alt text checked. Lighthouse accessibility and performance scores are in the high 90s.
- [ ] `source-assets/` is outside `public/`, so originals are not deployed.
- [ ] README.md explains how to edit a page, preview locally, and deploy.
- [ ] DEPLOY.md is written and its steps were checked against current Cloudflare docs.
