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