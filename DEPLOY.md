# Deploying khpathways.com on Cloudflare

Checked against Cloudflare's documentation in October 2026 (sources at the bottom). Cloudflare renames dashboard items often, so if a button name differs, look for the closest match. Where I could not verify something, it is marked **[verify]**.

## The short version

1. **Right now (no domain changes, no risk to the Wix site):** create a free Cloudflare account, connect this GitHub repo, and get a test address like `khpathways.<something>.workers.dev`. Part 1 below.
2. **Then a decision about the domain.** Cloudflare needs to control `khpathways.com`'s DNS, and Wix may not allow that. Part 2 explains the two routes. Check one thing in Wix first.
3. **Only after the new site is live and checked on your real domain:** cancel the Wix site plan. Do not cancel it earlier.

## Why Workers and not Pages

Cloudflare's own Pages page now says: "Workers supports most Pages use cases and offers a broader feature set... Start new projects with Workers." So this site is set up as a Cloudflare **Worker with static assets**. There is no Worker code. The file `wrangler.jsonc` in the repo just tells Cloudflare to serve the `public/` folder. It sets two things:
- `html_handling: "drop-trailing-slash"`: `/services` is served directly (as on your Wix site) and `/services/` redirects to `/services`. Without this, Cloudflare would redirect `/services` to `/services/` and your URLs would change.
- `not_found_handling: "404-page"`: unknown URLs show `public/404.html` with a real 404 status.

