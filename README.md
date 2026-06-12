# OneFile Kanban

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

It's free. No account, no password, no sign-up. It doesn't need IT approval. It isn't bloated. It just does the job.
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

---

## How it works

The principle: One HTML file. Open it in a browser. Everything runs locally.

### The whole stack

There isn't one. No build step, no bundler, no framework, no package manager, no server. The file is plain HTML, CSS, and JavaScript, written by hand and readable by anyone who opens it in a text editor.

Concretely, `kanban.html` contains:

- A `<style>` block with the UI styling.
- A `<body>` with the static markup for the toolbar, the board container, and two hidden modal dialogs.
- A `<script>` block containing all the logic: database access, rendering, drag and drop, markdown, event handling.

That's it. No external dependencies. Nothing loaded from a CDN. No fonts fetched from Google. Open the file with your network unplugged and it works identically.

### Storage

Data lives in **IndexedDB**, a database built into every modern browser. It's structured (not just key-value strings), asynchronous, and gives you proper indexing on lookups.

Four object stores:

```
swimlanes  { id, name, colour, position }
columns    { id, name, position }
cards      { id, swimlane_id, column_id, title, description, position, created_at }
checklist  { id, card_id, text, done, position }
```

Each store auto-increments its `id`. `position` is an integer ordering within a parent (its swimlane and column for cards, its card for checklist items). When you drag a card to a new position, the app recomputes positions for the affected cells inside a single transaction.

Two indices help with common queries:

- `cards.by_swimlane_column` for fetching all cards in a given cell.
- `checklist.by_card` for fetching all items belonging to a card.

The database is scoped by the browser to the exact URL the file was loaded from. Open the file from a different path and you'll see an empty database. Open the same file in a different browser and you'll see an empty database. This is a browser security boundary, not a quirk of the app.

On first load, the app calls `navigator.storage.persist()`, which asks the browser not to evict the data under storage pressure. Most browsers grant this automatically for local files. It's not bulletproof: aggressive privacy tooling, manual cookie clearing, or browser reinstalls can still wipe it. That's why Export JSON exists.

### Rendering

Vanilla DOM manipulation. No virtual DOM, no reactivity framework, no templating engine.

The pattern is brute-force but appropriate at this scale: there's a single in-memory `state` object that mirrors the database. After any mutation, the app re-reads from IndexedDB, repopulates `state`, then clears the board container and rebuilds it from scratch. For a personal kanban with tens or hundreds of cards, this is fast enough that you can't perceive it. If the dataset ever grew to thousands of cards the approach would need to change, but it isn't going to.

The board layout uses a CSS grid with one row per swimlane and one column per kanban column. Adding a column or swimlane changes the grid template; the rest follows automatically.

### Drag and drop

Native HTML5 drag and drop. Each card sets `draggable="true"` and emits `dragstart` with its ID. Each cell listens for `dragover` (to allow the drop) and `drop` (to perform the move).

The drop handler does two things worth flagging:

1. **Computes target position from the mouse Y coordinate.** It walks the cards already in the target cell, finds the first one whose vertical midpoint is below the cursor, and uses that index as the drop position. Drop into an empty cell or below all cards and you get appended.

2. **Reorders siblings transactionally.** Moving a card out of its old cell shifts everything below it up by one position. Moving it into the new cell shifts everything from the target position downward by one. Both happen as part of the same logical operation, so the database never sees a state with duplicate or missing positions.

No drag-and-drop library. The HTML5 API is enough for a single board.

### Markdown

A minimal hand-rolled renderer (about 30 lines), not a library. It handles:

- Fenced code blocks (```` ``` ````)
- Inline code (`` ` ``)
- Bold (`**`) and italic (`*`)
- Links (`[text](url)`)
- Headings (`##`)
- Paragraphs and line breaks

It deliberately doesn't handle lists, tables, or anything more elaborate, because the checklist feature covers structured lists and the descriptions are meant to be short. If you want full markdown, swap the function for a library like `marked`. The renderer escapes HTML first and only injects tags it has generated itself, so you can't inject HTML through a description.

### Persistence cycle

Every user action follows the same shape:

1. The handler updates IndexedDB.
2. It then calls `loadBoard()`, which re-reads everything from the database into the in-memory `state`.
3. The board renders from `state`.

Saving happens on **blur** for text inputs (title, description, checklist item edits) and on **change** for checkboxes and dropdowns. There's no save button anywhere. Click out of a field and the data is in the database before you've moved your mouse to the next thing.

### Export and import

Export reads all four stores, wraps them in an envelope object with a timestamp and version number, serialises to JSON, creates a Blob, and triggers a download via a temporary `<a>` element. The whole operation is a few lines.

Import is the reverse: read the file, parse the JSON, validate it has the expected top-level keys, ask for confirmation, clear all four stores, and write the imported records back. It's destructive (replaces all current data) rather than merging, because merging two kanban boards is a problem with no obvious right answer and I didn't want to invent one.

### What isn't there

Worth being explicit about absences:

- **No service worker.** The file works offline because it has no network dependencies, not because anything is cached. Loading and reloading just works.
- **No analytics, telemetry, or error reporting.** Nothing is sent anywhere. If you want to verify, open browser DevTools, go to the Network tab, and reload. You'll see one request (the HTML file itself) and nothing else.
- **No authentication.** It's single-user by design and there's nothing to authenticate against anyway.
- **No sync.** Each browser holds its own data. The only way to move data between browsers or machines is Export then Import.
- **No backend.** Not even a thin one. If a feature would require a server (push notifications, multi-device sync, sharing), it isn't in scope for this project.

### Why this approach

Two reasons.

First, **it makes the trust story simple.** A single readable HTML file with no network calls is verifiable in a way that any normal web app isn't. Anyone who can read JavaScript can audit the entire thing in 20 minutes.

Second, **it removes every form of friction between "I want a kanban" and "I have a kanban".** No install, no account, no terms of service, no compatibility matrix, no version pinning. Whatever made this work today will keep working in five years because nothing it depends on is going anywhere.
