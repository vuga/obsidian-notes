Automated feed FPS - Goda and Akvile for the meet
Unified FP report - Dima, Valdemaras


## FP/FN Meeting

8.82% FPs
91.18% FNs

- LACK OF INTELLIGENCE SOURCE: 19 (55.9%)
- MISSED BECAUSE OF ML: 7 (20.6%)
- FP FROM FEED: 4 (11.8%)
- MISSED UPON REPORT: 2 (5.9%)
- WAS MALICIOUS BEFORE: 1 (2.9%)
- N/a: 1 (2.9%)

### Category breakdown

- Fake e‑commerce/services (fake businesses or offerings): 13 (40.6%)
    - Fake shop: 6 (18.8%)
    - Fake travel agency: 1 (3.1%)
    - Fake bank (site): 1 (3.1%)
    - Fake betting: 1 (3.1%)
    - Private health service (fake service): 1 (3.1%)
    - Fake site (generic): 1 (3.1%)
    - Fake Game news website: 1 (3.1%)
    - Fake game beta: 1 (3.1%)
- Credential phishing (logins/payment capture): 10 (31.3%)
    - Bank login: 2 (6.3%)
    - Others: Scam affiliate login page, Roblox login page, Fake Brand login page, StreamYard login, Fake account login, Fake login page, Fake voting page, payment form → 8 total (25.0%)
- Delivery/parcel scams: 2 (6.3%)
    - Fake Delivery site, Parcel Retrieval Scam
- Giveaway/promotions scams: 2 (6.3%)
    - Fake giveaway (x2)
- Malware/tooling: 2 (6.3%)
    - iOS/Android game malware, “safe qr code generator”
- Social‑media scam: 1 (3.1%)
    - TikTok scam
- Job scam: 1 (3.1%)
    - Fake jobs
- Other/unclear: 1 (3.1%)
    - “Scam” (unspecified)

- “Fake shop” dominates a single label: 6/32 (18.8%).
- Login/credential theft is common: 10/32 (31.3%) spanning many brands/platforms (banking, Roblox, StreamYard, generic brand/account).
- Fake business/service fronts are the largest umbrella: 13/32 (40.6%), covering e‑commerce, travel, betting, health, and fake content (game news/beta).
- Finance‑themed items (bank/funding/gambling) show up frequently: Fake bank (1) + Bank login (2) + payment form (1) + Fake betting (1) = 5/32 (15.6%).
- Gaming‑themed items appear multiple times: Roblox login, Fake Game news website, Fake game beta, game malware = 4/32 (12.5%).
- Parcel/delivery lures are present but smaller: 2/32 (6.3%).
- Giveaways recur: 2/32 (6.3%).

### Insights

- Brand and impersonation coverage gaps (missing brands, brand term in domain, “add brand”): 8 (23.5%)
- E‑commerce scam indicators (fake shop, steep discounts, fake images, single/absent payment methods, Outlook contact): 6 (17.6%)
- Mobile‑first templates and invite‑coded logins (template pages, phone‑optimized only): 4 (11.8%)
- Site integrity and structure defects (broken/empty pages, one‑page‑only, dead links, visually broken): 4 (11.8%)
- Domain age/NRD signals (NRD, age thresholds, >90‑day affiliate signal): 3 (8.8%)
- Technical evasion and delivery (cloaking/geo‑fencing, redirects, shorteners): 3 (8.8%)
- External reputation/OSINT (VT score, social presence checks): 2 (5.9%)
- Platform/hosting coverage (Wix detection scope): 1 (2.9%)
- Business registry verification (company registrars): 1 (2.9%)
- Lure keywords (“beta”, “free”, “download”): 1 (2.9%)
- Fake reviews/comments: 1 (2.9%)

Key insights and trends

- Most misses are coverage problems, not model capability issues: brand catalog gaps and weak NRD/domain monitoring are the top drivers (32.3% if you group “brand coverage” + “domain age/NRD”). Strengthening brand/NRD monitoring should materially improve recall.
- A second cluster is e‑commerce fraud patterns (pricing anomalies, fake images, payment frictions), suggesting a reusable “fake shop” heuristic bundle will catch many variants with low effort.
- Multiple items point to device‑targeted templates (mobile‑only, invite‑code flows) and basic page‑quality signals (broken/one‑page sites), both of which are straightforward to operationalize.
- Evasion is present but under‑addressed (cloaking, redirects, shorteners). Handling multi‑geo/user‑agent variance and fully resolving redirect chains should reduce false negatives.
- Domain/URL/WHOIS features remain high‑value for ML and rule‑based detection; several of your notes map directly to established phishing features (domain age, lexical brand terms, redirects, lure keywords).

