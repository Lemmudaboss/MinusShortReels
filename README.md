# Short-Form Blocker

Blocks **YouTube Shorts** and **Instagram Reels**, on your computer.

| | What you get |
| --- | --- |
| [**Chrome extension**](#chrome-extension) | Removes Shorts and Reels from youtube.com and instagram.com, and opens their links without the swipe feed. |

---

## Chrome extension

<img src="docs/popup.png" alt="Short-Form Blocker popup" width="300">

### YouTube Shorts
- **Hidden:** Shorts shelves on the home page, subscriptions, search results, the watch-page sidebar, channel pages and history. Also the Shorts sidebar entry, the Shorts search filter, the Shorts channel tab and Shorts in notifications. Covers `m.youtube.com` too.
- **Redirected:** `/shorts/ID` plays in the normal player (`/watch?v=ID`), or goes to the homepage if you choose. The Shorts feed goes home, and a channel's Shorts tab goes to its Videos tab.

### Instagram Reels
- **Hidden:** the Reels tab, reel posts in your home feed, reels in Explore, and the Reels tab on profiles. Photo posts with music stay.
- **Redirected:** the Reels tab goes home. A reel inside the swipe feed (`/reels/ID`) opens on its own (`/reel/ID`), and a profile's Reels tab goes to the profile. Reels sent to you in DMs still open, unless you set links to "Go to homepage".
- Reels that start playing while hidden are paused.

### Install
1. Download this repo (**Code → Download ZIP**) and unzip it, or `git clone` it.
2. Open `chrome://extensions` and turn on **Developer mode**.
3. Click **Load unpacked** and choose the **`extension`** folder.

Works in Chrome, Edge, Brave, Opera and Vivaldi. After pulling changes, click the reload icon on the extension's card and refresh any open YouTube or Instagram tabs.

### How it works
| Part | File | Job |
| --- | --- | --- |
| Hiding | `extension/src/sites/*.css` | CSS rules scoped to `html[data-ysb-hide]`, so switching a site off restores the page instantly. |
| Content script | `extension/src/content.js` | Toggles that attribute from your settings and catches clicks and in-page navigation to Shorts/Reels. Also flags YouTube elements only recognisable by their label, and pauses hidden Instagram videos. |
| Redirect rules | `extension/rules/*.json` | `declarativeNetRequest` rules that rewrite Shorts/Reels URLs before the page loads. |
| Service worker | `extension/src/background.js` | Turns the rulesets on and off to match your settings, and shows the "off" badge. |
| Popup | `extension/src/popup.*` | Settings: master switch, plus a hide switch and a link option for each site. |

Permissions: `storage`, plus `declarativeNetRequest` and access to youtube.com and instagram.com only. No tracking, no network requests.

### When a site changes its layout
Inspect the element that slipped through and add a selector to the site's file in `extension/src/sites/`, scoped with `html[data-ysb-hide]`. On YouTube, target tag names (`ytd-…`, `yt-…`, `ytm-…`). On Instagram, class names are random, so use link targets (`a[href*="/reel/"]`) and tags like `article`.

### Tests
End-to-end tests load the extension in headless Chromium against local mock pages of both sites. They check hiding, every redirect mode, in-page navigation, settings and the popup:

```bash
pip install playwright && playwright install chromium
./extension/tests/run.sh
```

---

## Repository layout

```
android/      Android app (Kotlin, no third-party dependencies)
extension/    Chrome extension (Manifest V3)
docs/         Screenshots
.github/      Workflow that builds and publishes the APK
```

## License

[MIT](LICENSE). Not affiliated with or endorsed by YouTube, Google, Instagram or Meta.
