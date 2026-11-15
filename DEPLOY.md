# Deploying khpathways.com on Cloudflare

Checked against Cloudflare's documentation in October 2026, and updated in November 2026 with what your Wix account shows (sources at the bottom). Cloudflare renames dashboard items often, so if a button name differs, look for the closest match. Where I could not verify something, it is marked **[verify]**.

## The short version

Checked in your Wix account in November 2026: Wix **does** let you point khpathways.com at other nameservers ("Use custom nameservers"), so the domain can stay registered at Wix while Cloudflare runs the site. The domain is paid through **May 18, 2027**. The Premium Plan Light is $24/month, billed monthly.

**Recommendation: move the site now, and move the domain registration later.** Each month on Wix costs $24 and buys nothing the new site needs. The domain is already paid for, so it can stay at Wix until you transfer it to Cloudflare Registrar any time before May 2027 (Part 5). Waiting until May would not save money; it would only cost about six more months of the plan.

1. **Done:** the site runs on Cloudflare at a `workers.dev` test address (Part 1).
2. **Next:** add the domain to Cloudflare, copy your email records, and switch the nameservers in Wix (Part 2). Then attach `www` and set up the redirect (Part 3).
3. **Once the new site and email both work on your real domain:** cancel the Wix Premium plan (Part 4).
4. **Later, before May 2027:** transfer the domain registration from Wix to Cloudflare Registrar (Part 5).

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
2. **Connect the repo.** In the dashboard, go to **Compute** → **Workers & Pages** → **Create application** → next to **Import a repository** choose **Get started**.
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

## Part 2: Point the domain at Cloudflare (`khpathways.com`)

### What Cloudflare requires

- To attach a custom domain to a Worker, the domain must be an **active zone on Cloudflare**. That means its **nameservers point to Cloudflare**. Cloudflare's docs: "To add a Custom Domain, you must have: An active Cloudflare zone."
- You cannot create a Custom Domain on a hostname that already has a CNAME record.
- Moving only a `www` CNAME (leaving DNS elsewhere) is not an option for a Worker, and the bare domain (`khpathways.com`) can't be done that way either.

Your Wix account allows custom nameservers, so this is straightforward: the domain stays registered (and paid for) at Wix, and Cloudflare takes over DNS.

### Your current DNS records at Wix (exported November 2026)

| Type | Name | Value | What it is | In Cloudflare |
|---|---|---|---|---|
| MX | `@` | `aspmx.l.google.com`, priority 10 | Google Workspace email | **Copy** |
| MX | `@` | `alt1.aspmx.l.google.com`, priority 20 | Google Workspace email | **Copy** |
| MX | `@` | `alt2.aspmx.l.google.com`, priority 30 | Google Workspace email | **Copy** |
| MX | `@` | `alt3.aspmx.l.google.com`, priority 40 | Google Workspace email | **Copy** |
| MX | `@` | `alt4.aspmx.l.google.com`, priority 50 | Google Workspace email | **Copy** |
| TXT | `@` | `v=spf1 include:_spf.google.com ~all` | Lets Google send mail for your domain | **Copy** |
| TXT | `@` | `google-site-verification=xY-4nR-H32hzRUkGp7pRjlEAX-5StUSQfa6EoXqTSEg` | Proves domain ownership to Google | **Copy** |
| A | `@` | `185.230.63.171`, `185.230.63.107`, `185.230.63.186` | Bare domain → Wix site | Keep for now, **DNS only**; replace in Part 3 |
| CNAME | `www` | `cdn3.wixdns.net` | `www` → Wix site | Keep for now, **DNS only**; delete in Part 3 |
| NS | `@` | `ns0.wixdns.net`, `ns1.wixdns.net` | Wix runs DNS | Not copied; Cloudflare supplies its own |

**The MX records matter most.** If they are missing when the nameservers switch, email to your @khpathways.com address stops arriving.

### Steps

