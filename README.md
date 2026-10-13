# M&M Nursery — Website + Quote Request System

Website for M&M Nursery (Homestead, FL) with a working **Request a proposal** form, a database,
and a private **admin panel** to price requests and send proposals.

**No packages to install.** It runs on plain Node.js (version 22.13 or newer).

## What's included

| Part | Where | What it does |
|---|---|---|
| Website | `/` | Bright, mobile-first homepage: hero, ways to buy (Plant Sales, Residential, Wholesale), benefits, 3 steps, About, Delivery, Gallery, contact band, request form, contact + Google map, footer |
| Request form | `/#quote` | Short form: name, phone, email, plant/product, quantity, pickup or delivery, message. Buttons like "Check Availability" and "Ask About Delivery" preselect the request type |
| Mobile bar | phones | Call · Text/WhatsApp (when turned on) · Get a Quote, always at the bottom of the screen |
| Admin panel | `/admin` | Password-protected list of all requests, with status, search and CSV export |
| Pricing + proposal | Admin → open a request | Enter prices; the total calculates itself. "Open proposal" gives a printable proposal (English or Spanish) |
| SEO | automatic | Page title, description, local-business data (schema.org), `/sitemap.xml`, `/robots.txt`, image alt text |

## Three ways to use the website

| Option | What it is | Request form |
|---|---|---|
| **Shared link** (Claude page) | Ready to send by WhatsApp. Open it, tap **Share**, turn on the public link | Builds the customer's message; they send it by email or copy it |
| **index.html** (one file) | Open it or upload it to any web host. Put photos in an `images` folder next to it | Opens the customer's email app with the request filled in |
| **Full project** (this folder) | Website + database + admin panel. Needs a Node.js host (see "Put it online") | Saves every request to the admin panel |

**Quick free hosting for index.html:** go to app.netlify.com/drop and drag in a folder that contains
`index.html` and your `images` folder. You get a link like `mm-nursery.netlify.app` to share.

## site.json — the business facts

Everything the website says about the business comes from `site.json`. The site only shows what is
filled in, so nothing is invented. Edit it, then restart the server.

Currently hidden because the information was not provided yet:

- `hoursDays` — which days the 7 AM – 5 PM hours apply (e.g. "Monday – Saturday")
- `textMessages` / `whatsapp` — turn on the Text and WhatsApp buttons (only if that number takes texts / uses WhatsApp)
- `deliveryArea` — until filled, the site says "Ask about delivery" instead of promising delivery
- `social` — Facebook, Instagram, TikTok, Google Business links
- `categories` — plant categories (e.g. Palms, Shrubs). Add only what the nursery really sells; each gets a photo card
- `reviews` — real customer reviews only
- `siteUrl` — your domain, used for search engines and link previews

## Logo

Your current logo wasn't provided, so the site uses a simple interim text logo (leaf mark + "M&M Nursery").
To use your real logo, save it as `public/images/brand/logo.svg` (or `.png`); it replaces the text logo
automatically. Interim logo files for social media and printing are in `public/images/brand/`.

## Run it on your computer

1. Install Node.js 22 LTS or newer from https://nodejs.org
2. In this folder, copy `.env.example` to `.env` and set `ADMIN_PASSWORD` and `SESSION_SECRET`.
3. Run:
   ```
   npm start
   ```
4. Open http://localhost:3000 (website) and http://localhost:3000/admin (admin).

## Add your photos

Put photos in `public/images/` with the names listed in `public/images/PUT-YOUR-PHOTOS-HERE.txt`
(`hero.jpg`, `offer-plant-sales.jpg`, `offer-residential.jpg`, `offer-wholesale.jpg`, `about.jpg`,
`gallery-1.jpg` … `gallery-8.jpg`). They appear automatically, within 10 seconds. No code changes needed.

## Put it online

The site stores requests in a small database file, so the host must keep files between restarts
("persistent disk" or "volume").

**Render (simple):**
1. Upload this folder to a GitHub repository.
2. On render.com: New → Web Service → pick the repo.
   - Build command: *(leave empty)* · Start command: `npm start`
   - Add a **Disk** (paid Starter plan; the free plan erases files), mount path `/var/data`
3. Environment variables: `ADMIN_PASSWORD`, `SESSION_SECRET`, `DB_PATH=/var/data/nursery.db`, `COOKIE_SECURE=true`
4. Add your domain (e.g. mmnursery.com) under Settings → Custom Domains.

**Railway:** same steps; add a Volume mounted at `/data` and set `DB_PATH=/data/nursery.db`.

**Your own server (VPS):** install Node 22, copy the folder, create `.env`, then run with a process
manager such as `pm2 start server.js --name mm-nursery` behind Nginx or Caddy for HTTPS.

## Email alerts (optional)

1. Create a free account at resend.com and verify your domain.
2. In `.env` set `RESEND_API_KEY`, `EMAIL_FROM` (an address on your verified domain) and `NOTIFY_EMAIL`.
Without these, requests are still saved and shown in the admin panel.

## Backups

All requests are in one file: `data/nursery.db` (or your `DB_PATH`). Copy it somewhere safe now and
then, or use "Export CSV" in the admin panel.

## Security built in

- Admin password checked on the server; login is rate-limited and kept in a signed, HTTP-only cookie
- Form spam protection (hidden honeypot field + rate limit: 5 requests per 15 minutes per visitor)
- Input validated and size-limited on the server; safe from SQL injection (prepared statements)
- Security headers and a strict Content Security Policy
- Admin pages are hidden from search engines

## Files

```
site.json            Business facts (source of truth for the website)
server.js            Web server, API, admin login, proposal page, sitemap
src/render.js        Builds the homepage from site.json and your photos
src/db.js            Database (SQLite, built into Node)
src/views/           Admin login + admin panel pages (served only through /admin)
public/css/site.css  Website styles
public/js/main.js    Menu and request form
public/admin/        Admin styles and scripts
public/images/       Your photos go here
data/                Database file (created automatically)
```