### actionable improvements
Actionable detection improvements

1. Brand and NRD coverage (highest impact)

- Data sources and monitors
    - Stand up continuous NRD + CT log monitoring to auto‑surface lookalikes and new domains containing brand terms or likely typos; generate tickets to add uncovered brands (e.g., Nequi, Fiverr, Clash Royale)
- Rules/features
    - Fuzzy brand matching on domain, subdomain, path, title, and on‑page text; boost score when brand term appears in domain or certificate SANs; decay with domain age.
    - Auto‑suggest brand additions when repeated detections mention “missing brand.”

2. URL/WHOIS/Content ML features (model + rules)

- Core features to add/weight
    - Domain age buckets (NRD ≤7/30 days; unusual “>90 days affiliate” pattern), registrar reputation, nameserver churn, URL lexical signals (brand terms, homoglyphs), redirect depth, and “lure keywords” (beta/free/download). National Institutes of Health (.gov)+2.
    - Page content signals for fake shops: steep/broad discounts, single or missing payment methods, Outlook/Gmail contact, repeated image hashes, low SKU counts, inconsistent pricing banners.
- Pipeline
    - Use these as both rules (fast‑fail) and as features in the risk model; calibrate with Platt scaling/thresholds per category.

3. Cloaking and geo‑fencing resilience

- Crawling strategy
    - Multi‑region crawling (residential and datacenter IPs) + diverse user agents/device profiles; compare DOM/response fingerprints across fetches to flag cloaking deltas. SpoofGuard+3.
    - Detect common cloaking artifacts (conditional UA checks, IP allow/deny logic, injected anti‑bot scripts, CAPTCHA‑gates) and fall back to anti‑cloaking routines. Cofense+1.

4. Redirects and shorteners

- Always resolve full redirect chains (HTTP 3xx, meta refresh, JS location, HSTS upgrades) before final classification; unshorten known shorteners (e.g., ct.ws) and score the landing page. National Science Foundation (.gov)+1.
- Persist the chain as a feature vector (count, types, domains traversed) for both rules and ML. National Science Foundation (.gov)+1.

5. Template/mobile‑only patterns

- Heuristics
    - Detect mobile‑only rendering (viewport/CSS/UA gates), “invite code” fields, and common kit assets; treat as a template‑phish feature bundle. NordVPN+1.

6. E‑commerce scam bundle (“fake shop”)

- Rules/features
    - Price anomaly detector (global or category‑relative discounts), single/absent payment method badges, mismatched policy pages, stock image reuse, broken trust seals, inconsistent currency, and low SKU diversity.
    - Elevate risk if the main domain is empty but a subpath/subpage is the only active shop page.

7. Site integrity/quality signals

- DOM/UX features
    - Dead links density, one‑page sites with empty root, broken layout indicators, repeated placeholder content, and “visual broken” cues aggregated into a single quality score.

8. External corroboration

- OSINT checks
    - Lightweight probes for social presence (brand‑consistent handles, age, activity) and reputation (VT telemetries) to adjust confidence and queue analyst review when signals contradict. DomainTools+1.

9. Platform/hosting and business legitimacy

- Expand coverage to popular builders (e.g., Wix) with platform‑specific cues (theme IDs, app manifests).
- Company registry cross‑checks (when available) to validate purported businesses before lowering risk.


### Most important points:
3 new brands - Fiverr, Nequi, Clash royale
Implement Newly registered domain ingestion as at least 8 cases mention young domain age, only 2 of those were ingested and marked as clean by Avira.
Almost ~20% are fake shops, seems like we need to double-check our internal Fake Shop ML. Mentioned indicators - steep discounts, fake images, single/absent payment methods, Outlook contact, same price for all products.

## Meeting notes
Define labels, double-check and make sure the logic makes sense
To ask akvile if we can query if a domain was sent to DRS automatically within a longer time range
Check if BBB has an API that could be used by us for a legitimacy check
Add hits calculation to FP/FNs
In Domain Curator, if we see a link shortener, we should check the final URL and, if possible, pay more attention to it. Mark the whole redirect chain with the final reputation.
To ask if we can query ScamML metadata about a domain.
Email blacklist as an indicator.
One platform to call all metadata from our internal services.
Traces for our domains that would store metadata from our internal services.
We should store cases that were unblocked during the review.
