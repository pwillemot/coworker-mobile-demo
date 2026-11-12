# Coworker mobile demo: build notes

What we built for Innovation Day 2026, why, and how to use it on stage.

**Live link:** https://pwillemot.github.io/coworker-mobile-demo/#brief
**Repository:** https://github.com/pwillemot/coworker-mobile-demo (public)
**Local folder:** `InnovationDay2026/coworker-mobile/`

---

## 1. Where it started

The source was the first two slides of the hosted **Coworker: Seller in the Flow of Work** demo:
`https://april-moon-5-55-pm-hey-d-app-…herokuapp.com/demos/03-coworker/demo.html`

| Slide | Original |
|---|---|
| 1 | Mobile "Daily Brief" digest (a newspaper-style briefing inside a phone mockup) |
| 2 | Sales Cloud **desktop** Seller Home (dashboard cards with donut charts) |

**Step 1: rebuild it locally.** We downloaded the page with its CSS, scripts and images and cut it down to slides 1–2. The result is in `InnovationDay2026/coworker-demo/` and runs at desktop size (1280×720). It has to be served over HTTP, because the SVG icons don't load from `file://`.

## 2. Turning it into a mobile app (styled like meeting360)

Model: `claude-projects/Elia/meeting360`. That demo is one self-contained `index.html` that:
- shows a decorative iPhone frame on a laptop,
- goes edge-to-edge on a real phone (`max-width: 600px` / `display-mode: standalone`),
- can be added to the iPhone Home Screen.

We rebuilt both slides from scratch as one HTML file with the same setup:

**Screen 1: Daily Brief.** The same content as the original. It has an "Open in Salesforce" button at the bottom that moves on to screen 2.

**Screen 2: Seller Home, redone as the Salesforce mobile app.** It took the earlier Salesforce mobile app screenshots as its reference:
- a blue header with avatar, the **Sales** app switcher and a "Good morning" greeting
- an **Ask Agentforce Coworker…** bar
- a quick-actions row: Log a Call, New Opportunity, New Event, New Task
- the cards stacked one per row: Close Deals ($9.6M), Today's Events, Plan My Accounts (8), Grow Relationships (17), Build Pipeline (4)
- the SF mobile bottom tab bar: Home, Favorites, Search, Notifications, Menu

**Navigation:** swipe left/right on the phone, ←/→ or Space on a laptop. The **Menu** tab goes back to the brief.

## 3. A "real" Coworker app on the iPhone

To make it feel like a separate app on your phone:
- **App icon:** white sparkles on a blue-to-purple gradient (`assets/icon.svg`, rendered to `icon-180.png` / `icon-512.png`)
- **Web app manifest** (`manifest.webmanifest`): name "Coworker", standalone display, starts on `#brief`
- **iOS meta tags:** `apple-touch-icon`, `apple-mobile-web-app-capable`, app title
- When launched from the icon it **always opens on the Daily Brief**

## 4. Briefing generation screen (~8 seconds) for the talk track

On launch, Coworker "builds" the briefing before showing it:

> **Good morning, Rekha**
> *Coworker is preparing your Daily Brief…*
> ✓ Reviewing opportunities: 3 deals
> ✓ Checking service cases: 1 escalated
> ✓ Scanning your calendar: 2 meetings
> ✓ Reading email & Slack: 14 threads
> ✓ Writing your brief → *Your Daily Brief is ready*

Each step spins, then gets a check mark, and a progress bar fills up. After about 8 seconds the screen fades into the Daily Brief.

- It plays again when you tap **↻ Refresh** in the brief
- **Tap anywhere** to skip it

## 5. Publishing

- A git repo created in `coworker-mobile/` and pushed to `pwillemot/coworker-mobile-demo`
- **GitHub Pages** enabled on `main` / root, so the link is a fixed `https://` URL that works without the Mac
- Checked before publishing: every file the page uses is present, it loads nothing from other sites, the manifest is valid, the JavaScript has no syntax errors, and there are no secrets in it

---

## How to use it

### On the iPhone
| Way | How |
|---|---|
| **Moshi** | Add the URL `https://pwillemot.github.io/coworker-mobile-demo/#brief` |
| **Coworker icon** | Open the link in **Safari** → Share → **Add to Home Screen** → Add |

### URL options
| Parameter | Effect |
|---|---|
| `#brief` | start on the Daily Brief |
| `#home` | start on Seller Home (skips the generation screen in the browser) |
| `?name=Patrice` | replaces "Rekha" everywhere (remembered on the device) |
| `?gen=10` | generation screen lasts 10 seconds (default 8) |
| `?nosplash` | no generation screen |

Example: `https://pwillemot.github.io/coworker-mobile-demo/?name=Patrice&gen=10#brief`

### Suggested talk-track flow
1. **Tap the Coworker icon.** Over the ~8s generation screen: *"Before I've even had my coffee, Coworker has already gone through my opportunities, cases, calendar, email and Slack…"*
2. **Daily Brief.** Scroll down: $950K closing at United Oil & Gas, three deals this week, an escalated billing case before the conversations happen.
3. **Tap "Open in Salesforce"** (or swipe left) to get to **Seller Home**, the Salesforce mobile app with the Ask Agentforce Coworker bar in the flow of work.
4. To show it again: tap **↻ Refresh** in the brief.

---

## Files

```
coworker-mobile/
├── index.html             the whole app (HTML + CSS + JS, no dependencies)
├── manifest.webmanifest   PWA manifest (name, icon, start URL)
├── README.md              short usage notes
├── BUILD-NOTES.md         this document
└── assets/
    ├── avatar.png         Rekha's avatar (from the original demo)
    ├── icon.svg           app icon source
    ├── icon-180.png       iOS Home Screen icon
    └── icon-512.png       manifest / launch icon
```

## Making changes

1. Edit `coworker-mobile/index.html`
2. Test locally: `cd coworker-mobile && python3 -m http.server 8766` → http://localhost:8766
3. Publish: `git add -A && git commit -m "…" && git push` (GitHub Pages updates within about a minute)
4. On the iPhone: fully close the app/Moshi and reopen it to get the new version

**Common tweaks in `index.html`:**
- Generation steps and counts: the `<ul class="sp-steps">` list
- Default duration: `var DUR = (… || 8) * 1000`
- Briefing text: the `<section id="s-brief">` block
- Seller Home cards: the `<section id="s-home">` block

## Notes
- All data is fictional (United Oil & Gas Corp, Rekha, Sarah Chen…). Not connected to an org.
- Dates follow **today's date** automatically, so the brief always reads as "this week".
- The status bar clock shows the real time.
- The repository is **public**. Don't add real customer data.