I tested this config locally with Wrangler (Cloudflare's tool): the five pages return 200, `/services/` redirects to `/services`, unknown paths return 404, and the security headers in `public/_headers` are applied.

The free plan covers this: requests for static files are free and unlimited, and builds get 3,000 minutes per month.

---

## Part 1: Put the site on a test address

**Before you start:** merge pull request #1 into `main`. Cloudflare deploys from `main`.

1. **Create an account.** Go to https://dash.cloudflare.com/sign-up and sign up with your email. Verify the email. Choose the **Free** plan whenever asked.
2. **Connect the repo.** In the dashboard, go to **Workers & Pages** → **Create application** → next to **Import a repository** choose **Get started**.
3. Choose your **GitHub** account. If asked to install the Cloudflare GitHub app, allow access to **only** `kyhuber/khpathways` (not all repos).
4. Select the `khpathways` repository.
5. **Settings** (use these exactly):
   - **Project / Worker name:** `khpathways`. It **must match** the `name` in `wrangler.jsonc`, or the build fails.
   - **Production branch:** `main`.
   - **Build command:** leave **empty**.
   - **Deploy command:** `npx wrangler deploy` (the default).
   - **Root directory:** leave empty.
6. Click **Save and Deploy**. When the build finishes, Cloudflare shows a `*.workers.dev` address.
7. **Test it** on that address:
   - Every page loads: `/`, `/financial-confidence`, `/services`, `/about`, `/disclaimer`.
   - The logo, photos and favicon show.
   - The booking button opens your calendar in a new tab.
   - The email links open your mail app.
   - A made-up address like `/nope` shows your "Page not found" page.
8. From now on, **every push to `main` redeploys automatically.** Pushes to other branches create preview versions, not the live site.

Your Wix site is untouched and still live at this point.

---

## Part 2: Move the real domain (`khpathways.com`)

### What Cloudflare requires

- To attach a custom domain to a Worker, the domain must be an **active zone on Cloudflare**. That means its **nameservers point to Cloudflare**. Cloudflare's docs: "To add a Custom Domain, you must have: An active Cloudflare zone."
- You cannot create a Custom Domain on a hostname that already has a CNAME record.
- Moving only a `www` CNAME (leaving DNS elsewhere) is not an option for a Worker, and the bare domain (`khpathways.com`) can't be done that way either.

**My recommendation: move DNS to Cloudflare** (the nameserver route). It is the only approach that gives you the custom domain, HTTPS, and the bare-domain-to-`www` redirect all in one place, for free.

### The Wix problem (check this first)

Your domain is registered at Wix. Cloudflare's documentation says platforms like Wix "typically do not allow you to change nameservers while the domain is registered with them." Wix's own help pages, as summarized in search results, say the same (I could not open Wix's help site from here, so **[verify]**).

**Do this check, which takes two minutes:** in Wix, open **Domains** → `khpathways.com` → look for **Name servers** (it may be under **Advanced**). Note whether you can *edit* the nameservers.

- **You can edit nameservers → Route A.**
- **You cannot → Route B.**

Also while you're in Wix, note: (a) the domain's **registration date** and whether you changed its **contact details** in the last 60 days, and (b) every record on the **DNS** tab, especially any `MX` or `TXT` records (email or Google verification). Screenshot them.

### Route A: Wix lets you change nameservers

1. In Cloudflare: **Domains** → **Onboard a domain** → enter `khpathways.com` → choose the **Free** plan.
2. Cloudflare scans for existing DNS records. **Compare them to your Wix screenshots** and add anything missing, especially `MX`/`TXT` records if you use email at the domain. If you only use `khpathways@gmail.com` and nothing else, you may have no email records.
3. If **DNSSEC** is on at Wix, turn it off first and wait 24 hours. Leaving it on can make the domain unreachable.
4. At Wix, replace the nameservers with the two Cloudflare gives you (shown on the zone Overview page). Copy them exactly.
5. Wait for the domain to show **Active** in Cloudflare (usually minutes, up to 24 hours). You'll get an email.
6. Continue at **Part 3**.

**Heads-up:** the moment the nameservers change, the Wix site stops being served from your domain. Do the next steps (Part 3) right away. Because your site gets few visitors, a short gap is low-impact.

### Route B: Wix won't let you change nameservers

This is what Cloudflare recommends: transfer the domain registration **away from Wix** to a registrar that allows nameserver changes, then point it at Cloudflare.

1. **Check eligibility.** ICANN blocks transfers for 60 days after registration, a previous transfer, or a change to the registrant name, organization or email. If you're inside that window, wait. **Do not change your domain's contact details at Wix in the meantime.**
2. **Prepare Cloudflare first:** onboard the domain (steps 1–2 of Route A). Cloudflare will show you its two nameservers; keep the page.
3. **At Wix:** unlock the domain and get the **authorization (EPP) code**. Wix's steps for this are in their help site **[verify]**.
4. **Pick a registrar** that supports nameserver changes and incoming transfers. Cloudflare's own transfer guide lists Namecheap, Porkbun, GoDaddy and others as examples. Enter the code there and pay (a transfer adds one year of registration).
5. The transfer usually takes 3–5 days. **Moving the domain away from Wix disconnects your Wix site**, so expect the Wix site to stop working at the end of the transfer.
6. As soon as the domain is at the new registrar, set its nameservers to Cloudflare's two, wait for **Active**, and go to Part 3.
7. **Optional, 60 days later:** you can transfer from that registrar to **Cloudflare Registrar** (Cloudflare requires a 60-day gap between transfers). It's cheaper only if renewal prices differ; you can safely skip it.

### Part 3: Attach the domain and set up the redirect

Canonical site address: **`https://www.khpathways.com`**. The bare `khpathways.com` redirects to it.

1. **Attach `www`.** **Workers & Pages** → your `khpathways` Worker → **Settings** → **Domains & Routes** → **Add** → **Custom Domain** → enter `www.khpathways.com` → **Add Custom Domain**. Cloudflare creates the DNS record and the HTTPS certificate for you.
2. **Make the bare domain redirect.** Cloudflare's redirect rules only work on traffic that passes through Cloudflare, so the bare domain needs a placeholder record:
   - **DNS** → **Add record**: Type `A`, Name `@`, IPv4 `192.0.2.1`, Proxy status **Proxied** (orange cloud). (This address is a placeholder; traffic never reaches it.)
   - **Rules** → **Create rule** → **Redirect Rule**. Name it "Apex to www". Choose **Wildcard pattern**: when the request URL matches `https://khpathways.com/*`, redirect to `https://www.khpathways.com/${1}`, status **301**, **Preserve query string** on. Add a second rule for `http://khpathways.com/*` the same way, or use `http*://khpathways.com/*` with target `https://www.khpathways.com/${2}`.
   - Cloudflare's docs show this pattern for the opposite direction (www to bare). Same steps, swap the two hostnames.
3. **Test:**
   - `https://www.khpathways.com/` loads the site, with a padlock.
   - `https://khpathways.com/about` redirects to `https://www.khpathways.com/about`.
   - `http://` versions redirect to `https://`.
   - You can check from a terminal: `curl -I https://khpathways.com/` should show `301` and a `location:` header pointing at `www`.
4. Test every page and link again on the real domain, including on your phone.

### Part 4: Finish up, in this order

1. **Confirm the new site is live and correct** (Part 3, step 4).
2. **Then cancel the Wix website plan.** Cancel the *site plan only*. Do **not** cancel or let lapse the domain registration unless it has already been transferred. **[verify]** In Wix, check whether your domain was bundled with the plan, and what happens to the domain when the plan is cancelled, *before* you click cancel.
3. **Google Search Console** (if you use it): add the property `https://www.khpathways.com`, submit `https://www.khpathways.com/sitemap.xml`.
4. **Google Business Profile:** follow `GOOGLE-BUSINESS-PROFILE.md`.
5. **Optional:** once you're happy, you can turn off the `*.workers.dev` address (Worker → Settings → Domains & Routes) so only the real domain serves the site. The page canonical tags already point to `www`, so the test address won't compete in search results.

### If something goes wrong

- **Site shows an error right after the nameserver change:** DNS is still propagating (up to 24 hours), or a record is missing from the Cloudflare DNS page. Compare to your Wix screenshots.
- **The test address shows "Hello world":** that's Cloudflare's placeholder, meaning no build of this repo has deployed yet. In the Worker, open **Deployments**, then **View build history**, and check that the newest build is for the latest commit on `main`. **Retry build** re-runs the same old commit, so push a new commit to `main` (or use a Deploy Hook under Settings, then Builds) to start a fresh one. Also check **Settings, then Builds, then Git branch** is `main`; builds of any other branch are previews, not the live site.
- **Build fails in Cloudflare:** the Worker name in the dashboard doesn't match `name` in `wrangler.jsonc`, or "Build command" isn't empty.
- **A page shows an old version:** hard-refresh. Static files are cached but always revalidated.
- **Roll back a bad deploy:** Worker → **Deployments** (or **Versions**) → redeploy the previous version.

## Sources checked

- [Cloudflare Pages overview (Pages vs Workers notice)](https://developers.cloudflare.com/pages/)
- [Workers static assets](https://developers.cloudflare.com/workers/static-assets/) and [HTML handling](https://developers.cloudflare.com/workers/static-assets/routing/advanced/html-handling/) and [headers](https://developers.cloudflare.com/workers/static-assets/headers/)
- [Workers Builds (Git integration)](https://developers.cloudflare.com/workers/ci-cd/builds/), [configuration](https://developers.cloudflare.com/workers/ci-cd/builds/configuration/), [limits](https://developers.cloudflare.com/workers/ci-cd/builds/limits-and-pricing/)
- [Workers custom domains](https://developers.cloudflare.com/workers/configuration/routing/custom-domains/)
- [Set up a primary zone (full DNS setup)](https://developers.cloudflare.com/dns/zone-setups/full-setup/setup/)
- [Transfer a domain to Cloudflare](https://developers.cloudflare.com/registrar/get-started/transfer-domain-to-cloudflare/) (includes the Wix workaround)
- [Redirecting www to the apex](https://developers.cloudflare.com/pages/how-to/www-redirect/) and [creating a redirect rule](https://developers.cloudflare.com/rules/url-forwarding/single-redirects/create-dashboard/)
- Wix's handling of nameservers: from search-result summaries of Wix's help pages, not read directly. Confirm in your Wix dashboard.
