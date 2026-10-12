# To do: items I need from you

Ordered by what unblocks the most. Check each off as you go.

## Cloudflare and the domain (see DEPLOY.md for the full steps)

- [x] ~~Allow `developers.cloudflare.com` in the Claude session.~~ Done. DEPLOY.md is written and checked against Cloudflare's docs.
- [ ] **Merge [pull request #1](https://github.com/kyhuber/khpathways/pull/1)** into `main` (Cloudflare deploys from `main`).
- [ ] **Do DEPLOY.md Part 1** (create the free Cloudflare account, connect the repo, test on the `workers.dev` address). Nothing in this part touches your live Wix site.
- [ ] **Check in Wix whether you can edit the nameservers** for khpathways.com (Domains, then Name servers, possibly under Advanced). This decides Route A or Route B in DEPLOY.md Part 2. Cloudflare's docs say Wix usually does not allow it. I could not read Wix's help pages from here, so I haven't confirmed that for your account.
- [ ] **Screenshot your Wix DNS records** (especially any MX or TXT records), and note the domain's **registration date** and whether you changed its contact details in the last 60 days.
- [ ] **Tell me what you find** and, if you like, paste the Cloudflare screens you get stuck on. I can walk through them.

## Before launch

- [ ] **Click the booking link once** (`https://calendar.app.google/NVaX8nhezWKmJPHC7`) and make a test booking if you can. I can't reach Google Calendar from here, so I've only confirmed that every booking button points to it and opens in a new tab.
- [ ] **Send a test email** to yourself from the `khpathways@gmail.com` links (home page, services page "Contact for a quote", disclaimer) to check the subject lines.
- [ ] **Have your Wix login ready.** You'll need it for DNS or nameserver changes. **Do not change the domain's contact details** at Wix before or during the move. Wix reports a 60-day lock after that kind of change.
- [ ] **Do not cancel Wix yet.** Cancel the Wix *site plan* only after the new site is live on khpathways.com and verified. Check in Wix first what cancelling does to your domain registration. Moving the domain away from Wix (Route B) disconnects the Wix site.

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
