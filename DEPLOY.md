# Deploy: Cloudflare Pages + myrecourse.in (registered at Hostinger)

One-time setup, then every `git push` auto-deploys.

## 1. Push to GitHub

```bash
cd /Users/siddharthagrawal/myrecourse-site
git init
git add .
git commit -m "Initial marketing site"
gh repo create myrecourse-site --public --source=. --remote=origin --push
```

If you prefer a private repo, swap `--public` for `--private`.

## 2. Connect to Cloudflare Pages

1. Go to <https://dash.cloudflare.com/> → **Workers & Pages** → **Create → Pages → Connect to Git**.
2. Authorise your GitHub, pick `myrecourse-site`.
3. Build settings:
   - **Framework preset:** Astro
   - **Build command:** `npm run build`
   - **Build output directory:** `dist`
   - **Root directory:** *(leave blank)*
   - **Node version:** 20 or higher (set env var `NODE_VERSION=20` if needed)
4. **Save and Deploy.** First build takes ~1 minute. You'll get a preview URL like `myrecourse-site.pages.dev`.

Any `git push` to `main` from now on triggers a production deploy.

## 3. Attach myrecourse.in

You have two paths. **Option A is strongly recommended** — Cloudflare handles DNS + SSL + CDN automatically, and Pages custom domains only take a couple of minutes.

---

### Option A (recommended): move DNS to Cloudflare

Cloudflare becomes your DNS provider. The domain is still *registered* at Hostinger — only nameservers change.

**Step 1. Add site to Cloudflare.**

1. In Cloudflare dashboard → **Websites → Add a site**.
2. Enter `myrecourse.in`. Pick the **Free** plan.
3. Cloudflare scans existing DNS records (usually none, since Hostinger hosts the domain but you haven't pointed it anywhere yet).
4. Cloudflare gives you **two nameservers** (looks like `xxx.ns.cloudflare.com` / `yyy.ns.cloudflare.com`). **Copy both.**

**Step 2. Change nameservers at Hostinger.**

1. Log in to Hostinger → **Domains** → **myrecourse.in** → **DNS / Nameservers**.
2. Choose **Change nameservers → Use custom nameservers**.
3. Paste the two Cloudflare nameservers. Save.
4. Propagation: usually 5–60 minutes, occasionally up to 24h.

**Step 3. Attach domain in Cloudflare Pages.**

1. Cloudflare dashboard → **Workers & Pages → myrecourse-site → Custom domains → Set up a custom domain**.
2. Enter `myrecourse.in`. Cloudflare auto-creates the required CNAME/AAAA records in your DNS.
3. Repeat for `www.myrecourse.in` (Pages will auto-redirect it to the apex).
4. SSL certificate is issued automatically within a few minutes.

Done. `https://myrecourse.in` and `https://www.myrecourse.in` both resolve to the site.

---

### Option B: keep DNS at Hostinger

Only pick this if you have a reason to keep DNS on Hostinger (e.g. you have MX records already pointing elsewhere and don't want to migrate them).

1. Cloudflare Pages → **Custom domains → Set up a custom domain** → enter `myrecourse.in`. Cloudflare will show you the target hostname (e.g. `myrecourse-site.pages.dev`) and ask you to add DNS records at your DNS provider.
2. In Hostinger DNS Zone Editor for `myrecourse.in`:
   - Add a **CNAME** record: name `www`, target `myrecourse-site.pages.dev`, TTL default.
   - For the apex (`@`), Hostinger supports **CNAME flattening / ALIAS** on most plans — add an ALIAS/ANAME record: name `@`, target `myrecourse-site.pages.dev`. If your plan doesn't support ALIAS at apex, you'll need to either upgrade or switch to Option A.
3. Back in Cloudflare Pages, wait for the domain to show **Active** (status polls automatically).

---

## 4. Verify

- `https://myrecourse.in` — should load the site.
- `https://www.myrecourse.in` — should redirect to apex.
- View source → check `<meta name="description">` and OG tags.
- Run [PageSpeed Insights](https://pagespeed.web.dev/) — should score 95+ everywhere on desktop.
- Test on mobile (real device, not just DevTools).

## 5. Ongoing

- **Edit copy:** modify `src/pages/*.astro`, `git push`, auto-deploys.
- **Add a page:** create `src/pages/new-page.astro` extending `Base`, add link in `Header.astro` nav array.
- **Preview branches:** every non-main branch and PR gets its own preview URL (e.g. `feature-x.myrecourse-site.pages.dev`) — great for reviewing before merging.
- **Rollback:** Cloudflare Pages → project → **Deployments** → pick a previous deploy → **Rollback**.

## 6. When the app moves to app.myrecourse.in

Once you point `app.myrecourse.in` at the Vercel app:

1. In Cloudflare DNS, add a CNAME: name `app`, target `cname.vercel-dns.com`, proxy status **DNS only** (grey cloud).
2. In Vercel project → **Settings → Domains → Add `app.myrecourse.in`**. Vercel will verify via the CNAME.
3. Find-and-replace `rental-recourse.vercel.app` → `app.myrecourse.in` in `src/`, commit, push.