1. **Check Google Workspace billing.** In Wix, open **Subscriptions** (account menu). If **Google Workspace** is listed there, your email is billed through Wix. Tell me before cancelling anything; it would need to move to direct Google billing first. If it's not listed, you pay Google directly and nothing changes.
2. **Add the domain to Cloudflare.** **Domains** → **Connect a domain** (older screens say **Onboard a domain** or **Add a site**; not **Transfer a domain**, which is Part 5) → `khpathways.com` → **Free** plan.
3. **Review the scanned DNS records** against the table above. Add anything missing (all 5 MX records with their priorities, both TXT records). For the Wix `A` records and the `www` CNAME, set **Proxy status** to **DNS only** (grey cloud). Leaving them in place means the Wix site keeps loading during the switch, so there is no gap.
4. **Copy the two nameservers** Cloudflare shows you (they look like `name.ns.cloudflare.com`).
5. **DNSSEC.** In Wix (**Domains** → `khpathways.com`), check whether DNSSEC is on. If it is, turn it off and wait 24 hours before step 6. Changing nameservers with DNSSEC on can take the domain offline.
6. **Switch the nameservers.** In Wix: **Domains** → `khpathways.com` → **⋯** → **Manage DNS records** → **Nameservers** → **Use custom nameservers**. Replace `ns0.wixdns.net` and `ns1.wixdns.net` with Cloudflare's two, exactly. Wix may warn that the domain will be disconnected from your Wix site; that's expected.
7. **Wait for Active.** Cloudflare emails you when the zone is Active (usually minutes, up to 24 hours).
8. **Test email.** From another account (e.g. `khpathways@gmail.com`), send a message to your @khpathways.com address and confirm it arrives.

