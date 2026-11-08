# Vilarci E-Split website

The website at **https://esplit.vilarci.in** for the Vilarci E-Split app
(package `in.vilarci.expensesplit`, app repo `vilarci-expenses-calculator`).
Plain static files, no build step. Everything served lives in `public/`.

| File | What it is |
|---|---|
| `public/index.html` | The landing page, and the invite page for `/join/<code>` (also `/?c=<code>`). Fonts: Instrument Serif + Hanken Grotesk from Google Fonts (only downloaded on the landing) |
| `public/404.html` | "Page not found" for any other address (real 404 status) |
| `public/confirmed.html` | `/confirmed`: where the "confirm your email" link lands (account sign-up) |
| `public/reset.html` | `/reset`: set a new password from the reset email (uses supabase-js 2.119.0 from jsDelivr and the app's publishable key) |
| `public/privacy.html` | `/privacy`: privacy policy. **Draft:** fill in `[contact email]` before publishing |
| `public/delete-account.html` | `/delete-account`: how to delete an account (Google Play needs this link). **Draft:** same placeholder |
| `public/style.css` | Styles shared by every page (light and dark) |
| `public/home.css` | The landing page's own styles (only `index.html` loads it). Everything is scoped to `.lp`; `html.invite` (set in `index.html`'s `<head>` for `/join/<code>` and `?c=`) hides the landing and shows the invite card from `style.css` |
| `public/_redirects` | Serves `index.html` for `/join/<code>`, keeping the address |
| `public/.well-known/assetlinks.json` | Lets Android open `esplit.vilarci.in` links straight in the app (App Links) |
| `public/_headers` | Cloudflare Pages headers (JSON type for assetlinks, basic safety headers) |
| `public/icon-512.png`, `icon-192.png`, `favicon.png` | Icons, made from the app's `assets/icon.png` |

## Rules

- **Keep the `/join/* / 200` line in `_redirects`.** Because `404.html`
  exists, Pages no longer serves `index.html` for unknown paths by itself;
  that line is what makes invite links work. Its target must be `/`, not
  `/index.html` (Pages redirects `/index.html` to `/`, which would drop the
  code from the address).
- Invite codes are 8 characters from `23456789ABCDEFGHJKLMNPQRSTUVWXYZ`
  (no 0/O or 1/I). The app must use the same rule.
- `assetlinks.json` lists the SHA-256 of **both** signing keys, and both
  must stay:
  - `02:B5:…:21:3A`: the **Play App Signing key**, which signs apps
    installed from Google Play (Play Console → Protected with Play → Play
    Store protection → Play App Signing);
  - `37:E9:…:96:FF`: the **upload key**, which signs builds installed
    straight from the developer's PC.
  Without the Play one, apps installed from Play open this page instead of
  the app.

## Cloudflare Pages (one-time setup)

1. Workers & Pages → Create → **Pages** → Connect to Git → this repo.
2. Production branch `main`, framework preset **None**, build command
   **empty**, build output directory **`public`**. Save and Deploy.
3. Custom domains → Set up → `esplit.vilarci.in`. vilarci.in's DNS is on
   Cloudflare, so the `esplit` record is added automatically. Wait for
   "Active".
4. Check:
   - `https://esplit.vilarci.in/join/K7QM2XPA` shows the code `K7QM-2XPA`;
   - `https://esplit.vilarci.in/.well-known/assetlinks.json` returns JSON
     (not the HTML page).

Every push to `main` redeploys the site.

## Try it locally

`npx wrangler@4.20.0 pages dev public --port 8788` runs Cloudflare's own
Pages server (`_redirects`, `_headers` and the 404 page behave as live).
Open http://127.0.0.1:8788/join/K7QM2XPA. It leaves `.wrangler/` and
`node_modules/` behind; delete them (stop the server first).

VS Code Live Server works for a quick look at the landing page:
`.vscode/settings.json` sets its root to `/public`, so open
http://127.0.0.1:5501/ (not `/public/index.html`). The pages link
`/style.css` etc. from the site root, so any server must serve `public/`
as the root. Live Server doesn't read `_redirects`, so use `?c=K7QM2XPA`
there for the invite card.

## Planned pages

- `/privacy`: privacy policy (needed before accounts ship, app 1.4.0).
- `/delete-account`: how to delete your account (required by Google Play
  for apps with accounts).
