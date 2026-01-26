Here is the comprehensive Operational Proof of Concept (PoC) Plan for **"Visual Infrastructure Pivoting."** This document is structured to be handed directly to an engineer or a new joiner to build the system from scratch.

### **Objective**
To transform our "Reactive" blocklist into a "Proactive" discovery engine. Instead of waiting for users to visit new phishing sites, we use the visual assets of *known* phishing sites to find their identical clones (kits) hosted on other domains before they are active.

---

### **Phase 1: Data Ingestion & Curation (The Input Funnel)**
**Goal:** Select the highest-quality "Seeds" for the search while minimizing cost and noise. We cannot scan every blocked site; we must scan unique *templates*.

**Step 1.1: Source Selection**
*   **Input:** Pull all domains blocked as `Phishing`, `Scam`, or `Malware` by our internal systems in the last 24 hours.
*   **Nuance:** Exclude "parked" domains or empty responses (HTTP 404/50x).

**Step 1.2: deduplication via DOM Hashing**
*   **Problem:** Phishing campaigns deploy the same kit on 1,000 domains. Scanning all 1,000 is redundant.
*   **Action:**
    1.  Render the HTML of the bad page.
    2.  Strip dynamic content (timestamps, CSRF tokens).
    3.  Generate a **Fuzzy Hash** (using `SSDEEP` or `TLSH`) of the HTML structure.
    4.  **Logic:** Group URLs by Hash. Select **1 Representative URL** per group (prefer the one with the fastest load time).
*   **Result:** Reduces input volume by ~90-95%, focusing only on unique *visual templates* .

**Step 1.3: Validity Pre-Check (The "Solicitation" Filter)**
*   **Problem:** Some blocked sites are just broken or text-only.
*   **Action:** The Representative URL must pass the **"Solicitation Trigger"** to be scanned. It must contain at least ONE of:
    *   **Input Fields:** `<input type="password">`, `<input name="cc_number">`, `<input name="ssn">`.
    *   **Keywords:** Regex match for `/(verify|unlock|wallet|connect|payment|suspend|unauthorized)/i`.
    *   **Urgency:** Presence of countdown timers or JavaScript redirectors.

---

### **Phase 2: Intelligent Asset Extraction (The Crawler)**
**Goal:** Extract the specific visual "fingerprints" that attackers reuse.
**Tool:** Headless Browser (Puppeteer/Playwright).

**Step 2.1: The Favicon (Golden Artifact)**
*   **Logic:** Attackers often host a fake bank site on `wordpress.com` but force the browser to load the "Chase Bank" icon to look legit.
*   **Extraction:**
    *   Check `<link rel="icon">`.
    *   Fallback: Try fetching `/favicon.ico` directly.
    *   **Nuance:** Hash the image file. If it matches a known "Generic Hosting" icon (e.g., the default React or Wordpress globe), **discard it**. We only want custom brands .

**Step 2.2: The "Hero" Image (The Anchor)**
*   **Logic:** We need the dominant visual element (e.g., the background image of the "Crypto Exchange" or the photo of the "Support Agent").
*   **Algorithm:**
    1.  Identify all visible images (`<img>` and CSS `background-image`).
    2.  **Filter:** Reject if `Width` or `Height` < 60px (removes social media icons/UI clutter).
    3.  **Score:** Calculate `Surface_Area` = (Visible Width × Visible Height).
    4.  **Select:** Pick the **Top 1** largest image.
    *   **Nuance:** Prioritize images loaded from *external* domains or those with random filenames, as these are often part of the kit.

**Step 2.3: The "Login Wrapper" Crop (Fallback)**
*   **Logic:** Some modern kits draw the logo using CSS code, so there is no image file to steal.
*   **Action:**
    1.  Locate the central interaction container (e.g., `<form>`, `.login-box`, or the central `<div>`).
    2.  Take a **Screenshot Crop** of just that element + 20px padding.
    3.  **Nuance:** Ensure the background color is captured (white text on a transparent background will fail in search engines).

---

### **Phase 3: The Threat Pivot (Execution)**
**Goal:** Use the extracted fingerprints to find the rest of the campaign.

**Step 3.1: Querying Yandex**
*   **Tool:** Yandex Images API (or scraping wrapper for PoC phase).
*   **Action:** specific query for **"Sites containing this image"**.
*   **Why Yandex:** It indexes "Scam Clusters" (exact duplicates on low-reputation hosts) better than Google, which aggressively de-indexes phishing pages .

