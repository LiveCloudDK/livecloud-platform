# Photo & video swap guide

All site photos and videos use **stable, semantic file names**. When new
versions of the photo/video assets arrive from Chad T. Charlton, drop
them in place over the existing files — no HTML or CSS changes needed.

Current assets are the **March 2026 finals** (retouched, no watermark,
no in-frame logo on the showreel videos).

## Where each file appears on the site

| File | Used in section | Source (March 2026 finals) |
|------|-----------------|------------------|
| `photos/photo-hero.jpg` | Hero backdrop (top of page) | 10 -CTC18399-Edit (POS tap-to-pay) |
| `photos/photo-bar.jpg` | Real-moments gallery, **large tile (left)** | 12 -CTC18664-Edit (busy bar, beer + phones) |
| `photos/photo-checkin.jpg` | Real-moments gallery, top-middle tile | 4 -CTC18242-Edit (multiple QR phones) |
| `photos/photo-transfer.jpg` | Real-moments gallery, top-right tile | 2 -CTC18231-Edit (peer ticket transfer) |
| `photos/photo-pos.jpg` | Real-moments gallery, bottom-middle tile | 13 -CTC18668-Edit (bartender scanning QR) |
| `photos/photo-gate.jpg` | Real-moments gallery, bottom-right tile | 3 -CTC18236-Edit (gate scan moment) |
| `photos/photo-festival.jpg` | Reserved (not yet placed on page) | 11 -CTC18662-Edit (single phone QR + beer) |
| `video/showreel-2.mp4` | Showreel block (click-to-play) | LIVECLOUD 2 NO LOGO MARCH 2026 |
| `video/showreel-2-poster.jpg` | Showreel poster frame | derived from showreel-2.mp4 |
| `video/showreel-1.mp4` | Reserved (alternative showreel) | LIVECLOUD 1 NO LOGO MARCH 2026 |
| `video/showreel-1-poster.jpg` | Reserved | derived from showreel-1.mp4 |

## To swap a photo

1. Receive the new JPEG from the photographer.
2. Resize to **max 1800 px wide** (retina-ready, web-friendly), JPEG quality ~82:
   ```sh
   sips -Z 1800 -s format jpeg -s formatOptions 82 input.jpg --out output.jpg
   ```
3. Save it over the existing file in `photos/` using the **exact same
   filename** (e.g. `photo-hero.jpg`).
4. Hard-refresh the browser (Cmd+Shift+R) to bypass cache.

## To swap the showreel video

1. Receive the retouched master.
2. Compress to 720p H.264 with web-friendly faststart:
   ```sh
   ffmpeg -i input.mp4 \
     -vf "scale=1280:720:flags=lanczos" -an \
     -c:v libx264 -profile:v high -preset slow -crf 26 -movflags +faststart \
     video/showreel-2.mp4
   ```
3. Re-extract the poster frame:
   ```sh
   ffmpeg -i video/showreel-2.mp4 -ss 00:00:01 -frames:v 1 \
     -vf "scale=1600:-1" video/showreel-2-poster.jpg
   ```

## To swap which photos appear in the gallery

The gallery is in `index.html`, marked by `<!-- ==== REAL MOMENTS GALLERY -->`.
Each tile references one of the `photos/photo-*.jpg` files. Change the
`src=""` and the `<div class="caption">` to use a different photo or wording.
The first tile has class `tall` and spans both rows on desktop — pick your
strongest "hero" shot for that slot.

## Swap the hero photo for a different shot

Edit `photos/photo-hero.jpg` in place, OR change the `<img src="">` inside
`<div class="hero-bg">` in `index.html` to point at a different file.

## Optional: switch hero from photo to video

In `index.html`, replace the `<img>` inside `<div class="hero-bg">` with:

```html
<video autoplay muted loop playsinline poster="photos/photo-hero.jpg">
  <source src="video/showreel-2.mp4" type="video/mp4">
</video>
```

Add CSS rule `.hero-bg video { width: 100%; height: 100%; object-fit: cover; }`.
