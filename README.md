# self-hosted-kanban-board

A self-hosted kanban board with swimlanes, packaged as a single HTML file. No install, no server, no build step. Open it in a browser and use it.

Data persists in IndexedDB on your machine. Nothing leaves the browser.

## Features

- **Swimlanes**: horizontal rows you define (e.g. Work, Personal, Side project). Each card lives in one swimlane.
- **Custom columns**: add, rename, reorder, delete. Defaults to Backlog / To Do / Doing / Done.
- **Drag and drop**: move cards between any swimlane and column combination.
- **Card detail modal**: editable title, markdown description with live preview, checklist with progress bar, swimlane/column dropdowns.
- **Checklists**: tick items off, see a progress bar and a card-level badge that turns green at 100%.
- **Markdown in descriptions**: bold, italic, inline code, code blocks, headings, links.
- **Export and import JSON**: full data snapshots for backup or moving between machines.
- **Local-first**: data lives in IndexedDB in your browser. No network calls, no accounts, no telemetry.

## Quick start

1. Download `kanban.html` from the [Releases](#) page, or clone this repo.
2. Put it in a permanent location on your machine (e.g. `~/Documents/kanban/kanban.html`).
3. Double-click to open in your browser.
4. Pin the tab or bookmark the file URL.

That's it. No npm, no Docker, no dependencies.

## When this is the right tool

It's free. It doesn't need IT approval. It isn't bloated. It just does the job.
The single-file, no-server, no-account design isn't a limitation pretending to be a feature, it's the whole point. Some situations it actually fits:

- **Sensitive personal data.** Health tracking, financial planning, anything you don't want sitting on a SaaS vendor's servers. A tool with zero network calls can't leak your data because there's nowhere for it to leak to. Open it offline and confirm for yourself.  
- **Locked-down work laptops.** No admin rights, no installing software, no signing up for cloud services on a corporate device. A single HTML file usually slips through where everything else gets blocked. No procurement form, no security review, no waiting six weeks for someone to approve a Trello account.  
- **Regulated or air-gapped environments.** Researchers in regulated industries, defence contexts, anywhere data is not allowed to leave the machine. One file, opened locally, no telemetry, no fonts loaded from a CDN, no analytics.  
- **Kids.** Drop the file on their laptop and they have a working kanban with no account, no data collection, no ads, and no way to accidentally publish anything to the internet. Useful for homework planning, chore tracking, or letting them learn what a kanban is without exposing them to a SaaS sign-up flow.  
- **Disposable boards**. Spin up a fresh board for a single project, work through it, export the JSON when done, archive the file. Jira would never be worth that setup cost. This one is.  

## When it's the wrong tool

Being honest saves everyone time:
- **Multi-user or real-time collaboration.** This is single-user by design. If you need shared boards, look at Wekan or Planka.  
- **Mobile-first use. It works on a phone.** It isn't pleasant on a phone. Built for laptops and desktops.  
- **Notifications, reminders, or scheduling.** No background processes, no email, no push.  
- **Integration with calendar, email, or other tools.** Nothing leaves the browser. That's a feature for some people and a dealbreaker for others.  
- **Data you can't afford to lose if you forget to back it up.** Browser storage can be wiped. Export JSON regularly or use a different tool.  

## How data storage works

This is the most important section. Read it before you commit real data to this tool.

### Where your data lives

Your data is stored in IndexedDB, a database built into your browser. The browser scopes that database to the exact URL the HTML file was loaded from. Concretely:

- If you open `file:///C:/Users/you/kanban/kanban.html`, the database is tied to that exact path.
- Open the same file at a different path? The browser treats it as a different site. Empty board.
- Open in a different browser (Chrome vs Firefox)? Different database. Empty board.
- Open in a private/incognito window? Often blocked entirely, or wiped when you close the window.

**Once you start using it, do not move the HTML file.** If you have to move it, use Export JSON first.

### What can wipe your data

- Clearing browser site data or cookies (depending on settings).
- Reinstalling the browser.
- Browser storage pressure (very rare for a small kanban, but possible).
- Aggressive privacy extensions like some ad blockers or anti-tracking tools.
- Some "system cleaner" utilities.

The app requests **persistent storage** on first load, which tells the browser "don't auto-evict this". Most browsers grant it automatically for local files. It is not bulletproof.

### Backup strategy

Use **Export JSON** regularly. It downloads a complete snapshot of swimlanes, columns, cards, and checklist items as a single JSON file. Stash it somewhere durable: cloud storage, a Git repo, a NAS, wherever.

**Import JSON** does the reverse, and replaces all current data. Use this to:

- Restore from a backup.
- Move to a new machine.
- Switch to a different browser.

A recurring "Export kanban" card in the board itself is a reasonable cadence forcing function.

### Why a file and not localStorage

localStorage is capped at 5-10MB depending on browser and is synchronous. IndexedDB is structured, asynchronous, and gives you far more room. The data model here uses object stores for swimlanes, columns, cards, and checklist items, with proper indices on lookups by card and by swimlane/column.

## Usage

### Adding things

- **Card**: click "+ Add card" in any cell.
- **Swimlane**: "+ Swimlane" button in the toolbar. Pick a name and an accent colour.
- **Column**: "+ Column" button in the toolbar.

### Editing

- **Card**: click the card to open the detail modal. Title, description, swimlane/column, and checklist are all editable. Changes save automatically on blur (when you click out of the field).
- **Column or swimlane name**: hover the header/label, click the pencil icon.
- **Swimlane colour**: hover the swimlane label, click the pencil icon, change the colour in the prompt.

### Moving cards

- **Drag and drop**: works between any cells.
- **Swimlane/column dropdowns inside the card modal**: useful on mobile or if you'd rather not drag.

### Deleting

Hover any column header, swimlane label, or open card. Click the cross or "Delete card" button. You'll be asked to type `delete` to confirm. This is intentional friction because deletes cascade (a deleted column takes its cards with it).

### Markdown in descriptions

Supports:

```
**bold**
*italic*
`inline code`
```
code block
```
## heading
[link text](https://example.com)
```

Lists and tables are not supported. If you need a structured checklist, use the proper checklist feature instead.

## Browser support

Tested in:

- Chrome / Edge (current versions)
- Firefox (current)
- Safari (current)

Requires a browser that supports IndexedDB and ES2017+ JavaScript. Anything from the last five years should be fine.

Will not work in:

- Private/incognito windows where IndexedDB is restricted
- Browsers with IndexedDB disabled by extensions or policy
- Internet Explorer (it's 2026, please stop)

## Hosting it somewhere instead

The file works equally well served over HTTP. If you want to access it from multiple devices on your own network:

```bash
# From the directory containing kanban.html
python3 -m http.server 8000
# Or
npx serve .
```

Then open `http://<your-machine-ip>:8000/kanban.html` from any device on the network. Note: data is still per-browser, so each device has its own separate board. There is no sync. If you need multi-device sync, this is the wrong tool.

You could also drop the file on a static host (GitHub Pages, Netlify, S3) and bookmark the URL. Same caveat applies: each browser keeps its own copy.

## Architecture

Single HTML file. No build step. No dependencies. The whole thing is:

- One `<style>` block: roughly Trello-inspired UI.
- One `<script>` block containing:
  - A small IndexedDB wrapper (`getAll`, `get`, `put`, `add`, `del`, etc.).
  - A minimal markdown renderer (no library).
  - State management as a single in-memory `state` object that mirrors the database.
  - Rendering done by clearing the board container and rebuilding it on every change. The data set is small enough that this is fine.
  - Event handling via delegation on `document`.

### Data model

```
swimlanes  { id, name, colour, position }
columns    { id, name, position }
cards      { id, swimlane_id, column_id, title, description, position, created_at }
checklist  { id, card_id, text, done, position }
```

`position` is a simple integer ordering within a parent. Drag and drop recomputes positions for the affected cell.

## FAQ

**Q: Can I share a board between two people?**
No. This is single-user, single-browser by design. If you want multi-user, you want a real server.

**Q: Can I sync across my own devices?**
Not natively. Workarounds: (1) host the file on a server you control and access from each device (still no sync; each device has its own data), (2) export from one device and import on another when you want to migrate. For real sync you need a server, which contradicts the point of this tool.

**Q: How do I keep the data if I want to switch browsers?**
Export JSON in the old browser. Open `kanban.html` in the new browser. Import the JSON file. Done.

**Q: My data disappeared. Help.**
First check that you're opening the file from exactly the same path as before. If you moved it, move it back. Then check Downloads or wherever you save exports for the most recent JSON backup. If neither helps, the data is likely gone. This is why exports matter.

**Q: Is anything sent over the network?**
No. The file has no external dependencies, no analytics, no fonts loaded from CDN, no calls home. Open it offline if you want to verify, everything works.

**Q: Can I customise it?**
Yes. It's one HTML file. Edit the CSS or JS directly. Common changes:
- Toolbar colours: edit the CSS variables at the top of the `<style>` block.
- Default columns/swimlane: edit the `ensureDefaults` function.
- Markdown features: edit `renderMarkdown`, or drop in a library like [marked](https://marked.js.org/) for full support.

**Q: Why not React/Vue/Svelte?**
A build step contradicts the "one file, open it" goal. Vanilla JS is fine at this scale.

**Q: Why IndexedDB and not localStorage?**
localStorage is synchronous and capped at 5-10MB. IndexedDB is structured, async, indexed, and gives you proper queries. Also: localStorage stores strings, so you'd end up serialising everything to JSON on every write anyway.

## Roadmap

Things that aren't here and might be added later (or might not, I'm not promising):

- Card labels/tags
- Due dates and reminders
- Search and filter
- Archive (hide completed cards without deleting)
- Card attachments (would need to base64-encode into IndexedDB, with size limits)
- Multiple boards
- Keyboard shortcuts beyond Escape

If you want any of these, the codebase is small enough to add them yourself. PRs welcome if this is on GitHub.

## Licence

MIT. Do what you want.  
This project was built collaboratively with Claude (Anthropic). Architecture and feature decisions were mine; most of the code was generated by Claude under my direction.

## Contributing

If this is on GitHub and you want to contribute:

1. Fork the repo.
2. Edit `kanban.html` directly. No build step to worry about.
3. Test by opening the file in a browser.
4. Open a PR with a clear description of what changed and why.

Keep the "single file, no dependencies" constraint. If a feature needs a library, it needs a strong justification.

## Acknowledgements

UI patterns borrowed liberally from Trello and Jira. The world doesn't need another kanban tool, but it does need ones that don't phone home.
