# myrecourse.in — marketing site

Static marketing site for [Recourse](https://rental-recourse.vercel.app) at
[myrecourse.in](https://myrecourse.in). Built with Astro 5 + Tailwind v4, deployed on Cloudflare Pages.

## Structure

```
src/
  layouts/Base.astro       # <html> shell, header + footer, SEO tags
  components/Header.astro
  components/Footer.astro
  pages/
    index.astro            # /
    how-it-works.astro     # /how-it-works/
    coverage.astro         # /coverage/
    faq.astro              # /faq/
    about.astro            # /about/
    privacy.astro          # /privacy/
  styles/global.css        # Tailwind v4 theme + resets
public/
  favicon.svg
  robots.txt
```

## Local dev

```bash
npm install
npm run dev        # http://localhost:4321
npm run build      # static output → dist/
npm run preview    # serve dist/ locally
```

## Editing content

- Marketing copy lives inline in the `src/pages/*.astro` files.
- Global chrome (nav links, footer, brand) is in `src/components/Header.astro` and `Footer.astro`.
- Colors, fonts, and design tokens are in `src/styles/global.css` under `@theme`.

Every page links to the app at `https://rental-recourse.vercel.app/intake`.
When you switch the app to a subdomain like `app.myrecourse.in`, do a
find-and-replace on `rental-recourse.vercel.app` across `src/`.

## Deploy

See `DEPLOY.md` for the full Cloudflare Pages + Hostinger DNS walkthrough.

## License

All rights reserved.
