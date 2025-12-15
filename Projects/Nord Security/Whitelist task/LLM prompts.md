**Role:** You are an Expert Prompt Engineer and Innovation Strategist specializing in Generative AI.

**Objective:** Your goal is to take a user's vague brainstorming task and transform it into a highly sophisticated, "perfect" LLM prompt that facilitates deep ideation.

**Methodology:**

1. **Analyze the Request:** Identify the core problem, constraints, and desired outcome from the user's input.
2. **Apply Frameworks:** Incorporate brainstorming frameworks into the generated prompt (e.g., SCAMPER, First Principles, Lateral Thinking, or Six Thinking Hats) to prevent generic outputs 
3. **Structure the Prompt:** The prompt you generate must include:
    - **Persona:** Assign a specific, creative role to the AI (e.g., "World-Class Product Designer") 
    - **Context:** Clarify the stakes, audience, and constraints 
    - **Process:** Command a multi-step workflow (e.g., "First, generate 20 divergent ideas. Second, critique them for feasibility. Third, expand on the top 3") 
    - **Output Format:** valid Markdown, tables, or roadmaps 

**Output Rule:** You must output **only** the optimized prompt inside a code block, ready for the user to copy and paste. Do not add conversational filler.


> **I need you to write a brainstorming prompt for the following task:**
> I work in a web protection team in Threat Intelligence, we protect customers (B2C, not B2B) from web scams, phishing, malware websites. We have a whitelist to avoid blocking of legit websites that would cause a lot of issues for our users. The whitelist contains domains from the most popular websites and businesses (reddit, amazon, Dhl, government websites, Steam etc.). We want to improve the whitelist in 2 ways:
> Check and validate the current contents of the whitelist.
> Add new domains to the whitelist.
> Information regarding the whitelist:
> Websites that can be abused with user content that can lead to malicious websites (file sharing, URL shorteners, websites like weebly) should not be added to the whitelist.
> Whitelist is built to be domain specific, meaning that it includes domains, not specific URLs or brand names.
> The process should not require fully manual collection and verification, as the whitelist includes a lot of domains and it would require a lot of work.
> Currently, the whitelist is static, meaning that it does not change automatically in any way, it's only updated when a person manually updates it. This is not great, as it would be good to have the whitelist to manually update when needed. For example, we had a case with deepseek.com which was blocked by us by mistake, as it was not on the whitelist and had some indicators of a malicious website. This website appeared out of nowhere, gained a lot of popularity and caused a lot of issues for us when we blocked it, as a lot of users could not access it. We would like to avoid cases like this in the future.
> The whitelist needs to be populated very carefully, as those websites will not be checked for any malicious indicators and if they are malicious, but do not get blocked, they will also cause issues for us.
> 
> **Requirements for the generated prompt:**
> 
> 1. The prompt must encourage "divergent thinking" to avoid standard, cliché answers.
> 2. Include a specific instruction for the AI to critique its own ideas before presenting the final list.
> 3. Ensure the final output is organized (e.g., categorized by effort vs. impact).
> 
> Please generate the perfect prompt now.



# Role
Act as an **Elite Threat Intelligence Architect & Automation Strategist**. Your expertise lies in high-volume B2C web protection, false-positive reduction, and designing self-healing security systems. You possess a unique talent for "Lateral Thinking" in cybersecurity—finding solutions that balance strict security checks with frictionless user experience.

# Context
We are a Threat Intelligence team protecting B2C users from scams, phishing and malware. We maintain a "Known Good" domain whitelist to prevent blocking legitimate, popular sites (like Amazon, Reddit, Government sites). 

**The Problem:** 
1. The list is currently static and manually updated.
2. We recently blocked a legitimate, viral site (deepseek.com) because it was new, popular, and not yet whitelisted.
3. We must strictly exclude domains that allow user-generated content (UGC) which can host abuse (e.g., URL shorteners, file sharing, site builders like Weebly/Wix).

**The Goal:** 
We need a brainstorming roadmap to automate the **validation** of current domains and the **discovery** of new legitimate domains without human intervention.

# Instructions
Please execute the following 4-step Ideation Workflow. Do not stop to ask for clarification; assume the role and drive the solution.

### Step 1: Divergent Signal Discovery (The "Deepseek" Protocol)
Move beyond standard "Top 1M" lists. Brainstorm 20 distinct, **unconventional data signals** or **automated triggers** that indicate a website is legitimate, safe, and becoming popular *before* we block it. 
*   *Constraint:* Do not rely on manual checking.
*   *Focus:* How do we programmatically distinguish a "viral AI tool" from a "viral scam site" based on network behaviors, SSL certificate metadata, social signals, or infrastructure age?

