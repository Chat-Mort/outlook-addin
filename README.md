# Swift Compose

An Outlook add-in I built because I was sick of retyping the same emails all day (pickup requests, delivery notes, you name it). It started as "just insert today's date in my templates" and, well... it got out of hand.

Right now it does templates with smart tags, a contact directory, a client address book, a barcode/QR generator and a palette of hard-to-type characters, all in one side panel. There's no backend, no server, nothing phoning home: it all runs in your browser and your data stays in your own mailbox / browser.

---

## What it does

### Templates
- Save templates with a name, subject, body, recipients (To / Cc / Bcc), a category, a shortcut and attachments.
- Click a template card and it fills in the draft you have open. If you're just looking at your inbox, it creates a fresh pre-filled email instead.
- Duplicate, edit, delete (there's a 6-second undo, I've misclicked enough), and a 👁 button to preview what the final email will look like before inserting anything.
- Categories (collapsible), search (pops up once you have more than 10 templates) and sorting: alphabetical, created, modified, recently used.
- Light body formatting: `**bold**`, `_italic_`, and lines starting with `- ` become bullets.
- Little helpers: subject length hint, warning if two templates share a shortcut, attachment size indicator.
- Export / import as JSON. Import always merges, it never overwrites what you already have.

### Tags
Tags get resolved every time you use a template. Dates come out as `DD/MM/YYYY`.

| Tag | What you get |
|---|---|
| `{{d}}`, `{{d+N}}`, `{{d-N}}` | Today, or N calendar days ahead / back |
| `{{bd+N}}`, `{{bd-N}}` | N **business days** ahead / back (weekends skipped) |
| `{{w}}`, `{{w+N}}`, `{{w-N}}` | ISO week number, N weeks ahead / back |
| `{{c}}` | Pops up a calendar to pick a date |
| `{{dn}}` | Asks for a delivery note number, inserted as `DNXXXXXXXX` |
| `{{client}}`, `{{client:Group}}` | Pick a client (optionally only from one group) and drop in their saved address |
| `{{q:Question}}` | Asks your custom question and inserts the answer. Every occurrence gets asked separately |
| `{{attach}}`, `{{attach:Label}}` | Reminds you to attach a file. The tag itself never shows up in the email |

Don't want to remember the syntax? There's a ⚡ button next to the subject and body fields that inserts any of them for you (with a live preview for the date offsets). It can also paste shortcuts and directory contacts.

### Shortcuts (`##name`)
- Give a template a shortcut name, then type `##name` in another template or straight into a draft to pull its content in.
- It's subject-aware: `##name` in a subject pulls the other template's subject, in a body it pulls the body.
- Shortcuts can be nested (up to 5 levels).
- If the panel opens on a draft that has `##shortcuts` in it, they get resolved on their own. There's also a **Resolve ## shortcuts** button for anything you type afterwards.
- It yells at you if a shortcut doesn't match anything (typos), and you can undo a resolution.

### Attachments
- Store attachments on a template and mark each one as **final** (attached automatically) or a **blank placeholder**.
- Blank ones give you a reminder with **Download blank** and **Import final**, so you can grab the empty version and attach the completed one.
- `{{attach}}` asks for an attachment without storing any file in the template.

### Directory
- Internal phone book: name, role, service, internal extension, personal phone, email.
- Grouped by service (collapsible), searchable, one-click copy of each number, and you can rename a whole service in one go.
- Export / import as JSON.

### Clients
- Address book for clients: name, optional group, multi-line address. This is what `{{client}}` pulls from.
- Grouped and collapsible, with export / import too.

### Codes
- Generates QR Code, Data Matrix, Code 128, EAN-13, UPC-A, PDF417 and Aztec codes.
- Batch mode (one value per line), PNG or ZIP download, print, and a recent history.
- `\t`, `\r` and `\n` typed as text get turned into the real characters, for those scanner "keyboard" formats.
- There's a Data Matrix login badge helper: type a login and a password and it builds the `\tLOGIN\tPASSWORD\t\t\r\n` line for you.
- It can also read an existing code from an image (drag & drop or file picker).

