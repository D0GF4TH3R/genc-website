# CLAUDE.md — Genc Website

> This file is read automatically by Claude Code and @claude on GitHub before every change.
> It is the single source of truth for this project.

## Project

**Repo:** https://github.com/D0GF4TH3R/genc-website
**Live:** https://gkelite.training/
**Developer:** Dominick Amp (@D0GF4TH3R)
**Client:** Genc Krasniqi

Premium personal training website for **Genc Krasniqi** — former Swiss professional footballer (Grasshoppers Club Zürich, FC Rapperswil-Jona), certified physiotherapist (Dipl. Physiotherapeut FH). Offers in-home personal training, physiotherapy, sports massage, and football coaching to high-net-worth individuals and expats in Dubai.

**Positioning:** Swiss Quality & Precision. Apple-like design language — ultra-minimalist, white background, clean typography. Think Steve Jobs would approve.

## Architecture

**Everything is one file:** `index.html` (~1350 lines, ~75 KB). No frameworks, no build tools, no dependencies. Pure HTML/CSS/JS.

**Hosted on Cloudflare Pages** at `gkelite.training`. Deploy = push to main. Auto-builds in 1–2 minutes.

```
genc-website/
├── index.html          ← THE file. All CSS, JS, HTML, i18n, legal pages.
├── GK_Logo.png         ← 852×178, PNG wordmark "⊙ ELITE TRAINING" (black on alpha), 15 KB
├── about-genc.webp     ← 1232×2000 About-section graphic (photo + stat grid); also Schema image
├── og-image.jpg        ← 1200×630 link-preview / social share card (Genc + logo + tagline)
├── favicon.ico         ← multi-res 16/32/48, GK monogram white on green tile
├── favicon-32x32.png / favicon-16x16.png  ← PNG favicons
├── apple-touch-icon.png ← 180×180 iOS home-screen icon (green square)
├── icon-192.png / icon-512.png ← PWA / Android icons
├── site.webmanifest    ← PWA manifest (theme #2D6A4F)
├── robots.txt          ← allows all, points to sitemap
├── sitemap.xml         ← single URL (gkelite.training), update lastmod on changes
├── llms.txt            ← AI-crawler summary of services + facts
├── CLAUDE.md           ← You are here
├── frames/
│   ├── hero/           121 WebP frames (scroll-driven animation)
│   ├── training/       121 WebP frames
│   ├── virtual/        121 WebP frames
│   ├── physio/         121 WebP frames
│   ├── massage/        121 WebP frames
│   ├── soccer/         121 WebP frames
│   ├── end/            121 WebP frames (CTA / closer)
│   └── easteregg/      169 WebP frames
└── .gitignore
```

**Do NOT create new files** for site content. All site changes go into `index.html` (the SEO support files robots.txt / sitemap.xml / llms.txt already exist and may be edited). The site is intentionally single-file.

## Core Feature: Scroll-Driven Frame Animation

The site uses an Apple-style scroll-driven canvas animation. **NOT** a `<video>` element — it's 847 individual WebP frames (7 sections × 121) drawn on a `<canvas>`, mapped to scroll position.

### Two-Layer System:
1. **Canvas layer** (z-index 5, fixed to viewport) — Shows video frames that change as user scrolls. Never moves.
2. **Text layer** (z-index 20, positioned above canvas) — Headlines + body text fade in/out over the canvas.
3. **White mask** (z-index 10) — Covers top portion of canvas so text is readable.

### Boot Sequence:
1. Loading screen with GK logo + green progress bar
2. Hero frames (121) preload with percentage counter
3. Hero auto-plays at 24fps (Genc turns to camera)
4. Scroll unlocked, chevron indicator appears
5. Remaining 6 sections background-load

### Text Delay System:
Text transitions are delayed by 50% (`TD = 0.50`). Old text lingers into the next section's scroll range before fading out, and new text fades in after 50%. This prevents text/frame mismatch. **Do not change TD without understanding the cascade effect on all animations.**

### Gradient Sweep (Apple Intelligence style):
Each headline starts black, then a multi-color gradient sweeps left-to-right through the text. Implemented via `background-clip: text` + animated `background-position`. Six custom gradients:
- Hero: Apple Vibe (blue → purple → pink → orange)
- Training: Cyberpunk (cyan → blue → pink → coral)
- Virtual: Digital Glow (indigo → violet → purple → cyan)
- Physio: Aurora (green → emerald → teal → violet)
- Massage: Sunset (yellow → pink → magenta → dark violet)
- Soccer: Berry (mint → light blue → pink → orange-red)

The sweep triggers when a headline becomes fully visible and resets when the section is off-screen.

## Sections

