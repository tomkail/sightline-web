# Sightline: design document

Working title. Short guide for anyone (person or chat) changing the game. Last updated 2026-11-11.

Play it: https://tomkail.github.io/sightline-web/

## The game

A physical party game for phones, in the spirit of Johann Sebastian Joust. A target sits on a table. Every player points their phone camera at it. You win by being the last phone that can still see it, so you block cameras, cover or move the target, and get in the way.

The game is less about seeing the target and more about **not being knocked or obstructed by your friends**. The phone is a referee; the fun happens in the room.

## Principles

Use these to settle design arguments.

1. **Zero setup.** A link, a camera permission, and something to point at. No install, no account, no typing beyond a 4-letter room code.
2. **The target is something you already have.** A ring with a solid dot in the middle: draw it with a pen, print it, or show it on a spare phone.
3. **Detection must never be what loses you the game.** Losing because a friend blocked you is the game. Losing because the camera flickered is a bug. A risky low angle is fair play.
4. **The phone talks to the room, not just its owner.** Sounds are loud and each player has their own pitch, so the table can hear whose phone is in trouble.
5. **Phone-first.** Big text, big buttons, touch only, one screen per step, nothing scrolls.
6. **Sync is optional.** Everything works with each phone on its own; hosting adds a shared start, shared rules and a result.

## How a round works

- **Setup:** choose Host, Join or Play unsynced, then a sound check and a camera check.
- **Lobby:** everyone points at the target. The phone chimes when it gets a solid lock and again when it loses it, which doubles as the proof that sound is on. Players are emoji, random to start.
- **Round:** a 3-second countdown, then keep the target in view. Losing sight drains a grace bar while the phone beeps; finding it again refills the bar slowly. Empty bar: out. Last one in wins.
- **Modes:** Classic (just keep sight), Zones (stay in a distance band that moves), Retreat (minimum distance grows), Crowd in (maximum distance shrinks).
- **Who sets what:** in a hosted game the host sets mode, grace time and target size; joined players see those settings locked. Unsynced, each player sets their own.

## Fairness rules already in the build

Keep these when changing detection or timing.

- Grace bar (default 1.5 s, tunable) rather than instant elimination.
- Dropouts under 0.3 s are ignored (motion blur, autofocus).
- "Lock quality" is the share of the last 30 frames with a detection. The lobby shows it; the host is warned before starting if anyone is under 80%.
- Found/lost chime uses two thresholds (found at 80%, lost at 30%) so a flickering view stays quiet.
- A "Camera view" toggle shows what the detector sees, for diagnosing bad lighting.

## The target

- Three sizes, with no dimensions shown to players: **Phone screen** (5 cm ring), **Printout** (9 cm, two per page), **Custom** (exact size, behind a tap).
- The on-screen target is drawn at true physical size. The phone's screen scale comes from a saved bank-card check, else a table of iPhone models, else a guess that is labelled as one.
- Printing is true-size, with a 100 mm ruler on the sheet to check the printer didn't rescale.
- The target screen has its own address (`?target=1`) for sharing, and the target is a real image so long-press or right-click saves it.
- Distance is estimated from the ring's apparent size. It is good enough for the distance modes after "Calibrate at 50 cm", and approximate before.

## How it is built

- One static file, `index.html`. No build step, no server code. Pushing to `main` deploys to GitHub Pages in about a minute.
- Custom bullseye detector in plain JavaScript: adaptive threshold, connected components, then "ring with a dot at its centre". Runs on a 640 px frame.
- Sync is peer-to-peer through PeerJS. The host owns the roster, the start, eliminations and the winner; each phone does its own tracking.
- Sound is Web Audio. Vibration is used where it exists (Android; not iPhone Safari).
- Settings are kept in `localStorage` under the `sl_` prefix.

## Look and feel (direction agreed, not built yet)

The game still has its plain dark prototype look. A separate design chat has settled the direction below through mockups; none of it is in `index.html` yet.

- **Theme: paparazzi.** Players are photographers jostling for the shot. Made-up celebrities only, never a real person's likeness.
- **Tone:** loud, colourful, zany, over the top. Touchstones are Jet Set Radio, Rhythm Heaven, Bishi Bashi, WarioWare and Point Blank. WarioWare-style jokes in the copy.
- **Shapes:** angular UI with lots of outlines and hard offset shadows. Patterned backgrounds that scroll slowly: a sunburst, and halftone shapes (dots or diamonds) that grow until they merge. No outlines on patterns.
- **Buttons:** chunky. Pressing slides the button down into its shadow, with no squash. Slide off to cancel. Hold to confirm for actions like leaving a game. Styles still in the running: Slab, Press pass, Sticker, Flash.
- **Motion:** transitions carry meaning. A dot wipe is a new phase in the same place; a card flip or slab push is going somewhere else. The round intro (banner, "Ready?", "Go!") is snappy, about 2.6 s.
- **End screen:** the celebrity strikes a pose with anime-style sparkles. This is the only place Japanese-style effects appear.
- **Polaroids** are the motif for swipeable cards: how to play, game modes, and photos from the round. Round photos are captured automatically and shared between the phones in the game.
- **Vibration** is wanted for feedback: pulses as grace runs out, a tap on re-finding the target, a thud on knockout.
- **Sound:** game-event sounds stay loud, because they inform the room. Button and interface sounds are off or very soft.
- **Avoid:** serif fonts, muted or autumnal palettes, soft rounded "bubbly" styling, TV game-show dressing (marquee bulbs, caption bars), Japanese text, and the fonts Fredoka and Dela Gothic.

## Open decisions

- **Name.** Sightline is a placeholder. Shortlist: Scumbags, Hot Shot, Snap Scum, Paparazzle.
- **Fonts.** Three roles (logo, header, body), all still being chosen. Tabloid-style heavy condensed sans is the leading idea for headers.
- **Colour set** and **button style**: narrowed, not picked.
- **"Any object" as the target** and **true depth-based distance**: neither is practical on the web on iPhone. The plan is a native iPhone build to compare, which needs a Mac with Xcode.

## Not yet tested on real hardware

Everything so far was checked in a desktop browser with a simulated camera. Still to confirm on phones: detection of hand-drawn targets, usable range per target size, speaker loudness, the iPhone silent-switch bypass, the iPhone screen-size table, real printing, and sync over real networks.

## Working agreements

- Keep it one file and keep every screen free of scrolling on a small phone (375 × 667).
- Work on `main` and push to deploy. Several chats work in this folder, so pull before editing and keep changes small.
- When a decision here changes, update this file in the same commit.
