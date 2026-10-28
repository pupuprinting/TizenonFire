# FireHome

A home-screen launcher for the Fire Stick 4K (1st gen) that copies the Samsung Smart Hub effect:
focus an app in the bar and its suggested content appears above it, over a big backdrop.

## Build the APK

**Easiest (no installs):** put this folder in a GitHub repo and push it. The *Build APK* workflow
runs automatically; download `FireHome-apk` from the run's *Artifacts* section (Actions tab).

**Android Studio:** open the folder, let it sync, *Build > Build APK(s)*.
(`./gradlew assembleDebug` also works with JDK 17 and the Android SDK, platform 34.)

## Install on the Fire Stick

1. Fire TV: Settings > My Fire TV > Developer options > turn on **ADB debugging** and **Install unknown apps**.
2. Note the stick's IP (Settings > My Fire TV > About > Network).
3. On your computer: `adb connect <ip>:5555` then `adb install -r app-debug.apk`.
   (Or install the "Downloader" app on the stick and fetch the APK from a link.)
4. Open it from Apps > FireHome.

## The Home button

Amazon does not let third-party launchers replace the Home button on Fire OS 6, so FireHome is opened
from the Apps list. To get it on a button press you can remap one with an app such as Button Mapper
(not tested here). Back is disabled inside FireHome on purpose.

## What shows above the bar (4 tiles left + 4 tiles right)

Open **FireHome Settings** (last tile of the bar, or the Menu button on the remote) once:

* **TMDB key**: free at themoviedb.org (account > Settings > API, the v3 "API Key"). Also set your country code (default IT).
* **Link Plex account**: shows a code; on your phone open plex.tv/link and enter it. FireHome then finds your server by itself (same-network address first).

| App | Left (public) | Right (personal) |
|---|---|---|
| Netflix, Prime Video, Disney+, Crunchyroll, Apple TV | "Popular on <app>" from TMDB, only titles really on that service in your country | "Recently Watched" from your Trakt history (titles streaming on this app's service first); without Trakt: "Recently Opened", the last titles you opened from FireHome |
| Plex | Recently Added shows and movies from your server | Continue Watching / Up Next from your server (with progress bars) |

**Trakt** (optional): the Trakt phone app can copy your Netflix / Prime Video / Apple TV history into your
Trakt account (about once a day). Link Trakt in Settings with the code shown on the TV. Trakt does not record
which service something was watched on, so FireHome shows titles available on that app's service first.
Create the Trakt API app at trakt.tv/oauth/applications with redirect URI `urn:ietf:wg:oauth:2.0:oob`.

Streaming apps do not share your viewing history with other apps on Fire OS, so for them the right side is
built from what you open through FireHome. Clicking a tile opens the app (not the exact title: no deep links).
Rows are cached for 10 minutes and refreshed after you come back from an app.

## Keys without typing on the remote (GitHub secrets)

In your GitHub repo: Settings > Secrets and variables > Actions > New repository secret. Add any of
`TMDB_KEY`, `TRAKT_CLIENT_ID`, `TRAKT_CLIENT_SECRET`. The workflow writes them into the APK at build time and
Settings is pre-filled. **Make the repo private**: anyone who can download the APK from a public repo can read them.

## Manual rows and other apps (`shortcuts.json`)

`app/src/main/assets/shortcuts.json` maps Android package names to settings. Put a file with the same format at
`/sdcard/Android/data/com.example.firehome/files/shortcuts.json` to change it without rebuilding.

* `tmdbProvider`: TMDB watch-provider id (Netflix 8, Prime Video 119, Disney+ 337, Crunchyroll 283, Apple TV+ 350).
  Add it to any app to get its "Popular on..." row.
* `"source": "plex"`: live Plex rows.
* `rows`: manual rows (used when no live source is set up, e.g. the "add your key" hint tile). Row 1 = left, row 2 = right.
  Items: `title`, `image` (URL), `badge`, `progress` 0-100, `showTitle`, `link` (deep link tried in that app).
* `backdrop`: optional big picture. Apps listed in the file appear first in the bar, in file order.

Items without an image (or whose image fails) show their title on a coloured tile.

This product uses the TMDB API but is not endorsed or certified by TMDB. Streaming availability data comes from JustWatch via TMDB.
