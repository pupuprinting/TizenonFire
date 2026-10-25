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

## Choosing the suggested content

Amazon/Netflix/etc. do not hand other apps their "recommended" feed on Fire OS, so the rows come from
a file you control: `app/src/main/assets/shortcuts.json` (bundled sample). To edit without rebuilding,
put a file with the same format at
`/sdcard/Android/data/com.example.firehome/files/shortcuts.json` (`adb push shortcuts.json` there),
then reopen FireHome.

* Keys are Android package names (Netflix `com.netflix.ninja`, Prime Video
  `com.amazon.amazonvideo.livingroom`, Disney+ `com.disney.disneyplus`, Plex `com.plexapp.android`).
  `_default` is used for apps without an entry.
* Apps listed in the file appear first in the bar, in file order; the rest follow alphabetically.
* Each app: optional `backdrop` image URL and `rows` (each `title` + `items`).
* Each item: `title`, `image` (URL), optional `badge` (red "New Season" tag), `progress` 0-100
  (switches to the "Continue Watching" caption + progress bar) and `link` (deep link tried in that app
  on click; falls back to just opening the app).

The sample uses placeholder images from placehold.co / picsum.photos, so the stick needs internet.
