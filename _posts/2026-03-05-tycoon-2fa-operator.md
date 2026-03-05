---
layout: page
title: Breaking Saad - phinding a PhaaS operator
permalink: /tycoon-2fa-operator
---

> Note: All of the information used in this analysis is publicly available or derived from open sources. A full list of sources and links is at the end of the post.

---

## TL;DR

On 4 March 2026, Europol and Microsoft announced a coordinated takedown of the Tycoon 2FA phishing-as-a-service (PhaaS) platform, seizing 330 domains and naming **Saad Fridi**, based in Pakistan, as the platform's primary developer. This post documents the open source investigation that arrived at the same attribution independently, starting from aliases exposed in a Sekoia threat intelligence report and pivoting through many sources.

---

## Background

### What is Tycoon 2FA?

Tycoon 2FA is a PhaaS platform — a subscription-based service that packages phishing infrastructure for sale to criminal customers, handling hosting, domain rotation, evasion, and campaign management. At its core it is an adversary-in-the-middle (AiTM) platform: a reverse proxy sits between the victim and a legitimate login service such as Microsoft 365 or Gmail, intercepting not just credentials but live session cookies, bypassing multi-factor authentication entirely by capturing the authenticated session token rather than the password alone.

Active since August 2023 and tracked by Microsoft as **Storm-1747**, Tycoon 2FA grew to account for roughly 62% of phishing attempts blocked by Microsoft and facilitated unauthorised access to nearly 100,000 organisations worldwide. The kit was sold via Telegram and Signal, starting at $120 for 10 days of access.

![tycoon group telegram](<../assets/img/tycoon_telegram.png> "Tycoon Group Telegram Channel")

### The Europol Takedown

On 4 March 2026, in a coordinated action led by Microsoft and Europol's European Cybercrime Centre (EC3), 330 domains forming the core infrastructure of Tycoon 2FA were seized. Law enforcement agencies across Latvia, Lithuania, Portugal, Poland, Spain, and the United Kingdom carried out operational measures, supported by private sector partners including Cloudflare, Intel 471, Proofpoint, SpyCloud, and The Shadowserver Foundation.

Legal action was also taken against multiple individuals suspected of running the platform, with Saad Fridi named publicly as its primary developer.

---

## Methodology

The investigation relied almost exclusively on **pivoting** — an OSINT technique where a known data point such as an alias, email address, or domain is used as a starting point to surface new, previously unknown information about the same target. Each step in the chain below begins with something established in the previous step and uses it to identify a new account, platform, or piece of infrastructure.

Threat actors who have been active online for many years leave a trail of bread crumbs, so let's follow them.

---

## Step 1: Aliases from the Sekoia Report