**Step 3.2: Result Parsing**
*   **Expectation:** You will receive a list of URLs where this image exists.
*   **Filtering:**
    *   **Cluster A (The Victim):** URLs matching the official brand (e.g., `paypal.com`). **Ignore.**
    *   **Cluster B (The Clones):** URLs on strange domains (e.g., `paypal-verify-secure.xyz`). **These are your targets.**

---

### **Phase 4: Verification & Ingest (The Verdict)**
**Goal:** Minimize False Positives before adding to the blocklist.

**Step 4.1: The Whitelist Check (Critical)**
*   **Action:** Check all found Domains against the **Alexa/Tranco Top 100k**.
*   **Logic:** If the image is found on `amazon.com`, it is legitimate. If found on `random-cloud-host.net`, it is suspicious.

**Step 4.2: The Blocklist Diff**
*   **Action:** Compare found URLs against your *existing* blocklist.
*   **Metric:**
    *   **Old Hits:** (Already blocked) = Validation of the method.
    *   **Net New:** (Not identifying) = **0-Day Discoveries.**

**Step 4.3: Action**
*   **Immediate:** Send "Net New" URLs to the high-priority scanning queue for text/content analysis.
*   **Future state:** If confidence is >99% (e.g., same favicon, same kit hash, bad domain age), auto-block.

---

### **Summary of "Nuances" for Documentation**
1.  **Do NOT scan whole pages:** Full screenshots introduce too much noise (UI layouts). Focus on **isolated assets** (Logo, Crypto-Chart, Favicon) .
2.  **Visual Deduplication is Key:** If you don't group by DOM Hash first, you will burn your API budget scanning 5,000 identical "DHL" scams. Scan the *template*, not the instance .
3.  **Solicitation Triggers:** A page without an "Ask" (password/money) is rarely a Phishing priority. Use regex to ensure the seed is actually dangerous.









This is the final **Operational Proof of Concept (PoC) Plan** for the "Visual Infrastructure Pivoting" system. This document is structured for immediate use by engineering teams to build the PoC.

### **Project Name:** Visual Infrastructure Pivoting (VIP) PoC
**Objective:** Detect "Zero-Day" phishing campaigns by extracting visual fingerprints (logos, favicons, hero images) from known scam sites and reverse-searching them to identify unknown clones using the same kit.

---

### **1. Infrastructure & Prerequisites**
To avoid "Reinventing the wheel" during the PoC phase, we will use established libraries to handle complexity (rendering, CAPTCHAs, WHOIS).

*   **Runtime:** Python 3.9+.
*   **Browser Engine:** `Playwright` (Python) for headless rendering and smart cropping .
*   **Visual Search Proxy:** `SerpApi` (Yandex Engine).
    *   *Why:* Scraping Yandex directly triggers aggressive CAPTCHAs. SerpApi abstracts this, providing clean JSON results for "Reverse Image Search" .
*   **Similarity Hashing:** `ssdeep` or `tlsh` (for template clustering).
*   **Context Scanners:** `python-whois` (Domain Age) and our whitelist API.

**Directory Structure:**
```text
/vip-poc
├── input/
│   └── urls.txt            # Raw list of manually gathered phishing URLs
├── output/
│   ├── artifacts/          # Saved images (logos/favicons)
│   └── report.json         # Final intelligence dossier
├── src/
│   ├── extractor.py        # Playwright logic
│   ├── pivot_engine.py     # Yandex/SerpApi logic
│   └── analyzer.py         # Verdict logic (Whitelist/Age)
└── main.py                 # Orchestrator
```

---

### **2. Detailed Implementation Logic**

#### **Module A: Input Normalization (The Filter)**
**Goal:** Prevent burning API credits on duplicate templates (e.g., 50 identical DHL login pages).

1.  **Ingest:** Read lines from `input/urls.txt`.
2.  **Cluster:**
    *   Visit each URL using `requests`.
    *   Generate a perceptual hash of the HTML DOM (using `ssdeep`).
    *   **Logic:** Group URLs by hash similarity (>90%).
    *   **Selection:** Pick **one** representative URL per group to process.
3.  **Result:** A clean list of *unique* phishing templates.

#### **Module B: Intelligent Extraction (The Camera)**
**Goal:** Extract high-fidelity "Fingerprints" rather than noisy full-page screenshots.
**Tool:** `Playwright` (Headless Chromium).

