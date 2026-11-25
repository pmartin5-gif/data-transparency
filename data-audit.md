# Data Audit: What a Visitor's Browser Can Reveal

An inventory of the data a site like this can collect, what's already in use, and what each piece could suggest about a person. It's a reference for deciding what to add, not a plan.

**Reliability key:** **All** = every major browser · **Chromium** = Chrome, Edge, Opera, Samsung Internet (most visitors) · **Varies** = blocked, capped or faked in some browsers (noted)
**⚑ sensitive:** inferences touching health, disability, finances, immigration or similar. These are the kinds of conclusions real trackers draw, and worth handling deliberately.

---

## 1. Already collected (before entry 003)

_Corrected after a closer read of `index.html`. Everything here is displayed on the page today._

| Data | Source in `index.html` |
|------|------------------------|
| User agent, platform, language(s) | `navigator` |
| CPU threads, device memory, touch points | `hardwareConcurrency`, `deviceMemory`, `maxTouchPoints` |
| Screen size, viewport, pixel ratio, colour depth, refresh rate | `screen`, `innerWidth/Height`, `devicePixelRatio`, `requestAnimationFrame` timing |
| Cookies, Do Not Track, plugins | `navigator` |
| Display preferences | `matchMedia`: dark mode, reduced motion, high contrast, colour gamut, HDR |
| Battery, connection, Save-Data | `getBattery()`, `navigator.connection` |
| Camera / mic / speaker counts | `mediaDevices.enumerateDevices()` |
| Time zone, local time, clock skew | `Intl`, `Date`, `performance.timeOrigin` |
| Referrer, history length, time of visit | `document.referrer`, `history.length`, `Date` |
| WebRTC local IP | `RTCPeerConnection` (usually hidden now) |
| Canvas fingerprint | 2D canvas, last 32 chars of the image data. **Fixed in 004:** now a hash of the full image, read twice to detect randomization |
| Audio fingerprint | `OfflineAudioContext` |
| Fonts (21 tested) | text-width measurement |
| Speech voices (count only) | `speechSynthesis.getVoices()` |
| Ad blocker | bait element |
| Dev tools open | window vs. viewport size |
| API presence | Bluetooth, WebXR, WebAssembly, SharedArrayBuffer, localStorage, IndexedDB |
| Persistent ID, session ID, visit count, referrer | first-party cookies |
| Engagement | GA4 events: scroll depth, clicks, sections viewed, time on page; generic log lines for mouse, copy, tab switch |

**Not actually collected:** the WebGL renderer. The "WebGL renderer 89%" fingerprint bar, like the other bar values, is hard-coded.

**Entry 003** added collection (not display) for everything in §2 and §3 below. Values live in two objects in `index.html`: `X` (passive) and `B` (behavioral).

---

## 2. Passive: readable silently on page load

