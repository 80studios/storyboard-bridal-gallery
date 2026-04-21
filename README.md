# storyboard-bridal-gallery

Static HTML pages served at **gallery.storyboardbridal.com** as unique per-pin destinations for the Storyboard Bridal Content Engine. Generated locally by `scripts/pageGenerator.mjs` in the main app repo (`~/storyboard-bridal-app`) and committed here in batches by the build pipeline.

## Why this exists

Pinterest applies a same-URL penalty when multiple pins share the same `Website_Link`. For pins that have no identified real outbound destination (no designer brand site, no editorial publication, no TikTok match), the Content Engine was falling back to the Storyboard Bridal TikTok and Substack homepages — creating repeat-URL pressure. This gallery replaces that fallback with a unique per-pin page: a real destination per pin, on a real domain Storyboard Bridal owns.

## Structure

```
gallery.storyboardbridal.com/
├── index.html            ← simple root landing (unlikely to be hit; gallery is pin-URL driven)
├── 404.html              ← Cloudflare Pages serves this for unknown routes
├── {hash}.html           ← one per pin; 6-hex filename deterministic from build_image_hash
└── {hash}_N.html         ← collision suffix if two different pins produce the same 6-hex
```

Every pin page carries `<meta name="robots" content="noindex, nofollow">` — these aren't meant to enter Google's index; Pinterest's crawler still follows per its own rules.

## Deploy

Hosted on [Cloudflare Pages](https://pages.cloudflare.com/). The free tier allows 500 deploys per month; the build pipeline batches one commit per run to stay well under.

Production branch: `main`. Every push triggers a rebuild + auto-deploy. Typical time-to-live: 30 seconds to 3 minutes.

## Design source of truth

Page template lives in [`scripts/pageGenerator.mjs`](../storyboard-bridal-app/scripts/pageGenerator.mjs) in the main app repo. Brand colors, typography (Cormorant Garamond via Google Fonts), and layout are defined there. Do not edit HTML files here by hand — they're generated output.

## Regenerating locally

```
cd ~/storyboard-bridal-app
node scripts/generate-sample-page.mjs
```

Renders a sample page from the latest built pin in the queue JSON, writes it to `~/storyboard-bridal-app/generated-pages/`, and opens it in the default browser.
