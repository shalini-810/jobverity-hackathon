# Omnikon-Hackathon

**JobVerity**

JobVerity is an AI-powered platform that detects fake job postings and recruitment scams in real time. Paste a job posting's text, a URL, or even a screenshot — JobVerity scores it for scam risk and explains exactly why, so job seekers can catch red flags before they apply, interview, or share personal information.

# Features
1. Paste-to-score — paste raw job posting text and get an instant risk score with a plain-English breakdown of red flags.
2. URL scraping — submit a job posting link (LinkedIn, Naukri, Indeed, company career pages) and JobVerity scrapes and analyzes it automatically.
3. Screenshot fallback — for pages that block scraping or render content behind login walls, upload a screenshot instead; a vision model reads and scores it the same way.
4. Plain-English explanations — every score comes with a human-readable reasoning summary (e.g. "recruiter email doesn't match company domain," "asks for a refundable deposit before training") instead of an opaque number.
5. Crowdsourced flagged-company database — scam companies flagged by any user are added to a public leaderboard visible to everyone, so the platform gets more protective as more people use it.


# Tech Stack
Technology & Role
Next.js	- Frontend UI and backend API routes in a single project (posting input, scoring endpoint, results display)
Cheerio	- Lightweight scraping of static HTML job posting pages
Playwright - Headless-browser scraping for JavaScript-rendered pages, and full-page screenshot capture as a fallback
Groq API (Llama 3.3 70B) - Generates the plain-English explanation from the matched risk signals
Groq Vision model -	Reads job posting screenshots when text scraping isn't possible
Supabase (Postgres) -	Stores scored postings and the crowdsourced flagged-company leaderboard
whois-json - Checks domain registration age to flag newly-created domains impersonating real companies
Tailwind CSS - Styling
Vercel - Hosting and deployment

# Architecture

                     ┌─────────────────────┐
                     │      User (Web)     │
                     │  paste text / URL / │
                     │      screenshot     │
                     └──────────┬──────────┘
                                │
                                ▼
                     ┌─────────────────────┐
                     │   Next.js Frontend  │
                     └──────────┬──────────┘
                                │
                                ▼
                     ┌───────────────────────┐
                     │  Next.js API Routes   │
                     │  (scraping + scoring) │
                     └──────────┬────────────┘
                 ┌──────────────┼──────────────┐
                 ▼              ▼              ▼
        ┌───────────────┐ ┌───────────┐ ┌──────────────┐
        │ Cheerio /     │ │ whois-json│ │ Groq Vision  │
        │ Playwright    │ │ (domain   │ │ (screenshot  │
        │ (scraping)    │ │ age check)│ │  fallback)   │
        └───────┬───────┘ └─────┬─────┘ └──────┬───────┘
                └───────────────┼──────────────┘
                                ▼
                     ┌──────────────────────┐
                     │   Risk Engine        │
                     │ (rule-based scoring) │
                     └──────────┬───────────┘
                                │
                                ▼
                     ┌─────────────────────┐
                     │  Groq LLM (explain) │
                     └──────────┬──────────┘
                                │
                                ▼
                     ┌────────────────────────┐
                     │      Supabase          │
                     │  postings + flagged    │
                     │  companies leaderboard │
                     └──────────┬─────────────┘
                                │
                                ▼
                     ┌────────────────────────┐
                     │  Results shown to user │
                     │  + public leaderboard  │
                     └────────────────────────┘

# Limitations & Future Improvements
1. **Rule-based scoring, not ML-trained** — the risk engine currently uses weighted keyword and pattern matching rather than a trained classifier. A future version could train on labeled datasets (e.g. public "real vs. fake job posting" datasets) for more nuanced detection.
2. **Scraping reliability varies by platform** — sites like LinkedIn actively block automated scraping, so results for some platforms fall back to screenshot analysis, which is slower and less precise than direct text extraction.
3. **No support for private/platform-locked posts** — job posts inside Facebook groups, Instagram DMs, or other login-walled spaces can't be auto-fetched; users must currently paste the text manually.
4. **Screenshot analysis is early-stage** — the vision fallback works but hasn't been tested across a wide variety of posting formats and layouts yet.
5. **Crowdsourced data has no verification layer** — flagged companies are currently based on risk scores alone, with no moderation or dispute process, which could lead to false positives affecting a real company's reputation.
6. **Planned improvements:** email inbox integration (with proper OAuth) to auto-scan received job offers, a browser extension for one-click scoring while browsing job sites, and a lightweight ML model trained on real scam report data to supplement the rule-based engine.
