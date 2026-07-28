# dfogel89.github.io — game invite links (Universal Links / App Links)

This folder is the **GitHub Pages user site**. Hosting it makes the invite links
`https://dfogel89.github.io/<game>/join?code=NNNN` open the right app (if
installed) or a landing page (if not). One site covers every game.

Layout:
```
.well-known/apple-app-site-association   <- iOS Universal Links (all games)
.well-known/assetlinks.json              <- Android App Links (all games)
<game>/join/index.html                   <- landing page per game
index.html                               <- root placeholder
```
Games covered: crosswars, battlesudoku, flipdeck, bidscrabble, superchess
(the multiplayer ones — single-player apps have no "join a game" link).

## How to host it (one time)

1. Create a **public** GitHub repo named exactly **`dfogel89.github.io`**.
2. Put everything in this folder at the repo ROOT and push it.
3. Repo Settings -> Pages -> Deploy from branch -> `main` -> `/ (root)` -> Save.
4. After ~2 min, verify these load:
   - https://dfogel89.github.io/.well-known/apple-app-site-association
   - https://dfogel89.github.io/.well-known/assetlinks.json
   - https://dfogel89.github.io/crosswars/join?code=1234  (shows a landing page)

## Fill in the real values first (placeholders can't verify)

I can't invent these — fill them in before/after hosting:

1. **Apple Team ID** — in `.well-known/apple-app-site-association`, replace every
   `TEAMID` with your 10-character Apple Developer Team ID (Apple Developer ->
   Membership, or it prints in any `eas build --platform ios` run). One value,
   used for all games.

2. **Android signing SHA-256 (one PER game)** — in `.well-known/assetlinks.json`,
   replace each `REPLACE_WITH_<GAME>_ANDROID_SHA256` with that game's app-signing
   SHA-256. Each game has its OWN fingerprint. Get each from its project folder:
   `npx eas-cli credentials` -> Android -> (production) -> the keystore SHA-256.
   Once a game is on Google Play, use the **Play App Signing** SHA-256 from Play
   Console -> Test and release -> App integrity (Google re-signs, so that's the
   one that ends up on devices).

## Notes

- The app side is already wired: each game's `app.json` has the iOS
  `associatedDomains` + Android `autoVerify` intent filter for its
  `/<game>` path, and each app parses the incoming link to auto-join. They just
  need the rebuild (the push script) to take effect.
- iOS caches the AASA via Apple's CDN; after hosting/fixing it, a fresh install
  may be needed before the link starts opening the app.
- Until this is hosted + values filled + apps rebuilt, invites still work: the
  link shows the landing page, and the in-app share + manual code entry work.
- Store URLs in the landing pages are TODO(launch) placeholders — fill them once
  the apps are public.
- This supersedes the earlier `CrossWarsApp/universal-links/` staging (which
  covered only CrossWars).
