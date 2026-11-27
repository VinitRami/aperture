# Vinit Rami Photographs

**Live:** https://aperture.vinitrami.co.in

A photography portfolio built around a 3D camera. It is its own site, separate
from the main portfolio at https://www.vinitrami.co.in: static files, no build
step and nothing loaded from other origins, deployed by Vercel from `main`
(every other branch gets a preview deployment).

## What is on the page

- **The camera.** A black Nikon D5200 modelled in Blender, with baked ambient
  occlusion and a leatherette normal map. Drag to turn it, click to pick it up,
  turn the mode dial, and the photos for that mode play back on its screen.
- **The lens.** Click the lens to zoom (with motor sound). Click through the
  glass to open the PDF portfolio, page by page.
- **Flash and record.** The shutter pops the flash, the red button records.
- **Photo details.** Each frame shows its camera, settings, histogram and colour
  palette, read from the image in the browser.
- **About.** The "Echo Face" spread: the portrait scans in behind a blue line.
- **Darkroom** (`#darkroom`). A desktop monitor runs the edit of the night photo.
  Drag the before/after line or any of the ten Develop sliders; the grade is
  computed per pixel and the histogram shows exactly what is on screen.

Every sound is synthesised with the Web Audio API; there are no audio files.
Without WebGL the page shows a still render of the same camera and keeps the
photo playback.

## Structure

```
.
├── index.html              # markup and SEO tags
├── assets/
│   ├── site.css            # all styles, plus the @font-face rules
│   ├── app.js              # all behaviour, photo data and captions
│   ├── fonts/              # Anton, Geist, Geist Mono, Michroma (WOFF2)
│   ├── LICENSE.md          # font and three.js licences
│   └── vendor/three/       # three.js r147 and the five example modules it uses (MIT)
├── img/                    # photos: <id>.webp (full) and <id>-s.webp (screen size)
├── pdf/                    # the PDF portfolio, one WebP per page
├── model/                  # d5200.glb plus its baked AO and normal maps
├── tools/blender_d5200.py  # the Blender (bpy 4.2) script that builds the model
├── 404.html              # the not-found page (assets/404.css)
├── og.jpg, favicon.svg, favicon.ico
├── vercel.json             # security headers and caching
├── robots.txt, sitemap.xml
├── .well-known/security.txt
├── LICENSE                 # code MIT; photographs all rights reserved
└── README.md
```

## Security

The page loads nothing from other origins, so it runs under a strict Content Security Policy (`vercel.json`, repeated in a
`<meta>` tag for hosts that cannot set headers): same-origin scripts, styles, fonts, images and fetches only.
There is no inline script, no inline style and no `style` attribute; values
the markup needs are `data-*` attributes mapped to CSS custom properties at
the bottom of `site.css`, and scripts build elements with DOM calls rather
than HTML strings.

## Analytics

Vercel Web Analytics counts page views. Its script is served from the site's own
origin (`/_vercel/insights/script.js`), so it fits the CSP above; it sets no
cookies and stores no personal data. Turn it on in the Vercel project under
**Analytics → Enable**; until then the script request returns 404 and nothing is
counted. Locally the request also 404s, which is expected.

Files in `model/` and `img/` are cached for a week, so the build adds a content
hash to the URLs of the ones that change (`d5200.glb?v=…`, `echo-face.webp?v=…`).

## Updating content

- **Photos:** add `img/<id>.webp` and `img/<id>-s.webp`, then add the photo to
  `P` (title, camera, file name, settings) and `ALT` in `assets/app.js`, and to
  a mode's `set` in `MODES`.
- **The real edit for the darkroom:** add `img/night-building-edit.webp` and set
  `HAS_EDIT = true` in `assets/app.js`.
- **About portrait:** `img/echo-face.webp` is a transparent WebP, so it sits on
  the grey studio background with no blend mode.
- **Portfolio pages:** `pdf/page-01.webp` to `pdf/page-12.webp`.
- **Model:** run `blender -b -P tools/blender_d5200.py -- out/` (or the script
  with the `bpy` module) and copy the GLB and the `*_ao.webp` / `*_nrm.webp`
  maps into `model/`.

## Deploy

The repository lives at `VinitRami/aperture`, in the same GitHub account that
the Vercel account is linked to (the main site's repository is there too).
Vercel only sees repositories in that account, so keep it there.

- **Vercel project** `aperture`: every push to `main` deploys to production,
  other branches get preview URLs. No build step: the framework preset is
  "Other" and the files are served as they are. `vercel.json` adds the
  security headers and the cache rules.
- **Domain** `aperture.vinitrami.co.in`, added under the project's
  Settings → Domains.
- **DNS** for `vinitrami.co.in` is managed at Cloudflare (the domain is
  registered at GoDaddy, whose nameservers point to Cloudflare; leave those
  alone). The record is `CNAME aperture → cname.vercel-dns.com` with the
  proxy off ("DNS only"), so Vercel serves the certificate and the headers.

## Run locally

```bash
python3 -m http.server 8123
# open http://localhost:8123
```