The investigation began with a [Sekoia blog post](https://blog.sekoia.io/tycoon-2fa-an-in-depth-analysis-of-the-latest-version-of-the-aitm-phishing-kit/) providing a detailed technical analysis of Tycoon 2FA. Within the report, Sekoia identified the Telegram channel used to sell the kit (`hxxps://t[.]me/tycoon_2fa_Link`) and documented several aliases associated with the operator:

- **Tycoon Group**
- **SaaadFridi**
- **Mr_XaaD**

These aliases became the starting points for two parallel lines of investigation that would later converge.

![investigation pivot diagram](<../assets/img/pivot_diagram.png> "Two parallel lines of investigation converging on RaoX44D")

---

## Step 2: Mr_XaaD → @Ra0saad on X

Searching for the alias **Mr_XaaD** surfaced an X (formerly Twitter) account, **@Ra0saad**. A [2016 post](https://x.com/Ra0saad/status/689879578265915392) from this account shared a link to a record on [Zone-H](https://www.zone-h.org/mirror/id/25494186) under the alias **Mr.XaaD**.

Zone-H is an archive where threat actors self-report website defacements — essentially claiming public credit for compromising and defacing a target website. It is a well-known resource in threat intelligence because actors voluntarily submit their own activity, often for reputational reasons within hacking communities, making it a useful passive collection point. The presence of a defacement record here is consistent with early-career hacktivist behaviour and provides a timestamp on when this alias was active.

While **Mr_XaaD** and **Mr.XaaD** are not an exact string match, the similarity combined with the self-referential nature of the post — the @Ra0saad account voluntarily linking to its own defacement record — is sufficient to connect them. This link is also made independently by Recorded Future, who list the **Mr.XaaD** alias in their 2024 Malicious Infrastructure Report.

![zone-h screenshot](<../assets/img/zone_h.png> "Zone-H defacement record for Mr.XaaD")

---

## Step 3: @Ra0saad → Facebook RaoX44D

Reviewing earlier posts on the @Ra0saad X account, I found a [2016 post titled "Like me"](https://www.facebook.com/RaoX44D/posts/870883589677600) that links directly to a Facebook profile operating under the handle **RaoX44D**.

This is a self-referential cross-platform link — the @Ra0saad account is actively directing its own followers to the RaoX44D Facebook page, meaning the operator of both accounts is the same person. The significance of this step is that RaoX44D now becomes a **common node**: an identifier that connects the Mr_XaaD alias on one side to the SaaadFridi alias on the other, as shown in Step 4 below.

![ra0saad like me post](<../assets/img/ra0saad_likeme.png> "Like me post on @Ra0saad linking to RaoX44D Facebook")

---

## Step 4: SaaadFridi → YouTube @saadfridi7784

Running the **SaaadFridi** alias through a Google search found the YouTube account [@saadfridi7784](https://www.youtube.com/@saadfridi7784). On its own, this is nothing concrete, the alias is close but not identical. The key corroborating detail is that several videos on this channel include links to the **RaoX44D** Facebook page in their descriptions.

This is the pivotal overlap in the investigation. The RaoX44D Facebook page had already been established in Step 3 as belonging to the same person as @Ra0saad. Its appearance in the @saadfridi7784 YouTube channel's video descriptions means the same Facebook account is being actively promoted across both the Mr_XaaD and SaaadFridi aliases, confirming that both clusters belong to the same individual.

![saadfridi7784 youtube](<../assets/img/youtube_channel.png> "The @saadfridi7784 YouTube channel")

---

## Step 5: YouTube → anon-spider[.]tk

The [earliest video](https://www.youtube.com/watch?v=FpKE3mIX3-o) posted to the @saadfridi7784 channel, titled *"admin file finder for bypassing by mr.xaad"*, contains two useful items in its description: a link to the domain `anon-spider[.]tk`, and a reference to the **Mr.XaaD** Facebook account. The video content itself is notable, it is a hacking tool tutorial, consistent with the defacement activity observed on Zone-H and the broader hacktivist community links shown later.

The domain `anon-spider[.]tk` is an additional pivot point and also contextually interesting beyond its investigative value. The Tycoon Group's commercial website `tycoongroup[.]ws` features a prominent spider, and the word "spider" appearing in an early domain linked to the same individual, years before Tycoon 2FA existed, may reflect a persistent personal aesthetic that carried through into the branding of his later commercial operation.

![anon spider youtube](<../assets/img/anon_spider_yt.png> "YouTube video description linking to anon-spider.tk")

---

## Step 6: anon-spider[.]tk → PII

The `anon-spider[.]tk` domain no longer resolves, but it is preserved via the [Wayback Machine](https://web.archive.org/web/20160330160426/http:/anon-spider.tk/). Archived snapshots are particularly valuable in OSINT because they preserve content that the subject may have since deleted or modified, often capturing details that were posted carelessly before the operator was aware of the operational security risks. The snapshot from 30 March 2016 lists the following contact details for the site operator:

- **Email:** `Saad.fridi110@hotmail.co.uk`
- **Skype:** `saad.fridi110`

The email address appearing on an archived domain linked via the YouTube channel to the RaoX44D Facebook page is already meaningful, but a [separate YouTube video](https://www.youtube.com/watch?v=HBG3qfbfMS0) on the @saadfridi7784 channel independently corroborates it — the video shows `Saad.fridi110@hotmail.co.uk` as the email address associated with the **RaoX44D** Facebook account directly. This is a second independent confirmation of the same overlap, significantly strengthening the link.

![anon spider archive](<../assets/img/anon_spider_archive.png> "Wayback Machine snapshot of anon-spider.tk showing contact details")

---

## Step 7: Corroborating Identity and Location

A [YouTube video](https://www.youtube.com/watch?v=HBG3qfbfMS0) from the @saadfridi7784 channel reveals what appears to be the threat actor's date of birth **28/11/1996** and lists his location as **Faisalabad, Pakistan**, major OpSec fail.

To independently corroborate the date of birth, I cross-referenced the email address `Saad.fridi110@hotmail.co.uk` against data from a breach affecting the music streaming service Deezer found via HaveIBeenPwned. The breach record associated with that email address lists the same date of birth — 28/11/1996 — providing independent confirmation of the detail visible in the video.

![youtube dob screenshot](<../assets/img/youtube_dob.png> "YouTube video showing date of birth and Faisalabad location")

---

## Attribution Summary

| Attribute | Value |
|---|---|
| Name | Saad Fridi |
| Date of Birth | 28/11/1996 |
| Location | Faisalabad, Pakistan |
| Education | PAK Polytechnic Institute (2012–2015) |
| Email | Saad.fridi110@hotmail.co.uk |
| Skype | saad.fridi110 |
| Facebook | RaoX44D, Mr.XaaD |
| X/Twitter | @Ra0saad |
| YouTube | @saadfridi7784 |

---

## Additional Context

### Links to Pakistani Nationalist Hacking Groups

I also identified links between Fridi and Pakistani nationalist hacking communities including **TeamLeets** and **PakXploiters**. I see this as an excellent area for further investigation but have not gone further down this route due to time constraints.

### The Spider Motif

As a contextual detail, the domain `anon-spider[.]tk`, linked to Fridi circa 2016, shares a thematic element with the Tycoon Group's commercial website `tycoongroup[.]ws`, which features a prominent spider image. While this is not direct evidence, it is consistent with the other attribution links and may reflect a persistent personal identifier carried across years of activity. 

---

## Conclusion

Starting from a handful of aliases in a threat intelligence report, open source investigation techniques allowed me to build a high-confidence attribution to a single individual, one subsequently confirmed by Europol and named publicly in coverage of the March 2026 takedown.

But more interestingly, this shows how a threat actor develops over time. The earliest evidence of Fridi's online activity dates to around 2015–2016, when he was posting hacking tool tutorials on YouTube and claiming defacements on Zone-H in the company of Pakistani nationalist hacking groups. Nearly a decade later, the same individual was operating one of the most prolific phishing platforms ever documented, responsible for tens of millions of fraudulent emails per month and unauthorised access to nearly 100,000 organisations worldwide.

---

## Sources

1. Sekoia - [Tycoon 2FA: an in-depth analysis of the latest version of the AiTM phishing kit](https://blog.sekoia.io/tycoon-2fa-an-in-depth-analysis-of-the-latest-version-of-the-aitm-phishing-kit/)
2. X post - [Hacked ^_^ Mr.XaaD (@Ra0saad)](https://x.com/Ra0saad/status/689879578265915392)
   - Zone-H defacement record - [https://www.zone-h.org/mirror/id/25494186](https://www.zone-h.org/mirror/id/25494186)
3. Facebook post - [RaoX44D "Like me"](https://www.facebook.com/RaoX44D/posts/870883589677600)
4. YouTube - [@saadfridi7784](https://www.youtube.com/@saadfridi7784)
5. YouTube video - [admin file finder for bypassing by mr.xaad](https://www.youtube.com/watch?v=FpKE3mIX3-o)
6. Web Archive - [anon-spider.tk (30/03/2016)](https://web.archive.org/web/20160330160426/http:/anon-spider.tk/)
7. YouTube video - [FacEbooK AuToLiker](https://www.youtube.com/watch?v=HBG3qfbfMS0)
8. Europol - [Global phishing-as-a-service platform taken down in coordinated public-private action](https://www.europol.europa.eu/media-press/newsroom/news/global-phishing-service-platform-taken-down-in-coordinated-public-private-action)
9. Microsoft On the Issues - [How a global coalition disrupted Tycoon 2FA](https://blogs.microsoft.com/on-the-issues/2026/03/04/how-a-global-coalition-disrupted-tycoon/)
10. Intel 471 - [Born to bypass MFA: Taking down Tycoon 2FA](https://www.intel471.com/blog/born-to-bypass-mfa-taking-down-tycoon-2fa)
11. Trend Micro - [Europol, Microsoft, TrendAI and Collaborators Halt Tycoon 2FA Operations](https://www.trendmicro.com/en_us/research/26/c/tycoon2fa-takedown.html)
12. SpyCloud - [Tycoon 2FA Takedown: Inside the Global Phishing Infrastructure Disruption](https://spycloud.com/blog/tycoon-2fa-takedown-inside-the-global-phishing-infrastructure-disruption/)
13. The Hacker News - [Europol-Led Operation Takes Down Tycoon 2FA Phishing-as-a-Service](https://thehackernews.com/2026/03/europol-led-operation-takes-down-tycoon.html)
14. SecurityWeek - [Tycoon 2FA Phishing Platform Dismantled in Global Takedown](https://www.securityweek.com/tycoon-2fa-phishing-platform-dismantled-in-global-takedown/)
