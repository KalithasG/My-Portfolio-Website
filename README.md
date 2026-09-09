# Kalithas Govindaraj — Portfolio

Single-page portfolio site, deployed on **Firebase Hosting**.

The whole site is one self-contained file: `public/index.html` (~616 KB, with all 23
screenshots embedded as base64 WebP). There is no build step — what is in `public/`
is what gets served.

```
public/
  index.html   the site
  404.html     not-found page, matching the site's Material 3 theme
firebase.json  hosting config: caching, security headers
.firebaserc    which Firebase project to deploy to
.github/workflows/firebase-hosting.yml   auto-deploy on push to main
```

## First-time setup

You need the Firebase CLI and a Firebase project.

```bash
npm install -g firebase-tools
firebase login
```

Point the repo at your Firebase project — this rewrites `.firebaserc`, replacing the
`YOUR_FIREBASE_PROJECT_ID` placeholder:

```bash
firebase use --add        # pick your project, alias it "default"
```

If you have not created a project yet:

```bash
firebase projects:create kalithas-portfolio   # pick any unused project id
firebase use kalithas-portfolio
```

## Deploy

```bash
firebase deploy --only hosting
```

Your site goes live at `https://<project-id>.web.app` and `https://<project-id>.firebaseapp.com`.

Preview a change before it goes live:

```bash
firebase hosting:channel:deploy preview   # temporary URL, expires in 7 days
```

Serve locally with the real hosting config (headers, 404 handling):

```bash
firebase emulators:start --only hosting    # http://localhost:5000
```

## Automatic deploys from GitHub

`.github/workflows/firebase-hosting.yml` deploys every push to `main` to the live
channel, and posts a preview URL on each pull request. It needs two things set on the
repo (Settings → Secrets and variables → Actions):

| Name | Kind | Value |
| --- | --- | --- |
| `FIREBASE_SERVICE_ACCOUNT` | Secret | The full JSON of a service-account key with the *Firebase Hosting Admin* role |
| `FIREBASE_PROJECT_ID` | Variable | Your Firebase project id |

The easiest way to generate the secret is to let the CLI do it — run this in the repo
and it creates the service account and pushes the secret to GitHub for you:

```bash
firebase init hosting:github
```

Otherwise, create a key manually in Google Cloud Console → IAM & Admin → Service
Accounts, and paste the entire JSON file contents as the secret value.

## Custom domain

Firebase Console → Hosting → Add custom domain, then add the DNS records it gives you
at your registrar. Certificates are provisioned automatically.

## Notes

- **The resume link is not wired up yet.** Both Resume buttons point at
  `./Kalithas-Govindaraj-Resume.pdf`, which is not in the repo — they currently 404.
  Drop the PDF at `public/Kalithas-Govindaraj-Resume.pdf` (exact filename) and it will
  work. It is already covered by the one-year cache header.
- **Icons depend on Google Fonts.** Icons are Material Symbols ligatures, so if
  `fonts.googleapis.com` is unreachable for a visitor, icon names render as literal
  text (`arrow_forward`, `mail`) instead of glyphs. Self-hosting the icon font under
  `public/` would remove that dependency.
- **Caching.** HTML is served `must-revalidate`, so a deploy is visible immediately.
  Static assets get a one-year immutable cache.
- **Security headers** (CSP, `nosniff`, `Referrer-Policy`, `X-Frame-Options`,
  `Permissions-Policy`) are set in `firebase.json`. The CSP allows the inline styles
  and scripts the page relies on, plus Google Fonts — it was verified against the live
  page with zero violations. If you add a third-party script, widen `script-src` or it
  will be blocked.
