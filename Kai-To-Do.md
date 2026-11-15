# To do: items I need from you

**Goal:** get www.khpathways.com off Wix and onto free hosting.
**Decision made:** host on **Cloudflare** (a Worker serving the `public/` folder), with the code on GitHub. Not GitHub Pages, whose terms exclude online businesses. The full steps are in `DEPLOY.md`.

Work top to bottom. Check each item off as you go.

## Step 1: Test site on Cloudflare (safe; Wix stays live)

- [x] ~~Merge the follow-up pull request into `main`.~~ Done (PR #2). `main` now has `wrangler.jsonc`.
- [x] ~~Do DEPLOY.md Part 1.~~ Done: the site is live on the `workers.dev` address.
- [ ] **Click through the test site:** every page, the booking button, the email links, and a made-up address like `/nope` (should show the "Page not found" page).

## Step 2: Find out how Wix handles your domain

- [x] ~~Check whether Wix lets you edit the nameservers.~~ Yes: "Use custom nameservers" is available. The domain can stay registered at Wix.
- [x] ~~Export the Wix DNS records.~~ Done. They show Google Workspace email (5 MX records) and two Google TXT records. The full list is in DEPLOY.md Part 2.
- [x] ~~Check the renewal date.~~ The domain is paid through **May 18, 2027**.
- [x] ~~Decide timing.~~ **Move the site now** (the Premium plan costs $24/month), and **move the domain registration later**, before May 2027.

## Step 3: Point the domain at Cloudflare (DEPLOY.md Parts 2 and 3, do soon)

- [ ] **Check Wix Subscriptions for Google Workspace.** If it's listed there, tell me before cancelling anything.
- [ ] **Add khpathways.com to Cloudflare** with **Connect a domain**, not Transfer (Free plan) and check that the 5 MX and 2 TXT records are there. Set the Wix A and `www` records to DNS only for now.
- [ ] **Check DNSSEC in Wix.** If it's on, turn it off and wait 24 hours.
- [ ] **In Wix, click "Use custom nameservers"** and enter Cloudflare's two.
- [ ] **When Cloudflare says Active:** send a test email to your @khpathways.com address.
- [ ] **Attach `www.khpathways.com`** to the Worker and set up the bare-domain redirect (Part 3).
- [ ] **Re-test every page** on the real domain, including on your phone, and send one more test email.

## Step 4: Cancel the Wix plan (as soon as Step 3 checks out)

- [ ] **Cancel the Premium Plan Light** in Wix Subscriptions. Cancel the site plan only, not the domain. Read the cancellation screen before confirming (DEPLOY.md Part 4).

## Step 5: Move the domain registration (any time before early April 2027)

- [ ] **Transfer khpathways.com from Wix to Cloudflare Registrar** (DEPLOY.md Part 5). Do it a few weeks after the cutover, once things have run smoothly. Your site and email keep working during the transfer.

## Also before the domain move

- [ ] **Click the booking link once** (`https://calendar.app.google/NVaX8nhezWKmJPHC7`) and make a test booking if you can. I can't reach Google Calendar from here, so I've only confirmed that every booking button points to it and opens in a new tab.
- [ ] **Send a test email** to yourself from the `khpathways@gmail.com` links (home page, services page "Contact for a quote", disclaimer) to check the subject lines.
- [ ] **Have your Wix login ready.** You'll need it for the nameserver change and the later transfer. **Do not change the domain's contact details** at Wix until the transfer to Cloudflare is done. Any change starts a 60-day transfer lock.

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
