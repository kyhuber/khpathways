# To do: items I need from you

**Goal:** get www.khpathways.com off Wix and onto free hosting.
**Decision made:** host on **Cloudflare** (a Worker serving the `public/` folder), with the code on GitHub. Not GitHub Pages, whose terms exclude online businesses. The full steps are in `DEPLOY.md`.

Work top to bottom. Check each item off as you go.

## Step 1: Test site on Cloudflare (safe; Wix stays live)

- [ ] **Merge the follow-up pull request into `main`.** Pull request #1 was merged early, so `main` is missing everything after the first three commits, including `wrangler.jsonc`. Cloudflare deploys from `main`, which is why the first deploy failed. Ask Claude to open the new pull request, then merge it, then retry the Cloudflare deploy (Worker, then Deployments, then Retry build).
- [ ] **Do DEPLOY.md Part 1:** create the free Cloudflare account, connect the `khpathways` repo (name the project `khpathways`, leave the build command empty), and test on the `workers.dev` address. Nothing in this step touches your live Wix site.
- [ ] **Click through the test site:** every page, the booking button, the email links, and a made-up address like `/nope` (should show the "Page not found" page).

## Step 2: Find out how Wix handles your domain (before changing anything)

- [ ] **Check in Wix whether you can edit the nameservers** for khpathways.com (Domains, then Name servers, possibly under Advanced). This decides Route A or Route B in DEPLOY.md Part 2. Cloudflare's docs say Wix usually does not allow it. I couldn't read Wix's help pages from here, so I haven't confirmed that for your account.
- [ ] **Screenshot your Wix DNS records** (especially any MX or TXT records), and note the domain's **registration date** and whether you changed its contact details in the last 60 days.
- [ ] **Tell me what you find** (or paste any Cloudflare or Wix screen you get stuck on) and I'll walk through the next step.

## Step 3: Move the domain (DEPLOY.md Parts 2 and 3)

- [ ] Follow Route A or Route B, then attach `www.khpathways.com` and set up the bare-domain redirect.
- [ ] Re-test every page on the real domain, including on your phone.

## Step 4: Only then, retire Wix

- [ ] **Cancel the Wix site plan** only after the new site is live on khpathways.com and verified. Check in Wix first what cancelling does to your domain registration. Moving the domain away from Wix (Route B) disconnects the Wix site.

## Also before the domain move

- [ ] **Click the booking link once** (`https://calendar.app.google/NVaX8nhezWKmJPHC7`) and make a test booking if you can. I can't reach Google Calendar from here, so I've only confirmed that every booking button points to it and opens in a new tab.
- [ ] **Send a test email** to yourself from the `khpathways@gmail.com` links (home page, services page "Contact for a quote", disclaimer) to check the subject lines.
- [ ] **Have your Wix login ready.** You'll need it for DNS or nameserver changes. **Do not change the domain's contact details** at Wix before or during the move. Wix reports a 60-day lock after that kind of change.

## Content decisions still open

- [ ] **Testimonials.** Ask the clients who offered. Steps and a starter email are in `GOOGLE-BUSINESS-PROFILE.md`, Part 2. Send me the approved wording and the name format each client approved (full name, first name and last initial, or anonymous), and I'll add a section to the home page. I won't edit a quote.
- [ ] **Attorney check.** Confirm with your attorney: (1) the line "Coaching is educational and is not financial advice" and the disclaimer wording, (2) the Google Business Profile category you choose, (3) the wording of the Profit First / ongoing-support service before it goes on the site, and (4) any testimonial that mentions numbers or results.
- [ ] **Optional: a retouched portrait.** The home photo is cropped to avoid the Wrapbook logo. If you have a version with the logo removed, send it and I'll swap it in.
- [ ] **Optional: the ongoing-support service.** When you've piloted it with your client, tell me what it includes and how you want to describe and price it (or "contact for a quote"), and I'll add it as a fourth block on the Work With Me page.

## After launch

- [ ] **Google Business Profile.** Follow `GOOGLE-BUSINESS-PROFILE.md`, Part 1, once `www.khpathways.com` is live.
- [ ] **Google Search Console.** Add the site and submit `https://www.khpathways.com/sitemap.xml`.
- [ ] **Check the live site** on your phone: each page, the booking button, and the email links.
- [ ] **Tell me when it's live**, and I'll re-run the link and Lighthouse checks against the real URL.
