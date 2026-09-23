# GHOST ACCESS — GitHub Pages site

GitHub account: **[sathoshimaking](https://github.com/sathoshimaking)**
Site URL: **https://sathoshimaking.github.io**

Four files, three schedules:

| File | What it is | When it goes live |
|---|---|---|
| `index.html` | Placeholder homepage — name and "coming soon" | **Now** |
| `app-ads.txt` | AdMob authorised-sellers list | **Now** — crawling takes time |
| `privacy.html` | The privacy policy | File can be committed now; its **URL goes to App Store Connect** only when the ads/IAP build is submitted |
| `README.md` | This file — not served content | n/a |

The schedules differ on purpose. `app-ads.txt` is harmless early and useless
late. The privacy policy describes collection that does not happen yet, so it
must not be presented as the app's policy until it is true — it carries a
`noindex` tag and a draft banner until then, and nothing links to it.

## The repository name is load-bearing

AdMob fetches exactly `https://<domain>/app-ads.txt` and looks nowhere else. A
GitHub Pages **user site** serves the domain root; a **project site** serves a
subpath, which is never crawled.

```
Repo named  sathoshimaking.github.io   ->  https://sathoshimaking.github.io/app-ads.txt   ✅
Repo named  ghost-access               ->  https://sathoshimaking.github.io/ghost-access/app-ads.txt   ❌ ignored
```

This folder is already named `sathoshimaking.github.io` so the repo name is
obvious when you create it. One user site is allowed per account — if that repo
already exists and hosts something else, add these files to it rather than
making a second one.

## Putting it up

```bash
cd ~/Desktop/sathoshimaking.github.io
git init -b main
git add .
git commit -m "Add placeholder homepage, privacy policy draft, and app-ads.txt"
gh repo create sathoshimaking.github.io --public --source=. --push
```

Then on GitHub: **Settings → Pages → Source: Deploy from a branch → `main` / `/ (root)`**.
Give it a couple of minutes, then confirm:

- `https://sathoshimaking.github.io/` — the placeholder
- `https://sathoshimaking.github.io/app-ads.txt` — must come back as **plain
  text**. GitHub can serve a 404 page with a 200 status, which AdMob reads as a
  malformed file, so look at what actually loads rather than trusting the status.

## Before `app-ads.txt` can verify

Three conditions, all required, all explained inside the file itself:

1. The `pub-XXXXXXXXXXXXXXXX` placeholder is replaced with your real AdMob
   publisher ID (AdMob → Settings → Account information).
2. The file sits at the domain root — see above.
3. `https://sathoshimaking.github.io` is set as the app's **marketing URL** in
   App Store Connect. Google finds the domain *through* the store listing, so a
   blank or mismatched URL means the crawl never starts.

Condition 3 means verification cannot complete until the app is on the store.
Expect "Not found" until then; that is normal, not a fault in the file.

## Before the privacy policy goes to App Store Connect

The page is written for the shipped-with-ads version and marked as a draft. It
carries `<body class="draft">`, which switches on a banner and a yellow note at
every claim that depends on how the SDK ends up configured.

Work through the notes after the AdMob and StoreKit integration is done, then:

1. Change `<body class="draft">` to `<body>` — banner and all notes disappear
   together. The notes stay in the file for the next policy change.
2. Delete the `<meta name="robots" content="noindex">` line in `<head>`.
3. Optionally uncomment the privacy-policy link at the bottom of `index.html`.
4. Set the App Store Connect privacy policy URL to
   `https://sathoshimaking.github.io/privacy.html`.

What each yellow note asks you to confirm:

- [ ] Real "last updated" date, and the app name matching the store listing
- [ ] A contact address you are willing to publish and actually read
- [ ] Section 3 still true — no analytics or crash-reporting SDK has crept in
- [ ] **Non-personalised ads are genuinely what the code requests**, and no ATT
      prompt exists anywhere
- [ ] The consent form really runs before the first ad request
- [ ] The `[ PRIVACY OPTIONS ]` row shipped in Settings, and appears for an
      EEA/UK device — the policy promises consent can be changed later
- [ ] Purchase wording matches the products actually on sale
- [ ] Section 6 agrees with the App Store age rating

The fourth is the one worth being slow about. A policy that says "we do not
personalise" while the code quietly does is the kind of inaccuracy that costs an
App Review rejection at best.

The sixth is specified in sub-phase 2 of the monetization design: a conditional
Settings row, shown only when the UMP SDK reports privacy options are required
and the consent flow has run at least once.

## Related, but not in this folder

App-side work, tracked in the monetization spec — listed here only so the launch
checklist reads in one place:

- `PrivacyInfo.xcprivacy` in the app bundle — required once AdMob ships
- The App Store privacy nutrition label, which must agree with this policy
- EU DSA trader status — declaring as a trader publishes your address and phone
