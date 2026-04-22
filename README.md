# Corey Ihler Real Estate — Website

Marketing site for Corey Ihler, real estate broker at Keller Williams North Sound, Marysville WA. Built as a static site for deployment on Cloudflare Pages.

**Live URL (once deployed):** https://coreyihlerrealestate.com

---

## Stack

- Pure static HTML + CSS + minimal vanilla JS. No build step.
- Deployed to **Cloudflare Pages** (auto-deploy on push to `main`).
- Fonts: Fraunces (serif) + Inter Tight (sans) via Google Fonts.
- Forms: vanilla JS, POST to `/api/submit-entry` (stub — must be wired to Cloudflare Worker or GHL webhook before launch).

---

## Structure

```
CoreyIhlerRealEstateWebsite/
├── public/
│   ├── index.html              ← Homepage
│   ├── monroe-giveaway.html    ← Canonical sweepstakes entry page (critical path)
│   ├── thank-you.html          ← Post-submission success state
│   ├── official-rules.html     ← Full sweepstakes Official Rules (stable URL, required for TCPA record)
│   ├── privacy.html            ← Privacy Policy (required for A2P 10DLC)
│   ├── terms.html              ← Terms of Use (required for A2P 10DLC)
│   ├── styles.css              ← Full design system
│   ├── _headers                ← Cloudflare Pages security/cache headers
│   ├── _redirects              ← Cloudflare Pages legacy-path redirects
│   ├── robots.txt
│   ├── sitemap.xml
│   └── images/                 ← Optimized (JPG, ~2MB total)
└── README.md
```

---

## Deploy Loop (proven from Trust Life Solutions)

1. Claude produces files in `/home/claude/CoreyIhlerRealEstateWebsite/`
2. Chris `rsync`s to `~/Projects/CoreyIhlerRealEstateWebsite`
3. `git add -A && git commit -m "..." && git push origin main`
4. Cloudflare Pages auto-deploys from `public/` in ~60s

**First-time setup:**
```bash
cd ~/Projects/CoreyIhlerRealEstateWebsite
git init
git remote add origin <repo-url>
# In Cloudflare Pages dashboard: Create project → Connect to Git
# Build output directory: public
# Build command: (none — static)
```

Domain setup in Cloudflare: `coreyihlerrealestate.com` purchased at Namecheap → nameservers pointed to Cloudflare → CNAME to the Pages project. Allow up to a few hours for DNS propagation, usually much faster.

---

## Critical pre-launch checklist (36-hour window, Monroe Home Show April 24–26)

### BLOCKING — must be done before booth opens Friday April 24

- [ ] Domain live at `coreyihlerrealestate.com` (Namecheap nameservers → Cloudflare)
- [ ] Cloudflare Pages project connected to Git repo, building from `public/`
- [ ] TLS certificate provisioned (automatic via Cloudflare, verify before booth)
- [ ] Form endpoint wired — either Cloudflare Worker writing to D1/KV + forwarding to GHL webhook, or direct GHL native webhook
- [ ] End-to-end test: submit a real entry from the actual tablet that will be at the booth, verify it lands in GHL and consent metadata is captured
- [ ] Paper backup entry cards printed (fallback if wifi fails at fairgrounds — this happens)

### IMPORTANT — best to have, but booth can operate without

- [ ] Official Rules reviewed one more time by the broker (already pre-cleared per Chris)
- [ ] GHL tags configured per consent type: `sweepstakes-entry`, `mkt-opt-corey`, `mkt-opt-tls`, `mkt-opt-mcv`
- [ ] QR code generated pointing at `/monroe-giveaway` — printed large on booth signage
- [ ] Booth staff briefed: NEVER check consent boxes on behalf of entrants, NEVER say "just check all three to speed it up"
- [ ] TLS teaser page built on trustlifesolutions.com pointing at canonical form (can wait until after weekend if needed)
- [ ] MCV teaser page built on millcreekview.us pointing at canonical form (can wait)

### POST-LAUNCH — next 2 weeks

- [ ] Submit A2P 10DLC campaign via GHL Trust Center Fast Track (Chris has TLS approved already; Corey needs his own brand if a separate legal entity, or can use Chris's brand under co-sponsor relationship depending on GHL's interpretation — confirm with GHL support)
- [ ] Add niche pages: `/probate-sales`, `/divorce-sales`, `/pre-foreclosure`
- [ ] Add case study detail page at `/case-studies/silver-lake`
- [ ] Wire the Cloudflare Worker to persist full legal records (4+ year retention)
- [ ] Add Plausible or similar privacy-friendly analytics

---

## Form submission — legal record requirements

Every entry submission MUST log (enforced client-side, must be persisted server-side):

- `timestamp` — ISO 8601 with timezone
- `userAgent` — browser string
- `referrer` — where the entrant came from
- `pageUrl` — exact URL of the form
- `consentVersion` — version hash of the consent text block (currently `2026-04-22-v1`)
- `language`, `screenSize` — environmental context
- All field values
- State of every checkbox (required + all three optional)

This is the TCPA/CTIA/A2P compliance record. Retain for **4+ years minimum**. If consent text is ever modified on the form, increment `CONSENT_VERSION` in the monroe-giveaway.html script and archive the old HTML in git — this establishes "what did the plaintiff actually see" in the event of any dispute.

The client-side script falls back to `localStorage` if the network request fails (offline tablet scenario at the fairgrounds). Booth staff should be trained to check `localStorage.getItem('giveaway_entries_failed')` at end of shift if the tablet had connectivity issues.

---

## Consent architecture reference

- **2 REQUIRED checkboxes:** eligibility + terms/privacy (both must be checked to submit)
- **3 OPTIONAL checkboxes:** marketing from Corey, TLS, MCV (each fully independent, each names its own service providers, each discloses AI-assisted and automated messaging)
- Phone number is **optional** unless an entrant opts into any marketing (A2P 10DLC carrier rule)
- Marketing consent is **never required** to enter or win (TCPA rule, anti-rebating rule for insurance)

---

## Known placeholders and TODOs

- Form POSTs to `/api/submit-entry` — this endpoint does not yet exist; the script currently writes to localStorage as a fallback so booth ops aren't blocked. Wire to real endpoint before A2P approval lands.
- Social media icons in homepage nav/footer are stubbed — add real URLs when ready.
- No niche pages yet (probate, divorce, pre-foreclosure) — these are v2 priorities post-Monroe.
- No case study detail page for Silver Lake yet — homepage section is sufficient for launch.
- KW North Sound logo currently served as JPG with white background; could be optimized to transparent PNG if KW provides one.

---

## Contact for this project

**Corey Ihler** — (425) 299-0960 · corey.ihler@kw.com
**Chris Ihler** — tech/automation lead
**License:** WA Real Estate Broker #22023575
**Brokerage:** Keller Williams Realty North Sound, 1027 State Avenue, Suite 102, Marysville, WA 98270

---

*Each Keller Williams® office is independently owned and operated.*
