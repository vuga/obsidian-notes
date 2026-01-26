## What to use
Yandex images API
## What to extract
Do **NOT** send the whole page screenshot to the search engine. You must send **Segmented Components**.
**The Solution (Component Extraction):** Your headless browser (Puppeteer/Playwright) should extract and send these three specific artifacts:

1. **The Logo Candidate:** Crop the top-left 200x100px area or extract the largest image in the `<header>`.
2. **The "Hero" Image:** The largest image visible above the fold (often the "Bank Building" or "Generic Support Staff").
3. **The Favicon:** This is the highest-signal/lowest-cost artifact to search

## Must contain password
_Action:_ Valid candidate pages MUST contain at least one visible `<input type="password">` OR keywords like "Connect Wallet" / "Verify Identity" in the DOM.