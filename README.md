# Vilarci E-Split website

The website at **https://esplit.vilarci.in** for the Vilarci E-Split app
(package `in.vilarci.expensesplit`, app repo `vilarci-expenses-calculator`).
Plain static files, no build step. Everything served lives in `public/`.

| File | What it is |
|---|---|
| `public/index.html` | The landing page, and the invite page for `/join/<code>` (also `/?c=<code>`) |
| `public/404.html` | "Page not found" for any other address (real 404 status) |
| `public/style.css` | Styles shared by every page (light and dark) |
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

## Planned pages

- `/privacy`: privacy policy (needed before accounts ship, app 1.4.0).
- `/delete-account`: how to delete your account (required by Google Play
  for apps with accounts).