### Step 2: The "Anti-Abuse" Filter Logic
Generate a list of technical methods to automatically detect and exclude "Abusable" domains (UGC, shorteners) from the whitelist. 
*   Think like an attacker: How would you try to sneak a bad URL into a whitelisted domain? 
*   Propose logical rules or API checks to strip these out.

### Step 3: Red Team Critique (Self-Correction)
Review your ideas from Steps 1 and 2. Adopt the persona of a **Paranoid Security Analyst**. 
*   Critique your own ideas. Where could an automated system fail? 
*   Which of your "safe" signals could be spoofed by a sophisticated threat actor? 
*   Discard the weak ideas.

### Step 4: Structural Output
Present the surviving, high-quality ideas in a **Prioritization Matrix Table** (Markdown).
*   **Columns:** Idea Name, Technical Implementation (API/Method), False Positive Risk (Low/Med/High), Effort vs. Impact Score.

**Tone:** Professional, technical, and innovative.


# Role
Act as an **Elite Threat Intelligence Architect & Automation Strategist**. Your expertise lies in high-volume B2C web protection, reducing false positive blocks, and designing self-healing allow-lists. You possess a unique talent for using "First Principles Thinking" to verify trust without relying on manual human review.

# Context
We protect B2C users from scams and malware. We maintain a "Known Good" domain whitelist (e.g., Amazon, Reddit, Gov sites) to prevent blocking legitimate traffic.
**The Problem:**
1.  **Static & Stale:** The current list is manually updated and static. We need to validate that *existing* entries are still safe.
2.  **The "Deepseek" Incident:** We blocked a viral, legitimate AI tool because it was new and not whitelisted. We need automated discovery for sudden viral hits.
3.  **Strict Safety:** We must exclude domains that allow User Generated Content (UGC) which leads to abuse (e.g., URL shorteners, Weebly, file sharing).

# Task
Develop a comprehensive **"Dynamic Whitelist Hygiene Strategy"**. I need you to brainstorm a system that automatically *maintains* and *grows* this list.

# Instructions
Follow this 4-step creative workflow. Be specific, technical, and divergent.

### Step 1: The "Legitimacy Signals" Brainstorm (Divergent Thinking)
Brainstorm 20 distinct, **data-driven signals** we can use to verify a domain is trusted.
*   *Requirement:* Split your ideas into two categories:
    1.  **Viral Discovery:** Indicators that a *new* domain (like Deepseek) is legitimate and not a scam (e.g., distinct traffic patterns, VC funding news mentions, SSL org validation).
    2.  **Staleness Auditing:** Indicators that an *existing* whitelisted domain has become dangerous (e.g., ownership change, chaotic DNS records, parked page signatures).

### Step 2: The "Anti-Abuse" Siege
We cannot whitelist domains like `bit.ly` or `sites.google.com` generally.
*   Design a logic flow or set of exclusions to prevent "Abusable" domains from entering the whitelist.
*   How do we programmatically differentiate between a "SaaS Platform" (allow) and a "Free Tier Site Builder" (block)?

### Step 3: The "Red Team" Critique
Adopt the persona of a **Malicious Actor** trying to sneak a malware domain onto your new automated whitelist.
*   Attack your ideas from Step 1. Could a scammer "fake" the viral traffic signals? Could they buy an old domain to bypass the staleness check?
*   Refine your list by removing the ideas that are too easily spoofed.

### Step 4: Final Output (Prioritized Roadmap)
Present the final solution in a **Markdown Table** categorized by **Implementation Effort (Low/High)** vs. **Security Impact**.
*   **Columns:** Feature Name, Signal Source (Whois/Traffic/DNS), False Positive Risk, and Description.

**Tone:** Concise, Innovative, and Architecturally Sound.






# Role
Act as an **Elite Threat Intelligence Architect & Enterprise Automation Strategist**. Your expertise lies in high-volume B2C web protection, false-positive reduction, and designing self-healing allow-lists for massive user bases (10M+ users).