### Characters
- A searchable palette for everything that's a pain to type: superscripts and subscripts (², ³, ᵉ, ₂…), accented letters, fractions, French typographic spaces (NBSP, narrow NBSP), quotes and dashes, math, currency, arrows, symbols, Greek, and the awkward ASCII ones (`~ # { } [ ] | \ ^ @`).
- Search by name, keyword or code. French keywords work too (`exposant`, `flèche`, `cédille`), and you can paste a character to find it.
- Click a character to drop it at the cursor of the open draft, or flip the drop-down to **copy** mode (also what you get if no draft is open).
- Hover a character to see how to type it by hand: `Alt` + numeric keypad code, `hex code → Alt+X` (classic Outlook / Word engine only), the Unicode code point, plus Word's own shortcut when there is one.
- A **Recent** tab remembers what you used last, and a small converter turns text into superscript or subscript (`1er` → `1ᵉʳ`, select just part of the text to convert only that).

### The rest
- Dropdown to switch sections (Templates / Directory / Clients / Codes / Characters / Help) that remembers where you were, plus a global search (hit `/` to jump to it).
- Follows Outlook's light/dark theme, with a manual toggle, and a compact / comfortable density switch.
- A Help tab with a tag cheat sheet, a storage gauge, a backup reminder and a changelog.

---

## What you need

- An Exchange-based mailbox (Microsoft 365, Exchange Online, Outlook.com). Template text is stored in Outlook's roaming settings, so a plain IMAP/POP account won't cut it.
- I've really only tested it on **Outlook on the web**. It's a standard Office Add-in, so it *should* also work in new Outlook and classic Outlook for the same mailbox, but classic can take its time to pick up a freshly sideloaded add-in. Not available on the mobile apps.
- The right to sideload custom add-ins. Some companies lock that down. If you don't see the option, or the install gets refused, ask IT.
- Internet access: Office.js comes from Microsoft's CDN and the Codes tab needs three libraries from `cdn.jsdelivr.net`.

---

## Install it

Add-ins have to be served over HTTPS, so you can't just double-click `taskpane.html` from your disk. Two ways to go about it:

### Option A: just use my hosted copy (no fork)

Easiest way to try it. The `manifest.xml` in this repo already points at my GitHub Pages site.

1. Download **`manifest.xml`** from this repo (open it, hit **Raw**, save the file, or grab the whole repo as a ZIP).
2. Go to **<https://aka.ms/olksideload>**. It opens the add-ins dialog in Outlook on the web.
3. **My add-ins** → scroll down to **Custom Addins** → **Add a custom add-in** → **Add from file…**
4. Pick `manifest.xml` and accept the warning.
5. Open an email. A **Templates** button (group *Swift Compose*) shows up in the ribbon.

Heads up: this relies on my GitHub Pages staying online and not changing under you. Your data isn't shared with anyone though (see [where your data lives](#where-your-data-lives)). If you want to be independent, go with Option B.

### Option B: host your own copy (fork)

You control everything and you don't depend on me. Forking is the easy way, but any HTTPS host works (GitHub Pages, Azure Static Web Apps, whatever you have).

1. **Fork** this repo and **keep the name `outlook-addin`**. It keeps step 3 to a single find-and-replace.
2. **Turn on GitHub Pages**: *Settings → Pages → Deploy from a branch → `main` / `(root)`*. Wait for the deployment (green check in the **Actions** tab), then make sure this opens in your browser:
   `https://<your-username>.github.io/outlook-addin/taskpane.html`
