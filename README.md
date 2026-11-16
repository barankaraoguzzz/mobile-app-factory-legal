# mobile-app-factory-legal

Public legal and support site for **Orvynel Labs** apps (StudyGlow first), hosted on GitHub Pages. Orvynel Labs is
the studio brand of Baran Karaoğuz (Türkiye), not a registered company. Plain HTML and one shared stylesheet: no
JavaScript, cookies, tracking or build step.

> Application source code lives in a separate **private** repository. Never add source code, secrets, SDK keys,
> internal documents or unpublished product plans here.

## Status

StudyGlow documents are **in effect since November 16, 2026** (approved by Baran Karaoğuz). When an app's data
practices or subscription terms change, update its pages in the same change, set a new "Last updated" date, and
keep each statement matching the shipped app and its SDK configuration.

Pages use `body.theme-orvynel` for the studio palette; app pages use the app's own palette (StudyGlow:
Midnight Glow). Both follow the system light/dark appearance.

## Structure

```
index.html                  list of apps
assets/styles.css           shared styles (light/dark from the system appearance)
<app>/index.html            app landing page
<app>/privacy/index.html    privacy policy
<app>/terms/index.html      terms of use
<app>/support/index.html    support and FAQ
.nojekyll                   serve files as-is (no Jekyll processing)
```

All links are **relative** (`../`, `../../assets/styles.css`), so the site works both under the GitHub Pages
project path (`/mobile-app-factory-legal/`) and later at the root of a custom domain, without changes.

## Adding an app

New pages start as drafts: add `<meta name="robots" content="noindex">` and a draft notice until the owner
approves them.


1. Copy `studyglow/` to `<app>/` (lowercase, no spaces) and rewrite every page from that app's actual behavior —
   never copy another app's data practices.
2. Add the app to `index.html`.
3. Use the published URLs in the app's Release configuration (the app's build fails without them).

## Preview locally

```sh
python3 -m http.server 8000
# open http://localhost:8000/studyglow/privacy/
```

## Publish on GitHub Pages (requires the owner's approval)

Run from this directory, signed in to the personal GitHub account `barankaraoguzzz` with GitHub CLI
(`gh auth status`). The repository is public by design; the app repository stays private.

```sh
git init -b main
git add -A
git commit -m "Initial legal and support pages"
gh repo create mobile-app-factory-legal --public --source=. --remote=origin --push

# Enable Pages from the main branch root, then require HTTPS
gh api -X POST repos/barankaraoguzzz/mobile-app-factory-legal/pages -f "source[branch]=main" -f "source[path]=/"
gh api -X PUT repos/barankaraoguzzz/mobile-app-factory-legal/pages -F https_enforced=true

# Wait for the first deployment, then check every page answers 200 over HTTPS
gh api repos/barankaraoguzzz/mobile-app-factory-legal/pages/builds/latest --jq .status
for page in "" studyglow/ studyglow/privacy/ studyglow/terms/ studyglow/support/; do
  curl -s -o /dev/null -w "%{http_code} $page\n" "https://barankaraoguzzz.github.io/mobile-app-factory-legal/$page"
done
```

Expected URLs:

```
https://barankaraoguzzz.github.io/mobile-app-factory-legal/studyglow/privacy/
https://barankaraoguzzz.github.io/mobile-app-factory-legal/studyglow/terms/
https://barankaraoguzzz.github.io/mobile-app-factory-legal/studyglow/support/
```

## Custom domain (later)

Planned: `legal.orvynellabs.com` for these pages (`orvynellabs.com` for the studio site). Domain ownership,
trademark and social handles are **not verified yet** — do not reference these addresses anywhere until the
domain is owned and serving these pages over HTTPS.


1. Add a `CNAME` file containing the domain (e.g. `legal.example.com`).
2. At the DNS provider, add a `CNAME` record pointing that name to `barankaraoguzzz.github.io`.
3. In the repository settings → Pages, set the custom domain and enable "Enforce HTTPS" once the certificate is
   issued.
4. Update the URLs in each app's configuration, then keep the old `github.io` URLs working (GitHub redirects
   them to the custom domain).