**In the Wix domain menu, leave these alone:** **Edit contact info** (any change to the name, organization or email starts a 60-day transfer lock), **Assign to a different site** and **Unassign from this site** (not needed), and **Transfer away from Wix** (that's Part 5, later).

### Part 3: Attach the domain and set up the redirect

Canonical site address: **`https://www.khpathways.com`**. The bare `khpathways.com` redirects to it.

1. **Attach `www`.** In Cloudflare **DNS**, delete the `www` CNAME that points to `cdn3.wixdns.net` (a Custom Domain can't be added while it exists). Then **Compute** → **Workers & Pages** → your `khpathways` Worker → **Settings** → **Domains & Routes** → **Add** → **Custom Domain** → enter `www.khpathways.com` → **Add Custom Domain**. Cloudflare creates the DNS record and the HTTPS certificate for you.
2. **Make the bare domain redirect.** Cloudflare's redirect rules only work on traffic that passes through Cloudflare, so the bare domain needs a placeholder record:
   - **DNS**: delete the three Wix `A` records (`185.230.63.x`). Then **Add record**: Type `A`, Name `@`, IPv4 `192.0.2.1`, Proxy status **Proxied** (orange cloud). (This address is a placeholder; traffic never reaches it.)
   - **Rules** → **Create rule** → **Redirect Rule**. Name it "Apex to www". Choose **Wildcard pattern**: when the request URL matches `https://khpathways.com/*`, redirect to `https://www.khpathways.com/${1}`, status **301**, **Preserve query string** on. Add a second rule for `http://khpathways.com/*` the same way, or use `http*://khpathways.com/*` with target `https://www.khpathways.com/${2}`.
   - Cloudflare's docs show this pattern for the opposite direction (www to bare). Same steps, swap the two hostnames.
   - **Do not touch the MX or TXT records.**
3. **Test:**
   - `https://www.khpathways.com/` loads the site, with a padlock.
   - `https://khpathways.com/about` redirects to `https://www.khpathways.com/about`.
   - `http://` versions redirect to `https://`.
   - You can check from a terminal: `curl -I https://khpathways.com/` should show `301` and a `location:` header pointing at `www`.
4. Test every page and link again on the real domain, including on your phone. Send one more test email to your @khpathways.com address.

### Part 4: Cancel the Wix plan and finish up

1. **Confirm the new site and email both work** (Part 3, steps 3 and 4).
2. **Cancel the Wix Premium Plan Light** ($24/month). In Wix, open **Subscriptions**, find the Premium plan, and cancel it (or turn off auto-renew). Cancel the *site plan only*. The domain is a separate subscription, paid through May 18, 2027; do not cancel it. Before you confirm, read Wix's cancellation screen: it should say the site plan ends and the domain stays. If it says anything about the domain, stop and check with me.
3. **Google Search Console** (if you use it; the `google-site-verification` record suggests you do): add the property `https://www.khpathways.com` and submit `https://www.khpathways.com/sitemap.xml`.
4. **Google Business Profile:** follow `GOOGLE-BUSINESS-PROFILE.md`.
5. **Optional:** once you're happy, you can turn off the `*.workers.dev` address (Worker → Settings → Domains & Routes) so only the real domain serves the site. The page canonical tags already point to `www`, so the test address won't compete in search results.
6. **Optional: stop others sending email as @khpathways.com.** You use Gmail, not email at this domain, and there is no Google Workspace subscription. So the 5 Google `MX` records and the Google SPF record are leftovers. With no DMARC record, it is easier for spammers to send mail that claims to come from your domain. In Cloudflare **DNS → Records**:
   - Delete the 5 `MX` records.
   - Edit the SPF `TXT` record to `v=spf1 -all` (no server may send mail for this domain).
   - Add a `TXT` record, Name `_dmarc`, Content `v=DMARC1; p=reject;` (receiving mail servers should reject mail that claims to be from this domain).
   - Keep the `google-site-verification` record.
   - Your Gmail address is not affected. If you ever want email at khpathways.com, undo these and follow your email provider's setup.

### Part 5: Move the domain registration to Cloudflare (any time before May 2027)

Not urgent. The domain is paid through **May 18, 2027**. Moving it means one fewer account to manage, and Cloudflare Registrar renews at cost (no markup; roughly $10–11 a year for a `.com`), usually less than Wix's renewal price. For a `.com`, a transfer **adds one year** to the current expiry, so you don't lose the time you've already paid for. A sensible time is a few weeks after the cutover, once everything has run smoothly. Do it **no later than early April 2027** so it finishes before Wix renews.

Requirements (Cloudflare's docs): the domain is already an **Active** zone on Cloudflare (done in Part 2), **DNSSEC is off**, the domain was registered or last transferred **more than 60 days ago**, and the registrant name, organization and email haven't changed in the last 60 days.

1. **In Wix:** **Domains** → `khpathways.com` → **⋯** → **Transfer away from Wix**. Follow the prompts to unlock the domain and get the **authorization (EPP) code**. Wix emails it or shows it on screen. Codes expire, so use it promptly.
2. **In Cloudflare:** **Domain Registration** → **Transfer Domains** → select `khpathways.com` → enter the code → confirm the contact details and pay for one year.
3. Approve any confirmation email from Wix or Cloudflare. The transfer takes up to about 10 days. Your site and email keep working throughout, because Cloudflare already runs the DNS.
4. When it completes, check that Cloudflare shows the domain with auto-renew on, and that it no longer appears as a paid subscription in Wix.

After that, Wix has nothing left of yours. You can close the Wix account if you like.

### If something goes wrong

- **Site shows an error right after the nameserver change:** DNS is still propagating (up to 24 hours), or a record is missing from the Cloudflare DNS page. Compare it to the table in Part 2.
- **Email stops arriving after the nameserver change:** one of the 5 MX records is missing or has the wrong priority in Cloudflare DNS. Compare it to the table in Part 2. Mail servers retry for several days, so mail sent during a short gap usually arrives late rather than bouncing.
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
- Wix's handling of nameservers: confirmed in Kyle's Wix dashboard (November 2026), which offers "Use custom nameservers" for this domain.
- [Cloudflare Registrar FAQ](https://developers.cloudflare.com/registrar/faq/) (transfer timing and requirements)