3. **Edit `manifest.xml`** on your computer: find-and-replace every `chat-mort` with **your GitHub username** (they're all inside URLs). If you named your repo something else, also replace `outlook-addin` in those URLs.
4. Optional but smart: swap the `<Id>` for a fresh GUID (any generator works), so it doesn't clash if you ever install mine next to yours.
5. **Sideload** your edited `manifest.xml` like in Option A (steps 2 to 5).

Only `taskpane.html`, `commands.html` and the `icons/` folder need to be on your host. The manifest gets installed from your computer, it doesn't need to be served.

### Pin the panel

Open the panel and click the **pin icon** in its header. A pinned panel stays open as you move between emails, so it's already there when you start a new message and any `##shortcuts` in the draft get resolved right away. Trust me, you want this.

---

## Updating

1. Replace the changed files in your repo and wait for GitHub Pages to redeploy.
2. Hard-refresh the panel (**Ctrl + F5**).
3. Outlook still showing the old version? Bump the `?v=` number on the `taskpane.html` and `commands.html` URLs in `manifest.xml` (`?v=2` → `?v=3`), remove the add-in from **Custom Addins**, and sideload the manifest again.

To uninstall: **My add-ins → Custom Addins →** remove *Swift Compose*.

---

## Tag cheat sheet

The Help tab inside the add-in has the same thing:

```
{{d}}  {{d+3}}  {{d-1}}        today / +3 days / -1 day
{{bd+2}}  {{bd-3}}             +2 / -3 business days
{{w}}  {{w+1}}                 this week number / next week
{{c}}                          calendar picker
{{dn}}                         delivery note number → DNXXXXXXXX
{{client}}  {{client:Spain}}   pick a client (optionally within group "Spain")
{{q:PO number}}                ask "PO number", insert the answer
{{attach}}  {{attach:CMR}}     ask for an attachment, optionally labelled
##shortcut                     pull in another template's subject/body
**bold**  _italic_  - item     body formatting
```

---

## Where your data lives

No server, no tracking. Here's where everything ends up:

| What | Where | Synced between devices? |
|---|---|---|
| Template text, subjects, recipients, categories | Outlook roaming settings (your mailbox) | Yes, but capped at 32 KB (there's a gauge in the Help tab) |
| Template attachments | Browser `localStorage` | No |
| Directory, Clients | Browser `localStorage` | No |
| Code history, recent characters, theme, density, sort and collapsed-group preferences | Browser `localStorage` | No |

What that means in practice:

- Whatever is in `localStorage` is per browser and per device. Clear the site data and it's gone. **Use the Export buttons** to back stuff up. The Help tab nags you if it's been a week.
- After each successful sync I keep a local copy of your templates. If the synced version ever comes back smaller than it should, the add-in offers to restore from that local backup. (Added after I lost my own directory once. Lesson learned.)
- Even if several people use the same hosted copy, nobody sees anybody else's stuff: roaming settings live in each person's own mailbox and `localStorage` is private to each browser.
- ⚠️ If you use the Data Matrix login helper, the recent-codes history (and the generated images) can contain **login / password pairs in plain text** in your browser. Don't do that on a shared computer, and clear your browser data if you need to. This is not a password manager.

---

## Things that don't work (and why)

- **New email from the inbox opens in a separate window.** Outlook's API can't attach files to it or touch it, so all you get there is a download reminder. Attachments only get added automatically when a draft is already open.
- Roaming settings max out at **32 KB** for the template text.
- The button only shows when an email is selected or being composed. Add-ins need an open item to hang on to.
- You can't make the new-message window open inline instead of as a pop-out. That's Outlook's decision, not mine.
- The Codes tab needs internet to load its libraries.
- Nothing on Outlook mobile.

---

## When it breaks

**"Add-in installation failed" or manifest errors**
- Check there's no `chat-mort` left in your edited `manifest.xml` (if you forked).
- Open `https://<you>.github.io/outlook-addin/taskpane.html`, `…/commands.html` and `…/icons/icon-32.png` in a browser. All three have to load. GitHub Pages can take a few minutes after the first deployment.
- Your company might block custom add-ins. Ask IT.

**Still seeing the old version after an update**
- Ctrl + F5, then bump the `?v=` in the manifest and reinstall (see [Updating](#updating)).

**No button anywhere**
- Select an email or open a draft first. On classic Outlook, a freshly sideloaded add-in can take a while to show up.

**Templates vanished or "Sync error"**
- Go to **Help → Storage**, look at the gauge and read the status message. If a restore banner shows up, hit **Restore from local backup**. And export regularly, seriously.

**Codes tab says a library failed to load**
- Check your connection, make sure `cdn.jsdelivr.net` isn't blocked by a proxy or firewall, then reload the panel.

---

## What's in the repo

```
.
├── manifest.xml     # Add-in definition. Edit the URLs here if you host your own copy
├── taskpane.html    # The whole UI and logic, in a single file
├── commands.html    # Tiny page the manifest needs
└── icons/           # icon-16.png, icon-32.png, icon-80.png
```

At runtime it loads `office.js` (Microsoft), plus `bwip-js` (code generation), `JSZip` (ZIP export) and `@zxing/library` (code reading) from jsDelivr.
