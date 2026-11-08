# Vilarci E-Split website

The website at **https://esplit.vilarci.in** for the Vilarci E-Split app
(package `in.vilarci.expensesplit`, app repo `vilarci-expenses-calculator`).
Plain static files, no build step. Everything served lives in `public/`.

| File | What it is |
|---|---|
| `public/index.html` | The landing page, and the invite page for `/join/<code>` (also `/?c=<code>`) |
| `public/.well-known/assetlinks.json` | Lets Android open `esplit.vilarci.in` links straight in the app (App Links) |
| `public/_headers` | Cloudflare Pages headers (JSON type for assetlinks, basic safety headers) |
| `public/icon-512.png`, `icon-192.png`, `favicon.png` | Icons, made from the app's `assets/icon.png` |

## Rules

- **No `404.html` in `public/`.** Without one, Cloudflare Pages serves
  `index.html` for every path, which is how `/join/<code>` reaches the
  invite page. Adding a 404 page breaks invite links.
- Invite codes are 8 characters from `23456789ABCDEFGHJKLMNPQRSTUVWXYZ`
  (no 0/O or 1/I). The app must use the same rule.
- `assetlinks.json` must list the SHA-256 of **both** signing keys: the
  upload key (already in) and the **Play App Signing key** (Play Console →
  Test and release → App integrity → App signing). Without the Play one,
  apps installed from Play open this page instead of the app.

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

## Planned pages

- `/privacy`: privacy policy (needed before accounts ship, app 1.4.0).
- `/delete-account`: how to delete your account (required by Google Play
  for apps with accounts).