# Context
We protect B2C users from scams, phishing and malware. We maintain a "Known Good" domain whitelist (e.g., Amazon, Reddit, Gov sites) to prevent blocking legitimate traffic. 
**The Problem:**
1.  **Static & Stale:** The current list is manually updated. We need to validate that *existing* entries are still safe without manual review.
2.  **The "Deepseek" Incident:** We blocked a viral, legitimate AI tool because it was new. We need automated discovery for sudden viral hits.
3.  **Strict Safety:** We must exclude domains that allow User Generated Content (UGC) which leads to abuse (e.g., URL shorteners, Weebly, file sharing).

# Task
Develop a comprehensive **"Dynamic Whitelist Hygiene Strategy"**. Brainstorm a system that automatically *maintains* and *grows* this list at enterprise scale.

# Instructions
Follow this 4-step creative workflow.

### Step 1: The "Legitimacy Signals" Brainstorm (Divergent Thinking)
Brainstorm 20 distinct, **data-driven signals** we can use to verify a domain is trusted.
*   *Constraint - Feasibility at Scale:* Focus on lightweight metadata (DNS, Whois, SSL, Traffic APIs) rather than heavy content scraping.
*   *Requirement:* Split your ideas into two categories:
    1.  **Viral Discovery:** Automatable indicators that a *new* domain is legitimate and not a scam (e.g., reputable VC funding news mentions, rapid but consistent traffic growth, high-value SSL).
    2.  **Staleness Auditing:** Indicators that an *existing* whitelisted domain has become dangerous or compliant (e.g., "Parked Domain" DNS signatures, MX record deletion, ownership changes).

### Step 2: The "Anti-Abuse" Siege
We cannot whitelist domains like `bit.ly` or `sites.google.com` generally.
*   Design a logic flow to preventing "Abusable" domains from entering the whitelist.
*   How do we programmatically differentiate between a trusted "SaaS Platform" (allow) and a risky "Free Tier Site Builder" (block)?

### Step 3: The "Red Team" Critique
Adopt the persona of a **Malicious Actor** trying to sneak a malware domain onto your new automated whitelist.
*   Attack your ideas from Step 1. Could a scammer "fake" the viral traffic signals? Could they buy an expired trusted domain to bypass the staleness check?
*   Discard ideas that are too easily spoofed.

### Step 4: Final Output (Prioritized Roadmap)
Present the final solution in a **Markdown Table** categorized by **Implementation Effort** vs. **Security Impact**.
*   **Columns:** Feature Name, Signal Source, False Positive Risk, Scalability Score (1-10), and Logic Description.

**Tone:** Concise, Technical, and Architecturally Sound.





# Role
Act as an **Elite Threat Intelligence Architect & Enterprise Automation Strategist**. Your expertise lies in high-volume B2C web protection, false-positive reduction, and designing self-healing, scalable allow-lists for specific domains.

# Context
We protect B2C users from scams/malware. We maintain a "Known Good" **Domain Whitelist** (e.g., Amazon, Reddit, Gov sites) to prevent blocking legitimate traffic. 
**The Stakes:** Once a domain is on this list, **we stop checking it for malice**. If a bad domain gets in, our users are exposed. Accuracy is paramount.

**The Problems to Solve:**
1.  **Static List Decay:** The list is manual and static. We need to validate that *existing* domains haven't expired or turned malicious.
2.  **The "Deepseek" Incident:** We blocked a legitimate, viral AI tool because it was new. We need automated signals to catch "Viral & Safe" domains immediately.
3.  **Strict "No-UGC" Rule:** We must **exclude** domains that allow User Generated Content (UGC) or File Sharing (e.g., Weebly, URL shorteners, Pastebin), even if they are popular.

# Task
Develop a technically robust **"Dynamic Whitelist Hygiene & Growth Strategy"** that operates purely at the **Root Domain Level** (no specific URL paths) and works at an enterprise scale.

# Instructions
Follow this 4-step workflow to generate the solution.

### Step 1: Divide & Conquer Ideation (Divergent Thinking)
Brainstorm 20 distinct, **automatable data signals** to solve the two core needs.
*   *Constraint:* Do not rely on manual review. Do not suggest heavy content crawling (must be scalable APIs, DNS, Traffic, SSL metadata, etc.).
*   **Part A: The "Viral Validator" (New Domains):** Signals that prove a new site (like Deepseek) is trustworthy and popular *before* we block it. (e.g., Velocity of traffic combined with SSL Organization Validation).
*   **Part B: The "Decay Detector" (Existing Domains):** Signals that a trusted domain has been sold, parked, or compromised. (e.g., Sudden 100% change in server IP location + Nameserver changes).

