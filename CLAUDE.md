# CLAUDE.md — Stevie Macs Mini Bus

Context for Claude (chat or Claude Code) working in this repo. **This repo is public** (free GitHub Pages): never put private data in it — no payment IDs, pricing strategy, internal notes, credentials, or DNS/email configuration details. Keep those in the private project notes.

## What this is
A single-page, conversion-focused website for **Stevie Macs Mini Bus**, a PSV-licensed minibus hire operator in Lanarkshire, Scotland, serving Glasgow, Edinburgh and surrounding areas. Built and maintained by getPresented. as a static site (plain HTML/CSS/JS, no frameworks, no build step) on GitHub Pages.

**The one goal of the page:** get a visitor to message on WhatsApp for a price. Every section funnels to that single CTA. Don't add competing paths (extra nav links, forms, secondary CTAs).

## Client facts (all public)
- Name: Stevie Macs Mini Bus (written "Stevie Macs", no apostrophe)
- Phone / WhatsApp: 07715 687055 (`https://wa.me/447715687055`)
- Email: steviemacsminibus@gmail.com
- Facebook: https://www.facebook.com/steviemacsminibus
- Domain: `steviemacsminibus.co.uk`
- Vehicles: 8 and 16 seater minibuses, door-to-door. Also "a small selection of premium vehicles on request" — **never specify which vehicles**.
- Services: airport transfers (Glasgow & Edinburgh Airport), concert/event transport, weddings, golf trips, corporate travel, nights out.
- Claims on the page: PSV licensed operator, hire & reward insured, qualified driver.
- Booking terms (as published on the previous site): 50% deposit to secure, balance before travel, 48-hour cancellation, £20 surcharge for 23:00–06:00 pickups. These aren't currently shown on the page; don't invent other terms.
- Concert fares: **Glasgow £15pp return, Edinburgh £30pp return.** Anywhere else shows "Message for price" — do not invent fares for other cities.
- Pickup points for concerts: Hamilton Bus Station, Motherwell Train Station, Angels Uddingston.

## Hosting
GitHub Pages from the repo root, custom domain `steviemacsminibus.co.uk`. A file named `CNAME` containing just the domain must exist at the repo root. DNS is managed in Cloudflare. **The domain also carries the client's email — only ever touch the website (apex/www) records, never any other DNS record.**

## Repo map
```
index.html          the whole site (CSS + JS inline, self-contained)
404.html            branded error page (noindex)
robots.txt          allows all crawlers incl. AI bots, points at sitemap
sitemap.xml         single URL; update <lastmod> when the page meaningfully changes
llms.txt            AI-readable business summary — keep in step with the page
CNAME               domain for GitHub Pages
CLAUDE.md           this file
README.md           (stale: still says "mockup"; refresh when convenient)
assets/images/
  favicon.svg       navy circle badge + white minibus
  sm-logo.png       transparent logo (nav, 404, fallback, schema image)
  sm-01.jpg … sm-26.jpg      gallery photos
  sm-hero-01…03.jpg          hero carousel photos
  bon-jovi.jpg, hollywoodvampires.jpg, scotvswitz.jpg   concert feature backgrounds
  chris-stapleton.jpg, take-that.jpg, metallica.jpg     currently UNUSED (no confirmed upcoming date)
```

## Brand & design system
Logo is a navy/silver/black circular badge with a white Ford Transit-style minibus. Style is clean, professional, not luxury.

| Token | Value | Use |
|---|---|---|
| `--navy` | `#1F3864` | primary brand, buttons |
| `--navy-deep` | `#152A4D` | accents, eyebrows, prices |
| `--navy-light` | `#4C6A9C` | gradient top, subtle accents |
| `--black` / `--charcoal` | `#15161A` / `#1D2430` | dark bands, footer |
| `--paper` | `#F5F6F7` | page background (cool off-white) |
| `--ink` / `--soft` | `#1A1C20` / `#5C6168` | text / secondary text |
| `--line` | `#DCDFE2` | borders |

Fonts: **Playfair Display** (headings, wordmark) + **Poppins** (body, UI). Primary button = navy gradient, white text. No gold — an earlier gold/black logo was replaced by the navy/silver one.
Voice: plain, direct, British English, no hype.

## Page structure (single page, `<main>` landmark)
1. Minimal sticky nav: logo + "Get a Quote" (no menu links by design)
2. Hero: headline, copy, CTAs, trust line + **3-image arc carousel**
3. "What we cover": six service cards
4. Gallery: all 26 photos, lightbox with arrows/keys
5. Premium-vehicles teaser ("By request") — wording deliberately vague
6. Concert transport: 3 columns (OVO Hydro · Other Events · Hampden Park) + "not listed?" CTA
7. Reviews (3)
8. Final CTA band, footer, floating WhatsApp button

