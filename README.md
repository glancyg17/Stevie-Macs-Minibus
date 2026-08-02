# Stevie Macs Mini Bus — Mockup

Single-page site mockup for client review before build.

## Structure
```
index.html
assets/
  images/
    sm-01.jpg – sm-26.jpg   ← gallery photos
    sm-logo.png             ← transparent logo, used in nav/hero/footer
```

## To view
Just open `index.html` in a browser — everything is local, no server needed.

## Notes for whoever builds the real site
- Gallery auto-renders `sm-01.jpg` through `sm-26.jpg`. To add/remove photos, drop files
  into `assets/images/` following the same naming, and update `GALLERY_COUNT` at the
  bottom of `index.html`.
- Concert transport section auto-hides past dates and sets price by venue city
  (Glasgow £15pp, Edinburgh £30pp) — see the `CONCERTS` array in the same script block.
- WhatsApp number and email are hardcoded throughout — search/replace if either changes.
- This is a mockup for client sign-off, not the final SEO-ready build (no sitemap.xml,
  robots.txt, llms.txt, schema, etc. yet — add those per the standard SOP once approved).