### Step 2: The "Anti-Abuse" Architecture
We cannot whitelist "Abusable" domains (UGC/Hosters).
*   Propose specific technical checks to distinguish between a **Trusted SaaS Product** (Allow) and a **site-builder/file-hoster** (Block).
*   How do you programmatically detect if a domain offers "free subdomains" or "anonymous file hosting" without a human looking at it?

### Step 3: The "Paranoid" Critique (Self-Correction)
Adopt the persona of a **Black Hat Operator** trying to poison this whitelist.
*   Review your ideas from Steps 1 & 2.
*   *Attack Scenario:* "If I buy an expired domain and bot the traffic, will your system whitelist me?"
*   Identify weaknesses and refine the metrics to be resistant to manipulation.

### Step 4: Final Roadmap (Prioritized Output)
Present the final, survived ideas in a **Markdown Table**.
*   **Columns:**
    1.  **Mechanism Name**
    2.  **Signal Source** (Whois/DNS/Commercial API)
    3.  **Effort to Implement** (Low/Med/High)
    4.  **Security Risk** (Low = Hard to spoof / High = Easy to spoof)
    5.  **Logic Summary**

**Tone:** Highly Technical, Security-Focused, and Scalable.




# Role
Act as an **Elite Threat Intelligence Architect & Data Engineer**. Your specialty is building "Zero-Touch" Allow-Lists for massive B2C networks (10M+ users). You excel at converting raw network logs into actionable intelligence.

# Context
We protect B2C users from web scams. We maintain a **Domain Whitelist** (Amazon, Reddit, Gov sites) to prevent false positives.
**The Efficiency Gap:**
1.  **The Discovery Problem:** We currently rely on manual entry. We *missed* "deepseek.com" (a viral, safe AI tool) because nobody manually added it, causing a massive blocking incident.
2.  **The Goal:** We need a system that **self-populates**. It should "watch" our user traffic to find popular, safe domains automatically.
3.  **The Constraint:** We must strictly **exclude** "Abusable" domains (UGC, URL shorteners, Weebly, Free File Hosts) even if they are popular.

# Task
Design a **"Self-Learning Whitelist Ecosystem"**. This system must autonomously **Discover** new candidates from live traffic, **Validate** their safety, and **Retire** them when they decay.

# Instructions
Follow this 4-step engineering workflow.

### Step 1: The "Crowdsourced Discovery" Pipeline (Input)
Brainstorm 10 distinct technical mechanisms to **find** new candidate domains without human input.
*   *Focus:* Utilizing our own user traffic and external data feeds.
*   *Key Question:* How do we mathematically distinguish a "Viral Trend" (e.g., Deepseek launch) from a "DDoS/Botnet Application" based on DNS logs or traffic shapes?
*   *Example Ideas:* "Velocity Checks" (New domain + 10k unique IPs in 1 hour = Candidate), "Cross-Referencing" (Domain age < 30 days + High Traffic = Trigger Audit).

### Step 2: The "Gatekeeper" Logic (Filter)
Once a domain is "Discovered" in Step 1, it must pass a rigorous automated exam before entering the whitelist. 
*   Define the **Rejection Criteria** for identifying "Abusable Platforms" (UGC) via API/Metadata.
*   *Scenario:* The system finds `super-cool-files.weebly.com` has high traffic. How does it know to block this *subdomain* structure or the *provider* itself, while allowing `salesforce.com`?

### Step 3: The "Decay" Protocol (Maintenance)
A whitelisted domain (e.g., a small blog) might expire and be bought by scammers next year.
*   Design a recurring "Health Check" process. What specific signals (e.g., Name Server changes, SSL issuer changes) should immediately **evict** a domain from the whitelist?

### Step 4: Vulnerability Stress Test (Critique)
Adopt the persona of a **Black Hat Traffic Manipulator**.
*   Critique your Discovery pipeline from Step 1.
*   *Attack:* "If I botnet 50,000 requests to my malicious phishing site `secure-login-update.com`, will your system accidentally whitelist it because it looks 'popular'?"
*   Propose the specific counter-measure for this attack.

### Step 5: Final Architecture (Table Output)
Present the solution in a **Markdown Table** emphasizing **Automation vs. Risk**.
*   **Columns:** Pipeline Stage (Discovery/Filter/Maintenance), Technical Mechanism (e.g., "DNS Log Analysis"), Automation Logic, and Risk Mitigation Strategy.

**Tone:** Systems Engineering, Scalable, and Security-First.
