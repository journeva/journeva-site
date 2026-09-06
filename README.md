# journeva.app — the website

Four static pages plus the story-update host, built to be published with **GitHub
Pages** on the custom domain `journeva.app`. No build step, no JavaScript, no
external fonts or scripts — the privacy policy promises that, so keep it true.

| File | Purpose |
|---|---|
| `index.html` | landing page |
| `privacy.html` | privacy policy (App Store "Privacy Policy URL") |
| `support.html` | FAQ + contact (App Store "Support URL") |
| `imprint.html` | Impressum, required for a commercial site in Germany |
| `style.css` | the one stylesheet, light and dark |
| `assets/` | app icon (favicon, touch icon, 512 px for link previews) and four simulator screenshots as 640 px JPEGs — retake them after visible UI changes |
| `CNAME` | tells GitHub Pages the custom domain |
| `.nojekyll` | tells GitHub Pages to serve files as-is |
| `content/` | written by the publish script: `manifest.json`, `content.json`, `images/` |
| `stories/` | **generated** by `ContentStudio/build_story_pages.py` — one page per story per language (A1 text, level previews, links to the app), a language index each, the pictures; plus `sitemap.xml` and `robots.txt` at the root. Never edit by hand; rerun the script after every content batch |

## Publisher details

The legal name, postal address and phone number in `privacy.html` (section
"Who is responsible") and `imprint.html` are filled in (3 September 2026). If any
of them change — a move, a new number, a VAT ID — edit both files; those two
pages are the only places they appear. The same address, phone and
`support@journeva.app` go into App Store Connect's trader information.

## Publishing (once)

The app repository has no GitHub remote, so create a **public** repository for the
site (GitHub Pages on a free account only publishes from public repos):

1. On GitHub: New repository → name `journeva-site` → Public → create.
2. Locally, from this folder:
   ```bash
   cd docs
   git init -b main
   git add .
   git commit -m "Journeva website"
   git remote add origin git@github.com:journeva/journeva-site.git
   git push -u origin main
   ```
3. In the new repo: Settings → Pages → Source **Deploy from a branch** → branch
   `main`, folder `/ (root)` → Save. Under Custom domain, `journeva.app` should
   already appear (from the `CNAME` file); wait for the DNS check, then tick
   **Enforce HTTPS**.

If you'd rather push the whole app repository to GitHub as a public repo, you can
instead point Pages at the `main` branch, folder `/docs`, and skip the copy. Keep
the app repo private? Then the separate site repo is the way.

## DNS at Cloudflare (once)

Cloudflare → journeva.app → DNS → Records. All four records **DNS only** (grey
cloud), not proxied, or GitHub can't issue the certificate:

| Type | Name | Content |
|---|---|---|
| A | `@` | `185.199.108.153` |
| A | `@` | `185.199.109.153` |
| A | `@` | `185.199.110.153` |
| A | `@` | `185.199.111.153` |
| CNAME | `www` | `journeva.github.io` |

Leave the email records (MX, the `_dmarc` TXT, Apple's SPF/DKIM records) exactly as
they are; they don't interact with these. Certificate issuance takes a few minutes
to an hour after the DNS check passes. `https://journeva.app`, `/privacy.html`,
`/support.html` and `/imprint.html` are then live.

## Launch day

The app was submitted to App Review on 2026-09-06 with manual release. When you
press Release in App Store Connect, make these two edits in `index.html` and push:

1. The hero button: change
   `<a class="cta" href="support.html">Coming soon to the App Store</a>` to
   `<a class="cta" href="https://apps.apple.com/app/id6808384465">Download on the App Store</a>`.
2. Optionally add the same link to the `#pricing` section under the plans.
3. In `ContentStudio/build_story_pages.py` set `LAUNCHED = True` and, once App
   Analytics is available (a day or so after release), paste the provider token
   from Analytics → Acquisition → Campaigns into `PROVIDER_TOKEN`; rerun the
   script so every story page's button becomes an attributed App Store link.

The `apple-itunes-app` meta tag on `index.html` and `support.html` is already in
place: once the app is live, Safari on iPhone shows Apple's Smart App Banner at the
top of those pages by itself; before that it shows nothing.

## Updating

This folder is the source of truth; the live site is the separate public repo
`journeva/journeva-site`, cloned at `../../journeva-site` (sibling of the app
folder, set up 2026-09-06 with a repo-scoped deploy key `~/.ssh/id_ed25519_github`,
read/write, no passphrase). To publish, after committing here:

```bash
cd /Users/sheli/Documents/Learning/App/journeva-site && rsync -a --delete --exclude .git --exclude .DS_Store ../Journeva/docs/ ./ && git add -A && git commit -m "Update site" && git push
```

GitHub Pages redeploys within a minute. The `content/` folder is updated by the
publish script the same way (commit here, then the command above), which is how a
new story reaches every installed app without an App Store update.
