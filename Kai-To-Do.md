# To do: items I need from you

Ordered by what unblocks the most. Check each off as you go.

## Needed to finish DEPLOY.md

- [ ] **Allow Cloudflare's docs site in this Claude session.** I couldn't verify Cloudflare's current setup steps because this session's network policy blocks `developers.cloudflare.com` (403). In the cloud environment menu in the session title bar, choose **Edit**, then **Network access**. Either add `developers.cloudflare.com` under **Allowed domains** (leave "Allow package managers" ticked), or choose a broader access level. Steps: https://code.claude.com/docs/en/cloud-environments#network-access . Then tell me and I'll check the docs and write DEPLOY.md.
  - Specifically, I'll confirm whether a static folder should go through Cloudflare Pages or Workers static assets right now, how the `www` and bare-domain setup works when your DNS is at Wix, and the apex-to-www redirect steps.

## Before launch

- [ ] **Merge [pull request #1](https://github.com/kyhuber/khpathways/pull/1)** into `main` when you're happy. Cloudflare will deploy from `main`, so the site needs to be there first.
- [ ] **Click the booking link once** (`https://calendar.app.google/NVaX8nhezWKmJPHC7`) and make a test booking if you can. I can't reach Google Calendar from here, so I've only confirmed that every booking button points to it and opens in a new tab.
- [ ] **Send a test email** to yourself from the `khpathways@gmail.com` links (home page, services page "Contact for a quote", disclaimer) to check the subject lines.
- [ ] **Create a free Cloudflare account** (dash.cloudflare.com) when DEPLOY.md is ready.
- [ ] **Have your Wix login ready.** You'll need it for DNS or nameserver changes. **Do not change the domain's contact details** at Wix before or during the move. Wix reports a 60-day lock after that kind of change.
- [ ] **Do not cancel Wix yet.** Cancel only after the new site is live and verified, and any domain transfer to Cloudflare Registrar comes last.

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
