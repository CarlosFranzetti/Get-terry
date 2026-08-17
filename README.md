# TERRY CATCH

**Play: https://get-terry.vercel.app**

A tiny 8-bit arcade game built for iOS Safari. Terrys fall from the sky — swipe
to slide your basket under them, and don't catch the bombs.

Everything lives in a single `index.html`: no build step, no dependencies, no
assets. Sprites, the 3×5 pixel font, and the chiptune sounds are all generated
in code.

## Play

The deployed copy lives at [get-terry.vercel.app](https://get-terry.vercel.app),
and redeploys automatically on every push to this branch.

To run it locally instead, open `index.html` in a browser, or serve the folder
and visit it on your phone:

```sh
python3 -m http.server 8000
# then open http://<your-computer-ip>:8000 on the phone
```

On iPhone, tap the share icon and **Add to Home Screen** to launch it
fullscreen with no browser chrome.

## Controls

| Input | Action |
| --- | --- |
| Swipe / drag anywhere | Slide the basket — a fast flick keeps sliding with momentum |
| Tap | Start, or retry after game over |
| ← / → or A / D | Move (desktop) |
| Space / Enter | Start or retry (desktop) |

You don't need to touch the basket itself — dragging anywhere on screen moves
it, so your thumb never covers the action.

## Rules

- **Terry** `+1` — catch it.
- **Gold Terry** `+5` — rarer, worth five.
- **Bomb** `-1 life` — let it fall past you.
- Missing a Terry also costs a life. You start with three.
- Every 5 catches in a row raises the score multiplier, up to ×5. Taking a hit
  resets the streak.
- Every 8 catches raises the level, and everything falls faster.
- **A BLOOD ROUND can break out at any moment.** It runs on its own clock
  rather than the level, so the first 20 seconds of a run are safe and after
  that anything can happen — it can hit on level 1. Each one lasts 9–18
  seconds, and how gory it gets is random too, so no two are alike. The sky
  turns over, bombs get more
  common, and any Terry you fail to catch hits the ground and detonates —
  blood sprays up the screen, runs back down it in streaks, and pools on the
  floor. Catching one pops it in the basket instead. A miss costs the same
  single life it always did, but the round pays **double**, so it's a gamble
  rather than a punishment.
- Every 60 points earns a life back, up to the maximum of three.
- Your best score is saved in `localStorage`.

## Notes on how it's built

- Terry is an actual photograph, not pixel art. The head is inlined as a 4.9 KB
  JPEG data URI and clipped to an ellipse on an offscreen canvas at load time,
  so it stays a real image while everything around it stays 8-bit. The gold
  bonus Terry is the same photo with its luminance remapped onto gold at
  runtime, which avoids shipping a second image.
- The game is laid out in 160×240 logical pixels, but the canvas backing store
  runs at device resolution — a 160×240 buffer would reduce a photograph to
  mush. The context is scaled by a whole number of device pixels per game
  pixel, so the pixel-art sprites and the bitmap font still land on exact pixel
  boundaries and stay crisp. Smoothing is switched on only while drawing the
  photo.
- Difficulty ramps through fall speed rather than through crowding, and at most
  four items fall at once. New drops avoid the lane of anything still near the
  top, so a bomb never traps a Terry in a position where catching and missing
  both cost a life.
- Touch handlers call `preventDefault` and the page sets `touch-action: none`
  to stop iOS scroll, rubber-banding, and double-tap zoom mid-game.
- Audio is WebAudio square waves, created on the first touch to satisfy iOS
  autoplay rules.