| # | ID | EN Headline | DE Headline | Color |
|---|-----|------------|-------------|-------|
| 0 | `s-hero` | Your Body. Your Gym. | Dein Körper. Dein Gym. | `#111111` |
| 1 | `s-training` | Your gym comes to you. | Dein Gym kommt zu dir. | `#2D6A4F` |
| 2 | `s-virtual` | Your session. Your screen. | Deine Session. Dein Screen. | `#4F46E5` |
| 3 | `s-physio` | Fix it. Move better. | Lösen. Bewegen. Stärken. | `#007AFF` |
| 4 | `s-massage` | Recovery is the strategy. | Recovery ist die Strategie. | `#D32F2F` |
| 5 | `s-soccer` | Trained by a pro. | Trainiert vom Profi. | `#E8740C` |
| 6 | `s-closer` | Ready when you are. | Bereit, wenn du es bist. | `#111111` |

Frame folder → section mapping: `hero`→0, `training`→1, `virtual`→2, `physio`→3, `massage`→4, `soccer`→5, `end`→6 (CTA/closer). Source videos live in `Videos/` (`1 - Hero.mp4` … `7 - CTA.mp4`), numbered in display order.

## Design Tokens

```css
--green:  #2D6A4F   /* Training, CTAs, WhatsApp */
--blue:   #007AFF   /* Physio */
--red:    #D32F2F   /* Massage */
--orange: #E8740C   /* Soccer */
--black:  #111111   /* Hero, Closer, body text */
--grey:   #6B7280   /* Body/sub text */
--white:  #FFFFFF   /* Background */
```

**Font:** `-apple-system, BlinkMacSystemFont, 'SF Pro Display', 'Helvetica Neue', sans-serif`
**Headlines:** `font-weight: 600`, `clamp(2rem, 6.5vw, 2.75rem)`
**Body:** `clamp(0.9rem, 2.5vw, 1.05rem)`

## i18n System

**All text is trilingual (EN + DE + AR).** Arabic is fully RTL — `setLang('ar')` sets `<html dir="rtl">` and dedicated `html[dir="rtl"]` CSS rules mirror the layout. The system uses `data-i18n="key"` for plain text and `data-i18n-html="key"` for HTML (with `<br>` tags).

### CRITICAL: When changing ANY text, you MUST update ALL THREE languages (en, de, ar).

Translations live in the `T` object in JS:
```javascript
const T = {
  en: { hero_headline: 'Your Body.<br>Your Gym.', ... },
  de: { hero_headline: 'Dein Körper.<br>Dein Gym.', ... },
  ar: { hero_headline: '...', ... }
};
```

**Language detection:** `localStorage('genc-lang')` → `navigator.language` (incl. `ar*`) → default English.

**WhatsApp messages are also translated:**
- EN: `Hi Genc, I'd like to learn more about your services.`
- DE: `Hallo Genc, ich möchte gerne mehr über deine Services erfahren.`

When updating WhatsApp message text, remember it's URL-encoded in the `wa_text` key.

## Legal Pages

Impressum and Datenschutz/Privacy Policy are **fullscreen modals** rendered from the `LEGAL` object in JS. They have EN + DE + AR versions.

### Current state (as of March 2026):
- **Firma aufgelöst** (dissolved end of January 2026) — NO Handelsregister entry in Impressum
- **Standort:** Dubai, United Arab Emirates / Dubai, Vereinigte Arabische Emirate
- **Email:** genc@gkelite.training
- **WhatsApp:** +41 76 568 77 07
- **Hosting:** Cloudflare Pages (named in Datenschutz/Privacy hosting clause)
- **No cookies, no tracking, no analytics** — Datenschutz is very simple

When editing legal text, update ALL THREE (`en`, `de`, `ar`) versions in the `LEGAL` object.

## Responsive Layout

**Mobile-first.** Three breakpoints. Desktop uses a centered portrait column.

| Breakpoint | Canvas Size | Column | Spacers |
|-----------|-------------|--------|---------|
| Mobile (<769px) | 100% width, 64svh | Full width | Hero 50vh, sections 150vh |
| Tablet (769–1024px) | 65% width, 72svh | Full width | Hero 60vh, sections 170vh |
| Desktop (1025px+) | 100% of column, 80svh | 620px centered | Hero 70vh, sections 180vh |
| Large Desktop (1440px+) | Same | 680px centered | Same |

### CRITICAL: Use `svh` not `dvh` or `vh`
Safari iOS dynamically changes `dvh` when the toolbar animates — causes canvas stretching. `svh` (small viewport height) never changes. This was a bug we fixed. **Do not use `dvh` anywhere.**

## UI Components

