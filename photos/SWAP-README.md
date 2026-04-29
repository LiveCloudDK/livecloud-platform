# Photo & video swap guide

All site assets use **stable, semantic file names** — drop new versions
in place over the existing files and the page picks them up on next
hard-refresh. No HTML or CSS changes needed.

Current assets are the **March 2026 finals** (retouched, no watermark,
no in-frame logo on the showreel videos).

## Format strategy: WebP + JPEG fallback

Every photo on the page is served via a `<picture>` element:

```html
<picture>
  <source srcset="photo-hero.webp" type="image/webp">
  <img src="photo-hero.jpg" alt="…" width="…" height="…">
</picture>
```

Modern browsers (~96% of traffic) download the **`.webp`** — typically
~25–35% smaller than the JPEG for the same visual quality. Older
browsers automatically fall back to the **`.jpg`**.

**When you swap a photo, you need to update both files** (or run the
helper script below to auto-generate the WebP).

## Where each file appears on the site

| File pair (`.jpg` + `.webp`) | Used in section | Source (March 2026 finals) |
|---|---|---|
| `photos/photo-hero` | Hero backdrop (top of page) | 10 -CTC18399-Edit (POS tap-to-pay) |
| `photos/photo-bar` | Real-moments gallery, **large tile (left)** | 12 -CTC18664-Edit (busy bar) |
| `photos/photo-checkin` | Gallery, top-middle tile | 4 -CTC18242-Edit (multiple QR phones) |
| `photos/photo-transfer` | Gallery, top-right tile | 2 -CTC18231-Edit (peer transfer) |
| `photos/photo-pos` | Gallery, bottom-middle tile | 13 -CTC18668-Edit (bartender scanning) |
| `photos/photo-gate` | Gallery, bottom-right tile | 3 -CTC18236-Edit (gate scan) |
| `photos/photo-festival` | Reserved (not on page) | 11 -CTC18662-Edit (single phone + beer) |
| `jakob` (in repo root) | Closing-quote portrait (×2) | — |
| `images/app-billet` | Phone mockup, Jeres-app section | App ticket screenshot |
| `images/app-beats` | Phone mockup, Beats section | App Beats wallet screenshot |
| `images/app-preorder` | Phone mockup, Mere-flow section | App pre-order menu screenshot |
| `video/showreel-2.mp4` | Click-to-play showreel after the gallery, label "Scan & betal" | LIVECLOUD 2 NO LOGO MARCH 2026 |
| `video/showreel-1.mp4` | Atmospheric autoplay loop between Ringsted and Manifesto (no controls, no label) | LIVECLOUD 1 NO LOGO MARCH 2026 |
| `video/showreel-{1,2}-poster.{jpg,webp}` | Poster frames | derived from each MP4 |

## Recipe — swap a photo

```sh
# 1. Receive new JPEG from the photographer
# 2. Resize to max 1800 px wide, JPEG quality 82
sips -Z 1800 -s format jpeg -s formatOptions 82 input.jpg --out photos/photo-hero.jpg

# 3. Generate the matching WebP (modern browsers prefer this)
cwebp -q 82 photos/photo-hero.jpg -o photos/photo-hero.webp

# 4. Hard-refresh the browser (Cmd+Shift+R)
```

For app screenshots (smaller, served at ~240px wide on the page):

```sh
sips -Z 720 -s format jpeg -s formatOptions 85 input.png --out images/app-billet.jpg
cwebp -q 85 images/app-billet.jpg -o images/app-billet.webp
```

## Recipe — swap a showreel

```sh
# 1. Receive the new master MP4
# 2. Compress to 720p H.264 + faststart (streams progressively)
ffmpeg -i input.mp4 \
  -vf "scale=1280:720:flags=lanczos" -an \
  -c:v libx264 -profile:v high -preset slow -crf 26 -movflags +faststart \
  video/showreel-2.mp4

# 3. Extract a poster frame at 1 second
ffmpeg -i video/showreel-2.mp4 -ss 00:00:01 -frames:v 1 \
  -vf "scale=1600:-1" video/showreel-2-poster.jpg

# 4. WebP poster for modern browsers
cwebp -q 80 video/showreel-2-poster.jpg -o video/showreel-2-poster.webp
```

## Reorder which photo lives where

The gallery markup is in `index.html` under `<!-- ==== REAL MOMENTS GALLERY -->`.
Each `<picture>` references a `photo-*.{jpg,webp}` pair. Swap the file paths
and the `<div class="caption">` text — no CSS changes.

The first gallery tile has `class="moment tall"` and spans 2 rows on desktop.
Pick your strongest "hero" shot for that slot.

## Switch the hero from photo to video

Inside `<div class="hero-bg">` in `index.html`, replace the `<picture>`
block with:

```html
<video autoplay muted loop playsinline poster="photos/photo-hero.jpg">
  <source src="video/showreel-2.mp4" type="video/mp4">
</video>
```

Then add `.hero-bg video { width:100%; height:100%; object-fit:cover; }` to
the existing `.hero-bg` rule.
