# Photo & video swap guide

All site photos and videos use **stable, semantic file names**. When the
retouched, watermark-free versions arrive from Chad T. Charlton, you swap
files in place — no HTML or CSS changes needed.

## Where each file appears on the site

| File | Used in section | Original source |
|------|-----------------|------------------|
| `photos/photo-hero.jpg` | Hero backdrop (top of page) | CTC18361 |
| `photos/photo-bar.jpg` | Real-moments gallery, **large tile (left)** | CTC18664 |
| `photos/photo-checkin.jpg` | Real-moments gallery, top-middle tile | CTC18242 |
| `photos/photo-transfer.jpg` | Real-moments gallery, top-right tile | CTC18231 |
| `photos/photo-pos.jpg` | Real-moments gallery, bottom-middle tile | CTC18668 |
| `photos/photo-gate.jpg` | Real-moments gallery, bottom-right tile | CTC18236 |
| `photos/photo-festival.jpg` | Reserved (not yet placed on page) | CTC18662 |
| `video/showreel-2.mp4` | Showreel block (click-to-play) | LIVECLOUD 2 MARCH 2026 |
| `video/showreel-2-poster.jpg` | Showreel poster frame | derived from showreel-2.mp4 |
| `video/showreel-1.mp4` | Reserved (alternative showreel) | LIVECLOUD 1 MARCH 2026 |
| `video/showreel-1-poster.jpg` | Reserved | derived from showreel-1.mp4 |

## To swap a photo

1. Receive the retouched JPEG from the photographer.
2. Resize to **max 1600 px wide** (web-friendly), JPEG quality ~80:
   ```sh
   sips -Z 1600 -s format jpeg -s formatOptions 80 input.jpg --out output.jpg
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
