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

## Updating

Edit the file, commit, push. GitHub Pages redeploys within a minute. The
`content/` folder is updated by the publish script the same way (commit + push),
which is how a new story reaches every installed app without an App Store update.
