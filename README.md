# CR Status Report

A lightweight, no-backend status report site for the Commercial Readiness team.
Five team members submit their weekly updates through a form; the site layers
everyone's submissions together into one report, grouped by project, in the
same format as the Word status report it replaces.

## How it works

- **`update.html`** — the submission form. Each person picks their name,
  gets prompted project-by-project ("check Outlook, Teams, status reports,
  and meeting notes"), and downloads a `<name>.json` file.
- **`data/*.json`** — one file per employee (`kelly.json`, `barry.json`,
  `tessa.json`, `laura.json`, `andrew.json`, `jan.json`). This is the only
  "database" — plain JSON committed to the repo.
- **`index.html`** — the combined report. On load, it fetches all six
  `data/*.json` files and layers each person's text into the matching
  project section, in the same visual layout as the original report
  (section bands, item headings, Out of Office and Sign Off tables).

Because everything is static files, this hosts on GitHub Pages with no
server, database, or build step.

## Weekly workflow

1. Each person opens `update.html`, selects their name, and fills in only
   the projects they touched this week (leave the rest blank).
2. They check **"I confirm the updates above are accurate and complete"**
   and click **Download my update file** — this saves `<name>.json`.
3. They replace the matching file in `/data` in this repo (via git, or by
   uploading directly on github.com — see below) and commit/push.
4. GitHub Pages rebuilds automatically (usually under a minute). Anyone can
   then open `index.html` to see the combined report with everyone's
   updates layered in.
5. Next week, click **Load my last saved update** on `update.html` first —
   it fetches your existing `data/<name>.json` and pre-fills the form so
   you're only editing, not retyping.

### Uploading a file on github.com without git

1. Go to the repo → open the `data` folder.
2. Click your file (e.g. `barry.json`) → the pencil (edit) icon.
3. Delete the contents and paste in the new file's contents, or use
   **Add file → Upload files** and drag the downloaded file in (choose
   "replace" if prompted).
4. Commit directly to the main branch.

## Publishing on GitHub Pages

1. Push this folder to a GitHub repo (e.g. `CRStatusReport`).
2. In the repo, go to **Settings → Pages**.
3. Under **Build and deployment**, set **Source** to "Deploy from a branch",
   branch `main`, folder `/ (root)`.
4. Save. GitHub will publish at
   `https://<your-username>.github.io/CRStatusReport/`.
5. Share `https://<your-username>.github.io/CRStatusReport/update.html`
   with the team for submissions, and the root URL for the combined report.

## Editing the project list

All project names/groups live in one place: `assets/projects.js`. Add,
rename, or reorder items there and both pages stay in sync automatically —
no need to touch `index.html` or `update.html`.

## Notes

- Opening the HTML files directly from disk (`file://`) will fail to load
  `data/*.json` in some browsers due to local CORS restrictions. Test either
  on the published GitHub Pages URL or via a local static server, e.g.
  `python3 -m http.server` from this folder, then visit
  `http://localhost:8000`.
- This is intentionally backend-free. If the team outgrows plain JSON files
  (e.g. needs edit history, notifications, or access control), the next step
  would be a small form backend (Google Forms + Sheets, Airtable, or a tiny
  serverless function) feeding the same `data/*.json` shape.
