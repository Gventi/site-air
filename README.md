# ingestair.space — the Ingest Air site

Everything here is self-contained: four static pages, two images, no build step, no
external requests (no CDNs, no fonts, no analytics). Drop it on GitHub Pages and it
works.

```
index.html      landing page
privacy.html    privacy policy   ← App Store Connect requires this URL
support.html    support page     ← App Store Connect requires this URL
terms.html      terms of use     (optional; Apple's standard EULA also applies)
CNAME           ingestair.space
assets/         icon.png, screenshot.png
```

## 1. Deploy

The main `ingest` repo already uses GitHub Pages for the *other* product, and a repo can
only carry one custom domain — so Ingest Air needs its **own repo**.

```bash
# from the repo root
cd site-air
git init
git add .
git commit -m "Ingest Air site"
git branch -M main
git remote add origin https://github.com/Gventi/ingestair.git   # create this repo first
git push -u origin main
```

Then in that repo: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.

Only the contents of `site-air/` go in that repo. The app source stays private in the
main repo.

## 2. Point the domain at it

`CNAME` is already set to `ingestair.space`. Add these records at your registrar:

| Type | Name | Value |
|------|------|-------|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| AAAA | `@` | `2606:50c0:8000::153` |
| AAAA | `@` | `2606:50c0:8001::153` |
| AAAA | `@` | `2606:50c0:8002::153` |
| AAAA | `@` | `2606:50c0:8003::153` |
| CNAME | `www` | `gventi.github.io` |

DNS takes anywhere from minutes to a few hours. Once it resolves, tick
**Settings → Pages → Enforce HTTPS**. Apple requires the privacy policy URL to be
reachable over HTTPS, so don't submit before that certificate is issued.

## 3. Set up the support email

Both the privacy policy and the support page point at **support@ingestair.space**, and
App Store review will check that the support URL is real. Set up forwarding for that
address at your registrar (most `.space` registrars include free email forwarding) and
point it at whichever inbox you actually read.

If you would rather use a different address, change it in `privacy.html`,
`support.html` and `terms.html` — it appears once in each.

## 4. Before you submit to App Store Connect

Already handled in the app and this site:

- [x] Privacy policy URL — `https://ingestair.space/privacy.html`
- [x] Support URL — `https://ingestair.space/support.html`
- [x] Marketing URL — `https://ingestair.space/`
- [x] App icon with **no alpha channel** at 1024×1024 (App Store Connect rejects
      transparency outright) — see `Scripts/generate_air_icon.swift`
- [x] Full macOS icon ladder, 16pt through 512@2x
- [x] `ITSAppUsesNonExemptEncryption = false` — no export-compliance prompt per upload
- [x] `LSApplicationCategoryType` set to Productivity
- [x] App Sandbox enabled with only `files.user-selected.read-only` and
      `files.bookmarks.app-scope`; **no network entitlement at all**

Still to do, and only you can do these:

- [ ] Create the app record in App Store Connect (bundle ID `com.garnetlyndon.IngestAir`)
- [ ] **App Privacy questionnaire**: answer **"Data Not Collected"**. Every section.
      That is accurate — the app cannot make a network request. This is the section
      most likely to get flagged if answered carelessly.
- [ ] Screenshots at the required sizes: 6.9" iPhone, 13" iPad, and 1280×800 or larger
      for Mac. A Simulator screenshot is acceptable.
- [ ] Age rating questionnaire (nothing here should trigger a rating above 4+)
- [ ] Set price (or Free) and territories
- [ ] Archive and upload a Release build, with a distribution certificate and
      provisioning profile for the App Store
- [ ] Once live, replace `href="#coming-soon"` in `index.html` with the App Store URL
      and swap the placeholder button for Apple's official
      [App Store badge](https://developer.apple.com/app-store/marketing/guidelines/),
      which Apple requires be used unmodified

### A note for the review team

Reviewers sometimes flag AI features. Two things worth putting in the review notes:

1. All processing uses **Apple's own on-device foundation model** (FoundationModels).
   No third-party model, no server.
2. The app **has no network entitlement**, so it cannot transmit user content anywhere.

The app also needs Apple Intelligence enabled on the review device — if it is off, the
app shows an explanatory banner rather than failing, but summarizing will not work. It
is worth saying so explicitly in the notes so a reviewer doesn't file it as a bug.

## Refreshing the screenshot

`⌘⇧4` then Space, click the Ingest Air window, and save over `assets/screenshot.png`.

## Regenerating the icon

```bash
swift Scripts/generate_air_icon.swift   # from the repo root
```

Writes the whole icon set into the asset catalog *and* refreshes `assets/icon.png` here.