| Data | What it reveals | Possible inference | Reliability |
|------|-----------------|--------------------|-------------|
| **Client hints** (`navigator.userAgentData.getHighEntropyValues`) | Exact OS version, CPU architecture, device model (Android), full browser version | Windows 10 vs 11 (hidden in the normal UA string); phone model → price bracket; how up to date they keep things | Chromium |
| **Installed voices** (`speechSynthesis.getVoices()`) | List of text-to-speech voices | OS and version; extra language packs installed → languages they speak beyond their browser setting | All (lists differ) |
| **Installed fonts** (measure rendered text widths) | Which fonts exist on the machine | Professional software (Adobe, Office, design tools) → occupation; regional fonts → language/region | All (Safari, Brave limit) |
| **Canvas fingerprint** (draw + hash) | A near-unique ID from rendering quirks | "We can recognize you again without cookies" | Varies (Brave/Firefox randomize) |
| **Audio fingerprint** (`OfflineAudioContext` hash) | Another rendering-quirk ID | Same as canvas; combined → highly unique | Varies |
| **WebGPU adapter** (`requestAdapter().info`) | GPU vendor and architecture | Gaming/workstation vs. basic laptop | Chromium, Safari 26+ |
| **WebGL parameters** (beyond renderer name) | Texture limits, extensions, precision | Sharpens GPU identification and fingerprint | All (Safari generalizes) |
| **Locale details** (`Intl` resolved options) | Calendar, number system, 12/24-hour clock, date format | Region of upbringing vs. current location | All |
| **Time zone vs. language mismatch** | e.g. Spanish browser in a New York time zone | Bilingual, expat, recent move, or VPN use ⚑ | All |
| **More media queries** | Contrast, reduced motion, reduced transparency, forced colors, inverted colors, wide-gamut (P3) / HDR display, pointer precision, hover | Accessibility needs ⚑; premium display → newer/pricier device; mouse vs. trackpad vs. touch | All (some Safari-only) |
| **Screen geometry** (`availWidth/Height` vs. `width/height`, `screenX/Y`, `availLeft/Top`) | Taskbar/dock size and position, window position, whether the window is maximized | Mac vs. Windows habits; second monitor present | Mostly all; `availLeft/Top` Chromium |
| **Multiple screens** (`screen.isExtended`) | Whether more than one monitor is connected | Desk setup, likely at a workstation | Chromium |
| **Storage quota** (`navigator.storage.estimate()`) | Space the browser will grant the site | Rough disk size; private/incognito mode (much lower quota) | Varies |
| **Bluetooth availability** (`navigator.bluetooth.getAvailability()`) | Whether the device has Bluetooth | Desktop vs. laptop; likely wireless accessories | Chromium |
| **Automation flag** (`navigator.webdriver`) | Whether the browser is controlled by software | Bot or scraper vs. human | All |
| **Tracker blocking** (did the GA script load? bait element hidden?) | Ad/tracker blocker present | Privacy-conscious; also a direct "your blocker just blocked our tracker" moment | All |
| **Privacy-browser tells** (inconsistent or randomized values, e.g. canvas hash changes between reads) | Fingerprinting protection active | "Your browser is lying on your behalf" | Varies (that's the point) |
| **Session history length** (`history.length`) | Pages visited in this tab before arriving | Deep browsing session vs. fresh tab | All |
| **Navigation timing** (`performance.getEntriesByType('navigation')`) | Load speed, cache use, how they arrived (link, reload, back button) | Real network quality; returning visit even without `localStorage` | All |
| **CPU benchmark** (time a fixed task) | Actual processing speed | Old vs. new device, power-saving mode | All |

---

## 3. Behavioral: learned from how they act on the page

All of these work in every browser and need no permission. They are what session-replay tools collect on many commercial sites.

| Data | Possible inference |
|------|--------------------|
| **Mouse path, speed, hesitation** | Where attention went; uncertainty; human vs. bot |
| **Scroll speed and reversals** | Skimming vs. reading; which passages they re-read |
| **Reading time per section** | Interest and reading speed |
| **Idle periods** | Distraction; when they stepped away |
| **Tab switching** (`visibilitychange`, `blur/focus`) | Multitasking; how often and how long they left |
| **Text selection and copy** | Exactly which sentences they copied |
| **Window resize** | Device and window habits |
| **Typing rhythm** (only if the page has an input) | Typing-speed signature; can identify individuals |
| **Touch position and pressure** (mobile) | Which hand holds the phone; grip ⚑ (speculative) |
| **Time of day of visit** (local clock) | Late-night browsing; work hours vs. leisure |
| **Device motion/orientation** | Walking, sitting, lying down; Android gives it freely, iOS requires a tap to allow |

---

## 4. Network: needs an outside lookup service

The page is a standalone file, so it can't see the visitor's IP address itself. These options require calling a third-party service. That means sending visitor data to that service, which is worth weighing given the project's message.

| Data | Source | Possible inference |
|------|--------|--------------------|
| **Public IP address** ✓ | IP echo service (e.g. ipify) or a public WebRTC STUN server | The identifier every site already logs |
| **City / region / approx. coordinates** ✓ | IP geolocation service (e.g. ipinfo, ipapi) | Where they are, typically to the city level |
| **Internet provider / organization** ✓ | Same | Home ISP vs. university vs. employer network ("you're browsing from [company]") |
| **VPN / proxy / data-center detection** | Same (often a paid tier) | Hiding location; privacy-conscious |
| **Mobile carrier** | Same | On cellular vs. Wi-Fi |
| **Local weather / daylight** | Weather service using IP location | "It's raining where you are": unsettling specificity from location alone |

✓ = collected since devlog 015, via country.is (recorder `N` in `index.html`; also postal code, time zone and ASN). Not displayed yet. Service choice and later plans in `PRODUCT.md`.

Note: GA4 already receives the visitor's IP and derived location, but those values are not shown back to the site, so the page can't display them.

---

## 5. Permission-gated: the visitor must click "allow"

These change the experience: the page has to ask, and the visitor decides. The request itself could be part of the message.

| Data | What it reveals | Reliability |
|------|-----------------|-------------|
| **Precise location** (`geolocation`) | Coordinates to within meters | All |
| **Camera / microphone** | Device names after granting; live image/audio | All |
| **Local fonts list** (`queryLocalFonts`) | Every installed font, by name | Chromium |
| **Screen details** (`getScreenDetails`) | Each monitor's name, size, position | Chromium |
| **Idle detection** | Whether the user is away from the *whole computer* | Chromium |
| **Clipboard read** | Whatever they last copied, anywhere | Varies |
| **Notifications** | Ability to reach them after they leave | All |
| **Contacts picker** | Chosen contacts | Android Chrome only |

---

## 6. No longer possible (worth knowing)

Browsers closed these off in recent years. Mentioning them could show that protections work.

- **Browsing history sniffing** via visited-link colors (blocked)
- **Local network IP** via WebRTC (now hidden behind random names)
- **Plugin list** (now a fixed, identical list in every browser)
- **Battery and connection data** in Firefox and Safari (removed from those browsers)