*   **Step 1: The Favicon (Golden Signal)**
    *   Access `<link rel="icon">`. If empty, try `/favicon.ico`.
    *   **Constraint:** Compute MD5 hash of the downloaded icon.
    *   *Filter:* If hash matches "Default Wordpress" or "Default Apache" icon, **Discard**. We only want custom brand icons .

*   **Step 2: The Hero Image (The Anchor)**
    *   Execute JS to find all visible `<img>` and `div`s with `background-image`.
    *   **Filter 1 (Size):** Width > 150px AND Height > 150px (Ignores tiny icons).
    *   **Filter 2 (Aspect Ratio):** `Width / Height < 2.5`. (Removes Banner Ads and Headers) .
    *   **Filter 3 (AdBlock):** Exclude images with keywords `doubelclick`, `adsystem`, `promo` in the src URL.
    *   **Selection:** Sort remaining images by `Surface_Area` (Px²) and pick the Top 1.

*   **Step 3: The "Login Wrapper" (Fallback)**
    *   If no Hero Image is found, locate the `<form>` element.
    *   Take a specific **Element Screenshot** of the form + 50px padding.
    *   *Why:* Captures the specific "Login Box" styling unique to the phishing kit .

#### **Module C: Infrastructure Pivoting (The Search)**
**Goal:** Find where else these images exist on the web.
**Tool:** `SerpApi` (Yandex Images Endpoint).

1.  **Query:** Send the image URL (or upload binary) to SerpApi.
2.  **Params:** set `engine="yandex_images"` and `lang="en"`.
3.  **Response Parsing:**
    *   Focus on the `sites_containing_image` or `exact_match` JSON keys.
    *   **Ignore:** "Visual counterparts" (images that *look* similar but are not identical). We strictly want files that are **matches** .

#### **Module D: Threat Contextualization (The Verdict)**
**Goal:** Distinguish between a " Victim" (Legit Brand), an "Attacker" (Phish), and a "Bystander" (Random blog).

For every URL returned by Yandex:
1.  **Whitelist Check:** Is the domain in our whitelist?
    *   *Yes* → Mark as **LEGIT** (e.g., `amazon.com`).
    *   *No* → Proceed.
2.  **Blocklist Check:** Is the domain blocked via our reputation API?
    *   *Yes* → Mark as **KNOWN** (Ignore).
    *   *No* → Proceed.
3.  **Domain Age Check (The Tie-Breaker):**
    *   Perform `whois` lookup.
    *   *Logic:* IF `Creation_Date < 90 Days` → **Risk: CRITICAL** (Zero-Day Phishing).
    *   *Logic:* IF `Creation_Date > 365 Days` → **Risk: SUSPICIOUS** (Likely a hacked site or stock photo overuse).

---

### **3. Final Output Artifact**

The script will generate `report.json` containing actionable intelligence. This is what you analyze.

```json
{
  "scanned_template": "dhl-login-fake.com",
  "extracted_assets": {
    "hero_image": "s3-bucket/dhl-truck-bg.jpg",
    "favicon_hash": "a1b2c3..."
  },
  "pivot_results": [
    {
      "url": "secure-delivery-update.net",
      "status": "ZERO_DAY",
      "risk_score": 95,
      "reason": "Image Match + Domain Age (4 days old) + Not Whitelisted"
    },
    {
      "url": "dhl.com/global-logistics",
      "status": "CLEAN",
      "reason": "Whitelisted (Tranco #253)"
    }
  ]
}
```

### **4. Analysis Strategy for the PoC**
When you run this on your file of 50 URLs:
1.  **Success Metric:** How many `ZERO_DAY` sites did you find per `scanned_template`?
    *   *Target:* > 0.5 (Finding 1 new site for every 2 known sites scanned).
2.  **False Positive Check:** Manually review the `SUSPICIOUS` category. Did we flag a legitimate dentist's website because they used the same "Smiling Woman" stock photo as the scammer?
    *   *Adjust:* If yes, tighten the **Hero Image Filter** to require larger sizes or stricter aspect ratios.

### **5. Nuances for Documentation**
*   **The "Ad Blocker" Logic:** Ensure the extractor ignores `ads.google.com` images, or you will find millions of results for "Google Ads" instead of the scam .
*   **Residential Proxies:** Even with SerpApi, Yandex results vary by region. Ensure your API is set to query from a neutral region (e.g., US or Germany) to see global results .
*   **Rate Limits:** Do not assume realtime speed. This process is slow (30s+ per URL). It is designed for **Asynchronous Batch Processing**, not live user blocking.