- **Nav:** GK_Logo.png (34px mobile, 38px desktop) left, glassmorphism hamburger right. Fixed, z-index 200.
- **Hamburger menu:** Fullscreen glassmorphism overlay. Links jump to END of each section's spacer (`offsetTop + offsetHeight - 2`), not the beginning — this ensures correct text/frame alignment due to the text delay system.
- **WhatsApp button:** Fixed bottom-right, green (#2D6A4F), links to `wa.me/41765687707`.
- **Scroll indicator:** Chevron at bottom, appears 600ms after hero autoplay, disappears on first scroll.
- **Loading screen:** GK logo + thin green progress bar + percentage.

## SEO

Schema.org `LocalBusiness` JSON-LD is in the `<head>`. Contains all 5 services, Dubai location (with geo-coordinates + Wikidata `areaServed`), Genc as founder with professional bio, opening hours 06–22. `image` is an array (real photo first, then logo).

**Other SEO assets in the `<head>` / root:**
- Title + meta description are Dubai/service keyword-rich. Canonical → `https://gkelite.training/`.
- A visually-hidden keyword-rich `<h1>` (`.sr-only`) sits at the top of `<body>`; the visible hero headline is an `<h2>` so the design is untouched. Section headlines are `<h2>`.
- Open Graph + Twitter Card meta; `og:locale:alternate` lists `de_DE` and `ar_AE`.
- `<link rel="preload" as="image" fetchpriority="high">` warms the first hero frame.
- `robots.txt`, `sitemap.xml` (bump `lastmod` on changes), `llms.txt`.

Site is on its final domain `gkelite.training` — all URLs already point there. If the domain ever changes again, update: Schema `url`/`image`, canonical, OG/Twitter URLs, `sitemap.xml`, `robots.txt`, `llms.txt`.

## Contact & Conversion

All conversion goes through WhatsApp. No forms, no chatbot, no email forms.

**WhatsApp link format:**
```
https://wa.me/41765687707?text=[URL-encoded message]
```

The message text changes with language. All WhatsApp links have the class `.wa-link` and are updated by `setLang()`.

## Git Conventions

- Commit directly to `main` (no branches needed for this project)
- Commit messages in German or English, descriptive
- After pushing, Cloudflare Pages auto-deploys in 1–2 minutes
- iPhone users: clear Safari cache after deploy (Settings → Safari → Clear History)

## Frame Extraction (if re-extracting from video)

```bash
# Step 1: Extract as PNG
ffmpeg -y -i "1 - Hero.mp4" -vf "scale=-1:1000:flags=lanczos" frames/hero/frame_%04d.png

# Step 2: Convert to WebP
for f in frames/hero/*.png; do
  cwebp -q 80 "$f" -o "${f%.png}.webp" && rm "$f"
done

# Verify: must be 121 frames per section, or update FRAME_COUNT in index.html
```

Source videos are 1292×1604, 4:5 aspect ratio, 24fps, ~5 seconds each.

### Performance re-export (smaller frames → faster mobile first paint)
Frames currently render at 1000px height / cwebp q80 (~21 KB each; hero = 2.6 MB).
To cut ~30–40% off load weight, re-extract at 800px / q75. Run per section folder:
```bash
ffmpeg -y -i "1 - Hero.mp4" -vf "scale=-1:800:flags=lanczos" frames/hero/frame_%04d.png
for f in frames/hero/*.png; do cwebp -q 75 "$f" -o "${f%.png}.webp" && rm "$f"; done
```
Frame *count* must stay 121/section. Visual quality at 800px/q75 is near-identical on phone canvases. Re-run for each of the 7 source videos (`1 - Hero` … `7 - CTA`).

## Open Items

- [x] **Domain:** `gkelite.training` — live on Cloudflare Pages. All URLs updated.
- [ ] **Google Business Profile** — Register as "Personal Trainer in Dubai" for Google Maps visibility
- [ ] **Google Search Console** — Submit `sitemap.xml` for `gkelite.training`
- [ ] **Performance:** Hero frames (121 = 2.6 MB) block the loader before first paint — slow on mobile cellular. Consider re-exporting frames smaller (q75 / 800px height) and/or releasing the loader after the first ~30 hero frames. See "Frame Extraction" below.

## What We Decided Against

| Idea | Why Not |
|------|---------|
| Next.js / Tailwind | Overkill — vanilla HTML/CSS/JS works perfectly |
| AI Chatbot | All conversion via WhatsApp |
| Cookie banner | No cookies, no tracking |
| AGBs page | Was faulty template, not applicable to services |
| Separate HTML files | Everything in one file, modals for legal |
| `dvh` units | Safari iOS stretching bug — use `svh` |
| Nav pill (horizontal menu) | Replaced with hamburger for mobile-first |
| 9:16 video format | Too narrow — 4:5 fills width better |
| Full-width desktop | Portrait column (620px) keeps Apple-like intimate feel |
