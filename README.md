# Weekly Status Update Assistant

A team-wide index page that turns a Claude project into a weekly status update generator, plus a second page that's the final version of the report. Styled to Elsevier's brand standards (Tiempos Text / National 2, brand colors, logo).

## Objective

Create a simple, repeatable weekly status-report process using Claude and a GitHub-hosted form.

Each team member opens the index page, copies a Claude prompt, and runs it in their own Claude project named "Status Update." Claude reviews that individual's Outlook emails, Teams chats, and historical status-report files to identify updates for each tracked status-report item.

Claude's output is concise, easy to read, and written in third person using the individual's name (for example, "Jane Smith completed…"). The output is structured so it can be pasted into a form with one editable field per status-report item. After pasting, the user can revise any individual update before generating the organized status report.

## What's in this repo

- `index.html` — the page everyone on the team uses. Always shows one field per tracked item, grouped by initiative; paste Claude's output and click "Update Status Report" to fill them in. "Save Changes" gives an explicit confirmation (fields also autosave as you type), and "View Report" shows a read-only, formatted preview right there before you move on.
- `report-form.html` — the final version of the report on the same device. Project updates auto-fill from `index.html`; out-of-office, recognition, and sign-off are entered directly here.
- `prompt.md` — the prompt text that goes into each person's Claude project.
- `elsevier-logo-graphite.svg`, `fonts/` — brand assets used by both HTML pages.

## Tracked items

The tracked-item list is fixed in two places, which must be kept in sync if it ever changes:

- `prompt.md` — inside the actual prompt text, so Claude reports on exactly these items.
- `index.html` — in the `STRUCTURE` constant near the top of the `<script>` block, so the page always shows a field for each one.

Current list (4 initiatives, 17 items, all status "Active"):

**Physician and Pharmacist Initiatives** — ClinicalKey Unified Vision (CK+?), ClinicalKey for Nursing Unified Vision (CKN+?), ClinicalKey (CK) Physician BAU, CK AI BAU, CPCK BAU, CK for Medical Schools BAU, CK Test Prep BAU, NEOID LMS Access CK Student

**Nursing and Patient Education Initiatives** — Clinical Learning Hub, CK Student NOAM/Clinical Cases BAU, CK Nursing BAU, Patient Pass/PE Products BAU, Clinical eLearning BAU

**Specialist Solution Initiatives** — ClinicalPath BAU, ClinicalPath Provider Reports, PSS BAU

**Strategic/Corporate Initiatives** — eLearning Localization

If Claude's pasted output includes a heading that doesn't match one of these exactly, `index.html` doesn't drop it — it shows up in an "Unmatched from pasted output" section so you can catch typos or fold it into the right field manually. If Claude finds nothing to report for an item, the prompt now tells it to leave that item's paragraph blank rather than writing a "no updates" placeholder sentence — `index.html` shows its own "No updates yet" placeholder for any blank field, so it's visually obvious which items are still open.

## One-time setup (each team member)

1. In Claude, create a new **Project** named **Status Update**.
2. Open `index.html`, click **Copy Prompt**, and paste it into the project's instructions. Replace `[YOUR NAME]` with your name.
3. Upload your past status reports to the project as knowledge files. Claude uses these to match your tone and formatting (the item list itself is already fixed in the prompt).

## Weekly workflow

1. In your **Status Update** project, ask: *"Run my status update for the week."*
2. Copy Claude's full response.
3. On `index.html`, paste it into the text box and click **Update Status Report**.
4. Each tracked item's field fills in — make any tweaks directly in the form. Click **Save Changes** for a confirmation, or **View Report** to see a formatted read-only preview before moving on.
5. Click **Open Report Form** to jump to `report-form.html` — the final version. Project updates are already there; add your name, the week-of date, upcoming out-of-office, recognition, and sign-off, then click **Copy Full Report**.

## How the parsing works

Claude is instructed to output each item as:

```
## Item Name
Update paragraph (or nothing, if there's no update)
```

`index.html` splits the pasted text on `## ` headings and matches each one to a tracked item by name (ignoring case/punctuation differences). Anything that doesn't match a known item is kept and shown separately rather than dropped. A heading with no body text underneath it just means that item has nothing to report this week.

## How the autofill between the two pages works

`index.html` saves your current items to the browser's `localStorage` (`statusUpdateItems`) every time you edit a field. `report-form.html` reads that same storage on load and pre-fills its project fields from it, grouped by the same initiative categories. A **Refresh from Status Update page** button on the report form re-reads storage in case you go back and make more edits.

`report-form.html`'s own additions — name, week-of, out-of-office, recognition, sign-off — are saved separately under `statusReportMeta`, so they persist across refreshes without being tied to whatever's currently in `index.html`.

This only works **within the same browser, on the same device** — `localStorage` is local to your machine, so it doesn't sync between teammates or across computers. If you need to hand a filled-in report to someone else, use **Copy Full Report** and send the text (or paste it into wherever your team's real report lives).

## Maintaining this over time

- Keep feeding each week's finished report back into the Claude project as a knowledge file so tone and format stay current.
- **If the team's tracked items change** (added, removed, or renamed), update the list in both `prompt.md` and the `STRUCTURE` constant in `index.html` — this no longer happens automatically, since the list is now fixed in both places rather than inferred from historical files each time.
- **If the team roster changes** (out-of-office / sign-off list), update the `TEAM` constant in `report-form.html`.
