# Story bundles

This folder is written by the ContentStudio publish script (session 3), never by hand:

- `manifest.json` — `{ schemaVersion, contentVersion, generatedAt, bundleURL, sha256, imageBaseURL, minAppVersion }`
- `content.json` — the full story library and glossary (same shape as the file inside the app)
- `images/<translationGroup>.jpg` — 600×600 pictures for stories published after the last App Store release

The app reads `https://journeva.app/content/manifest.json` (once `ContentEndpoint.manifestURL` is set) and only downloads `content.json` when `contentVersion` is higher than what it has.