## ⚠️ Golden rule: never invent event data
Concert/fixture dates must be **verified against the venue's or organiser's own listing** before being added. If a date can't be confirmed, leave it out and ask — don't guess. (This was learned the hard way: an early draft contained made-up shows.) Examples already checked:
- Chris Stapleton has **no** confirmed 2026 Hydro date (last played Oct 2024) → his photo is unused.
- "Connect Music Festival" could **not** be confirmed for 2026 (dormant since 2023) → not listed. The real Royal Highland Centre series is "Edinburgh Summer Sessions".
- Scotland play some Nations League games **away** (Slovenia 26 Sep, North Macedonia 3 Oct, Switzerland 16 Nov) — those are not at Hampden and are deliberately excluded.
Data was last verified during the August 2026 build. The page hides past dates automatically, so stale data just disappears — but re-check sources before adding or refreshing.

## Concert section mechanics (bottom `<script>` of index.html)
- `VENUES`: three columns. `mode:'single'` (Hydro, Hampden): one venue/city, price shown once in the column header. `mode:'mixed'` (Other Events): each entry carries its own `venueName` + `city`; price shown per row via `CITY_FARES`, else "Message for price".
- `CONCERTS` entry: `{ artist, venueKey, venueName, city, date:'YYYY-MM-DD', image?, featured? }`.
- Feature block = soonest upcoming show for single columns; for the mixed column the entry with `featured:true` (currently Bon Jovi at Murrayfield). Feature `image` renders as a background photo with a dark gradient.
- `LIST_CAP = 7`: list shows 7 rows, then a "Load N more" row reveals the rest. A column with a feature but no other shows displays "More … dates to follow".
- Every row/feature links to WhatsApp with a pre-filled "check availability for {artist} at {venue} on {date}" message.
- Multi-day events (e.g. Edinburgh International Festival) are one row dated on the start date, with the range in `venueName`.
- Hampden is mostly football; the section is effectively "big event transport", so non-music entries are fine if verified.

## Hero carousel mechanics
- `HERO_IMAGES` (src + descriptive alt) → three `.hero-carousel-item`s assigned to `slot-left/center/right`; rotates every **2700 ms**; skipped if `prefers-reduced-motion`.
- The container uses `overflow:hidden` so side images can never bleed into the text column. **Because of that, the container's `aspect-ratio` must match the item height** or photos get clipped: desktop item width 70% → height 0.525 × width → `aspect-ratio:40/21`; mobile (≤900px) item width 84% → `aspect-ratio:20/13`. Change item width ⇒ recompute the ratio.
- `html, body` also have `overflow-x:hidden` as a safety net against horizontal scroll on mobile.

## Gallery
`GALLERY_COUNT = 26` renders `sm-01.jpg`…`sm-26.jpg`. Add/remove photos by keeping the numbering and updating the count. Alt text rotates through descriptive variants (don't revert to "photo N").

## SEO (already done — keep consistent)
Keyword-first title ("Concert Transport & Airport Transfers – Stevie Macs Mini Bus"), meta description, canonical, Open Graph + Twitter tags, **JSON-LD `TaxiService`** (name, phone, areaServed, sameAs), `<main>`, descriptive alts, favicon, robots/sitemap/llms/404. Target searches: concert transport, airport transfers (Glasgow Airport / Edinburgh Airport), minibus hire Lanarkshire/Glasgow. If phone, email, services, fares or booking terms change, update **index.html, llms.txt and the JSON-LD together**. OG/Twitter image URLs are absolute (`https://steviemacsminibus.co.uk/...`), so they only resolve once the domain is live.

## Known gaps / to do
- Reviews are three paraphrased Facebook testimonials — replace with real Google reviews as they arrive (and add review schema only when real data exists).
- Add booking terms (deposit/cancellation/late-night surcharge) to the page if the client wants them visible.
- `README.md` is outdated.
- Submit `sitemap.xml` in Google Search Console once live; set up a Google Business Profile.
- Unused concert photos (Stapleton, Take That, Metallica) can be wired in only when a real date is confirmed.

## Working rules for Claude
- When asked for "just index.html", deliver the complete, self-contained file — don't paste fragments.
- After edits, sanity-check tag balance (div/section/script/ul) and that no stale class names remain.
- No frameworks, no build tools, no external JS. Keep CSS/JS inline in `index.html`; shared look stays consistent with `404.html`.
- No `localStorage`/cookies/trackers added without asking (the site is currently cookie-free).
- Don't touch the logo files; use `sm-logo.png` as supplied.
- Ask before changing pricing, fares, licensing/insurance claims, or anything the client hasn't confirmed.
