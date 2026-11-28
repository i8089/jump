# Jump

Jump rope interval timer, installed as a PWA on an iPhone. Everything is in
`index.html` (markup, CSS, JS). No build step, no dependencies, no tests.

## Shipping

- Pushing to `main` deploys to https://i8089.github.io/jump/ via GitHub Pages
  (about 3 minutes). Check with `gh api repos/i8089/jump/pages/builds/latest`.
- Bump `CACHE` in `sw.js` on every release, or the installed app keeps old files.
- Other sessions sometimes merge PRs into `main`. Pull before working; never force-push.

## Audio (iOS is the target, and the hard part)

- The countdown is the AudioContext clock (`ctx.currentTime - base`), not JS
  timers. `recoverCtx()` and the render-loop watchdog pause the run if the
  context stops after an interruption; keep them.
- Cues are five short clips scheduled from `cueList()`. Don't go back to one
  continuous track: it holds the iOS audio session open and stops the user's music.
- Use Web Audio, not `<audio>`: an `<audio>` element puts a Now Playing player
  on the lock screen.
- `navigator.audioSession.type`: `playback` stops music but ignores the ring
  switch; `ambient` mixes with music but the ring switch silences it. No type
  does both (`transient` was tried on the phone and behaved like `ambient`).
  The type is fixed when the context is created, so changing it means
  `dropCtx()` and a new context.
- Audio can only start from a tap.

## Testing

- `python3 -m http.server` and open it in a browser. The service worker caches
  aggressively: unregister it or add a `?v=` query when reloading.
- Start needs a real click, not a JS `.click()` with no gesture.
- Desktop browsers have no `navigator.audioSession`, so ring-switch and
  music-mixing behaviour can only be checked on an iPhone. Say so rather than
  claiming it works.
- Check the big number at 5 characters (`10:00`) on a 375 px viewport: it must
  not overflow (`--chars` and `setCount()` handle it).

## Product rules

- Default workout stays 5 rounds, 60 s first jump, 45 s jump, 15 s rest.
- Keep it lean: one screen, one big number, tap anywhere to pause.
