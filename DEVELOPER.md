# Developer Guide: Q-S AI-Generated Content Law and Policy Hub

## Architecture Overview

This is a static HTML site deployed via GitHub Pages from the `main` branch. There is no build step, no database, and no server-side logic. All legal content is stored directly in `index.html`.

### Key Files

| File | Branch | Purpose |
|------|--------|---------|
| `index.html` | `main` | Live site content — all legal data and UI |
| `archive/[date].html` | `main` | Historical snapshots of prior site states |
| `.github/workflows/static.yml` | `main` | GitHub Pages deployment workflow |
| `.github/workflows/weekly-update.yml` | `claude/setup-main-page-IbrtC` | Weekly auto-update trigger |
| `.github/prompts/weekly-update.md` | `main` (read at runtime) | LLM prompt driving the auto-update agent |
| `SECURITY.md` | `main` | Authentication limitation notice |

### Branch Structure

- `main`: deployed content. Every push to `main` triggers a GitHub Pages rebuild via `static.yml`.
- `claude/setup-main-page-IbrtC`: holds the scheduled workflow (`weekly-update.yml`). The workflow checks out `main` to do its work and pushes back to `main`.

---

## Auto-Update System

The weekly update workflow (`weekly-update.yml`) runs every Monday at 2pm UTC (cron: `0 14 * * 1`) and can also be triggered manually via `workflow_dispatch`.

**What it does:**
1. Checks out `main`
2. Reads `index.html` to determine the last update date
3. Runs the Claude Code CLI agent with the prompt from `.github/prompts/weekly-update.md`
4. The agent searches the web for confirmed U.S. AI governance developments since the last update
5. If significant developments are found: archives the current page, updates `index.html`, commits, and pushes to `main`
6. The push to `main` automatically triggers a GitHub Pages redeploy

**Runtime:** The agent uses the `ANTHROPIC_API_KEY` secret stored in GitHub repository secrets.

---

## Legal Data Storage

Legal records are stored as HTML within `index.html`. There is no separate database or JSON file. The structure is:

- **"What is live now" section**: Cards and a full table for enacted laws / official actions
  - Sub-section "In force now": laws currently operative
  - Sub-section "Enacted, not yet effective": laws signed but with a future operative date
- **"Federal watchlist" section**: Cards and a full table for federal proposals
- **"State proposal watchlist" section**: Cards and a full table for state proposals
- **"Commercial versus political classification" section**: Classification table

Each legal item ideally carries:
- Title and jurisdiction (in card header)
- Effective/operative date (in card body)
- Practical implication (in card body)
- Source link (in headline or body)
- Verification status badge (small badge near the source reference)
- Source metadata line (`.source-meta` paragraph at card bottom)

---

## Status Determination

### Operative Status Categories

| Category | Meaning | Displayed In |
|----------|---------|-------------|
| In force now | Enacted and currently operative | "In force now" subsection |
| Enacted, not yet effective | Signed/enacted but operative date is in the future | "Enacted, not yet effective" subsection |
| Proposed / pending | Not yet enacted | Federal or state watchlist sections |

**Critical rule:** A law is "in force now" only if its operative date has passed as of the site's update date. A law with a future operative date — even if signed — must appear in "Enacted, not yet effective." The counter labeled "Currently in force" must count only currently operative items.

### Verification Status

| Badge | Meaning |
|-------|---------|
| PRIMARY SOURCE | Material legal facts verified against an official source (legislature, Federal Register, court record, official agency page) |
| NEEDS REVIEW | Effective date, status, or key fact came from a secondary source only, or sources conflict |
| SECONDARY SOURCE | Development discovered via secondary source; primary source not yet confirmed |

**A record is VERIFIED only when a human or the update agent has located the actual official source and confirmed the key facts against it.** A confident automated summary is not verification.

---

## Source Authority Tiers

The update agent uses a three-tier source hierarchy:

**Tier 1 — Primary authority (preferred for all material legal facts):**
- Official state legislature websites (leginfo.legislature.ca.gov, etc.)
- Congress.gov
- Federal Register (federalregister.gov)
- Official regulatory agency pages (ftc.gov, fcc.gov, etc.)
- Official governor / executive action records
- Enrolled bill text, chaptered laws

**Tier 2 — Strong secondary (useful for interpretation and context):**
- Established law firm client alerts
- Recognized legal publications
- Authoritative policy research organizations
- Major news organizations reporting on legislative developments with primary source citations

**Tier 3 — Discovery sources (useful for identifying something to investigate; not sufficient to establish legal status):**
- AI law trackers and aggregators
- Industry newsletters
- Advocacy site summaries
- Social media

**Rules:**
- Tier 1 always controls when it conflicts with Tier 2 or Tier 3.
- Tier 3 alone cannot establish an effective date, enacted status, or statutory requirement.
- When a primary source cannot be confirmed, the record must be marked NEEDS REVIEW, not published as established law.

---

## Historical Snapshots

When the auto-update runs on date X and finds significant developments:
1. It archives `index.html` to `archive/X.html` (named after today, containing the previous state).
2. It updates `index.html` with new content.
3. It updates all existing archive files' version menus to add a link to the new current page.

This means `archive/X.html` preserves the exact state of the site just before update X ran.

**Do not overwrite existing archive files.** The archiving mechanism preserves a full record of how legal statuses changed over time. If a correction must be made to a past archive for accuracy, note the correction in a comment inside the file.

---

## How to Manually Correct a Legal Record

1. Check out `main` locally or edit directly via the GitHub UI.
2. Find the relevant card and table row in `index.html`.
3. Make the correction using the primary source as your authority.
4. Update the source link to point to the specific official page, not a homepage.
5. Update the verification status badge to PRIMARY SOURCE if you verified against an official source.
6. Update the `data-verified` attribute or source-meta line with today's date.
7. If the correction is significant (wrong effective date, wrong status), add a brief note in the What's New section under the current update date.
8. Commit with a message that explains what was corrected and why.

---

## How to Mark an Item for Human Review

Change or add the verification badge to `NEEDS REVIEW`:
```html
<span class="badge review-needed">NEEDS REVIEW</span>
```
Add a source-meta note:
```html
<p class="source-meta">Source conflict or unverified date. Primary source not confirmed. Human review recommended before next publication.</p>
```

---

## Summary Counters

The "Current snapshot" section shows four counts. These must match the underlying data:

- **Currently in force**: count rows in the enacted-law table (`<details>` under "What is live now") that have a past effective/operative date.
- **Enacted, not yet effective**: count enacted items with a future operative date.
- **Federal proposals**: count rows in the federal tracker table.
- **State proposals**: count rows in the state tracker table (excluding items that have died without enactment).

The agent is instructed to recount rows in the enacted-law table after making changes and update the counter accordingly. **Do not manually maintain a separate count that could drift from the table.**

---

## Authentication Limitation

The site currently uses a client-side password gate only. This is visual, not functional security. See `SECURITY.md` for the full explanation and recommended remediation (Cloudflare Access or equivalent).

**Until real authentication is in place, treat all site content as publicly accessible.**

---

## Deployment

Every push to `main` automatically triggers `static.yml`, which deploys the static files to GitHub Pages at `https://joetol2.github.io/ai_transparency/`.

There is no staging environment. Changes to `index.html` go live immediately after the GitHub Pages build completes (typically 1-3 minutes after the push).
