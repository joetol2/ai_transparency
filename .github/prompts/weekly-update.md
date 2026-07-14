You are maintaining the Q-S AI-Generated Content Law and Policy Hub, a static HTML site at https://joetol2.github.io/ai_transparency/. The site tracks U.S. AI governance relevant to commercial advertising, digital replicas, synthetic media, and platform compliance. It is hosted via GitHub Pages from this repository's main branch.

Today's date is {{TODAY}}.

Your working directory is the repository root. Key files:
- `index.html` — the main page (current version)
- `archive/` — folder containing prior dated versions

---

## STEP 1: Read the current page

Read `index.html`. Find the version dropdown button text, which reads "Last updated: [date]". That is the cutoff date for your research. If that date matches today's date, stop immediately and output: "No update needed: page was already updated today."

---

## STEP 2: Research

Search the internet for confirmed U.S. AI governance developments since the cutoff date. Run multiple searches covering each of these areas:

**Federal legislation:**
- NO FAKES Act S.4591 — any Senate floor vote outcome or House companion movement
- COPIED Act S.1396 — any Senate Commerce Committee action
- TRAIN Act H.R.7209 — any House Judiciary action
- DEFIANCE Act S.1837 — any House floor vote
- Great American AI Act — any formal introduction or bill number assigned
- Any new federal bills introduced on deepfakes, synthetic media, digital replicas, AI advertising, or AI disclosure

**State laws:**
- Any new AI laws signed or newly effective in any U.S. state
- Focus on laws affecting commercial advertising, digital replicas, deepfakes, synthetic media, or platform liability

**Federal agency actions:**
- FTC enforcement actions on AI advertising, synthetic content, or fake endorsements
- FCC enforcement actions on AI-generated voice calls or AI disclosure rulemaking
- DOJ or DHS enforcement actions on deepfakes or synthetic media platforms
- NIST AI guidance or standards updates

**Executive branch:**
- White House executive orders or presidential memoranda on AI

**Courts:**
- Significant AI copyright rulings or digital replica decisions

Only report confirmed findings. Each finding must have a source URL from a .gov site, a congressional press release, or a credible news outlet. Do not speculate or include anything you cannot verify with a source.

---

## STEP 3: Decide whether to proceed

If you found fewer than 2 confirmed significant developments since the cutoff date, output a brief summary of what you searched and found, then stop without making any file changes.

If you found 2 or more significant developments, continue to Step 4.

---

## STEP 4: Archive the current page

1. Determine the archive filename using today's date in this format: `archive/[mon]-[dd]-[yyyy].html` where the month is 3-letter lowercase. Example: `archive/jun-23-2026.html`.
2. Copy `index.html` to that path using Bash.
3. Read the new archive file and make only these changes to it:
   - Change `<title>Q-S AI-Generated Content Law and Policy Hub</title>` to `<title>Q-S AI-Generated Content Law and Policy Hub - [Month Day, Year] Archive</title>`
   - Change the version dropdown button text from `Last updated: [old date]` to `Archived: [old date]`
   - Change `<span class="version-item current">[old date] - current</span>` to `<span class="version-item current">[old date]</span>`
   - Add `<a class="version-item" href="../index.html">{{TODAY}}</a>` as the first item inside the version menu div (before the current span)
4. For each existing HTML file in the `archive/` folder, open it and update its version menu: add `<a class="version-item" href="../index.html">{{TODAY}}</a>` as the first item in the version menu div, if it is not already there. Also update the existing item that currently links to `../index.html` to instead link to the newly created archive file for the old date.

---

## STEP 5: Update index.html

### Version dropdown
- Change the button text from `Last updated: [old date]` to `Last updated: {{TODAY}}`
- Change `<span class="version-item current">[old date] - current</span>` to `<span class="version-item current">{{TODAY}} - current</span>`
- Add a new `<a class="version-item" href="archive/[new-archive-filename]">[old date]</a>` as the first link below the current span and above any existing archive links

### What's new section lead paragraph
- Change "Updated [old date]." to "Updated {{TODAY}}."
- Change the reference to the prior snapshot date to match the old date

### What's new cards
Replace ALL existing cards with 6 new cards based on your research findings. The grid must keep the attribute `style="grid-template-columns: repeat(3, 1fr);"` so the cards display as 2 rows of 3.

Each card follows this structure exactly:
```html
<div class="card">
  <span class="badge [color]">[label]</span>
  <h3>[headline, optionally wrapped in a link to a source]</h3>
  <p>[2 to 4 sentences of body text]</p>
  <p class="small"><a href="#[section-id]">[Section name]</a></p>
</div>
```

Badge color choices:
- `green` for laws that are signed or newly in effect
- `orange` for federal proposals, watchlist items, or pending committee action
- `blue` for enforcement actions, agency actions, or executive branch actions

Section jump link targets: `#federal` for federal watchlist, `#live-now` for What is live now, `#state` for state watchlist.

**Content rules — no exceptions:**
- No em dashes (—) anywhere in the file. Use a colon or rewrite the sentence instead.
- No lists of exactly 3 items. Use 2 items or 4 or more if you need a list.
- No emojis anywhere.
- No self-referential language. Never write "this hub," "this page," "this section," "added here," "tracked above," or any phrase that refers to the site itself.
- No first-person language.
- Every factual claim must have a source URL — either inline as a link in the headline or body text, or as a linked source at the bottom of the card.

### Snapshot counts
Count the rows in the enacted-law table (inside the `<details>` element under "What is live now") and update the "Enacted statutes and official actions" count card to match.

### What is live now section
If any new laws have been signed or have taken effect, add:
- A new card to the card section (inside the `<div class="columns-2">` before the `<details>` element), using the same format as existing cards including a `<div class="kicker">` for the jurisdiction
- A new row at the end of the enacted-law table inside the `<details>` element

### Federal watchlist section
If any tracked bill has advanced — committee markup, committee vote, floor vote, or signed into law — update:
- The relevant card's Status line and Monitor line to reflect the new status
- The same bill's row in the full tracker table inside the `<details>` element

If any new bill warrants tracking, add a card to the card section and a row to the tracker table, following the format of existing entries.

---

## STEP 6: Commit and push

Stage `index.html`, the new archive file, and all modified archive files. Then commit with a message in this format:

```
Update hub to {{TODAY}}: [brief summary of top changes]

- [key change 1]
- [key change 2]
```

Then push to origin:
```
git push -u origin main
```

If the push fails due to a network error, wait 5 seconds and retry. Retry up to 3 times. If it fails after 3 attempts, output the error and stop.
