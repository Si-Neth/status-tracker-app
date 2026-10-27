# IT Follow-up Tracker

A single-file web page (`index.html`) for tracking open IT follow-up items, seeing what is overdue, and chasing owners by **Outlook on the web** or **Microsoft Teams**.

No build step, no server, no installation. It runs entirely in the browser and can be hosted on GitHub Pages.

---

## Features

- **Overview tab:** all items with business unit, owner, type, status, target date and days overdue. Filter, search and add or edit items.
- **By owner tab:** open items grouped per owner, with a ready-made reminder for each person.
- **Quick add:** add an item without opening the full form.
- **Import Excel / Export Excel:** load items from a spreadsheet or download the current list (uses SheetJS). Import asks for confirmation and offers to export the current list first.
- **Reminders to owners:**
  - **✉ Email in Outlook** opens a pre-filled draft in Outlook on the web.
  - **💬 Remind in Teams** opens a pre-filled chat in Teams (app or browser).
  - **Copy table**, **Copy image**, **Save image** and **Copy email text** are copy-and-paste fallbacks.
  - **include image** adds a picture of the full list to the message.
- **Microsoft 365 connection (optional):** with it, Outlook gets a real draft with the picture already inside, and Teams shows a preview with a **Send to Teams now** button.

> Outlook desktop (`mailto:`) is intentionally not included in this version. Use Outlook on the web and Teams.

---

## Quick start (use it locally)

1. Download `index.html`.
2. Open it in Edge or Chrome.

Reminder links work this way. The Microsoft 365 connection does **not**, because Microsoft sign-in needs an `https://` address (see below).

---

## Host on GitHub Pages

1. Create a GitHub repository, for example `it-followup-tracker`.
2. Upload the tracker file and **rename it to `index.html`**.
3. Add this `README.md` to the same repository.
4. Go to **Settings → Pages**. Under **Build and deployment**, set **Source** to *Deploy from a branch*, choose branch `main` and folder `/ (root)`, then **Save**.
5. After a minute or two the site is live at:

   ```
   https://<your-username>.github.io/<repository-name>/
   ```

   Keep this address. You need it for the app registration.

### Before you publish: check what is public

GitHub Pages sites are **public** on free personal accounts. Anyone with the link can view the page source.

The file contains an embedded snapshot of your item list (descriptions, owners, dates, remarks). If that is internal data:

- use a **private repository** with an account or organisation plan that supports private Pages, or
- host it on an internal address (company web server or Azure Static Web Apps with sign-in), or
- clear the snapshot data before publishing.

Do not commit client secrets or passwords. This page needs none: the Microsoft 365 connection uses only a public client ID and tenant ID.

---

## Data storage

| Where it runs | What you see | Where data is kept |
|---|---|---|
| On GitHub Pages or a downloaded file | **Offline copy** badge, starting from the embedded snapshot | In **your own browser** (`localStorage`). Not shared between people or devices. |

Things to know:

- Each person who opens the page has their own copy of the data, so changes are not shared.
- Clearing browser data removes your edits. Use **Export Excel** regularly as a backup.
- Owner email addresses you type are saved the same way, in your browser.
- To refresh the list, use **Import Excel** with a spreadsheet.

---

## Set up the Microsoft 365 connection (optional)

This lets the tracker create Outlook drafts and send Teams messages **as you**, with the picture included. It needs the hosted `https://` address above.

### 1. Register the app

1. Go to **[entra.microsoft.com](https://entra.microsoft.com) → App registrations → New registration**.
2. **Name:** `IT Follow-up Tracker`. **Supported account types:** *Accounts in this organizational directory only*.
3. Under **Redirect URI**, choose platform **Single-page application (SPA)** and enter your GitHub Pages address, for example:

   ```
   https://<your-username>.github.io/<repository-name>/
   ```

   It must match the address in your browser exactly, including the trailing slash.
4. Click **Register**.

### 2. Add permissions

Open **API permissions → Add a permission → Microsoft Graph → Delegated permissions** and add:

| Permission | Used for |
|---|---|
| `User.Read` | Sign-in and identifying you |
| `Mail.ReadWrite` | Creating the Outlook draft |
| `Chat.Create` | Starting a Teams chat with the owner |
| `Chat.ReadWrite` | Reading and writing that chat |
| `ChatMessage.Send` | Posting the reminder |

Click **Grant admin consent** only if your tenant requires it.

### 3. Connect the tracker

1. From the app's **Overview** page, copy the **Application (client) ID** and **Directory (tenant) ID**.
2. Open the tracker, go to **By owner → 🔗 Set up Microsoft 365**.
3. Paste both IDs and click **Save and sign in**.

If your tenant blocks users from registering apps or consenting to permissions, ask your Microsoft 365 admin to do steps 1 and 2.

---

## Sending a reminder

1. Open the **By owner** tab and find the owner.
2. Type their **work email** in the box and press **Enter**. It is saved in your browser, so you only do this once per owner. Several emails separated by `;` start a Teams group chat.
3. Click **✉ Email in Outlook** or **💬 Remind in Teams**. The message opens with the reminder and the open items already typed in.
4. If Teams opens with an empty box, click in it and press **Ctrl+V**. The text is already copied.
5. Check the message and press **Send**.

**With the picture**

- *Without* the Microsoft 365 connection: tick **include image**. The picture is copied, the message opens, and you press **Ctrl+V** (⌘V on Mac) to paste it. Links cannot carry pictures, so this is a limit of Outlook and Teams, not of the tracker.
- *With* the connection: Outlook creates a draft with the picture inside (also saved in your Drafts folder). Teams shows a preview, and **Send to Teams now** posts it as you.

**Other notes**

- Text-only reminders shorten very long lists to "…and N more". The picture and **Copy email text** always show everything.
- If your browser blocks the pop-up, the button changes to **Open Teams ›** or **Open Outlook ›**. Click it a second time.
- If copying is blocked, a preview window opens. Right-click the picture, choose **Copy image**, then use the **Open** button.

---

## Troubleshooting

| Problem | Fix |
|---|---|
| Sign-in fails with a redirect URI error | The redirect URI in Entra must match the page address exactly, and be of type **Single-page application**. |
| Sign-in does not work from a file on my computer | Expected. Microsoft sign-in needs `https://`. Use the GitHub Pages address. |
| "Need admin approval" at sign-in | Ask your Microsoft 365 admin to grant consent for the five permissions. |
| Teams opens with an empty message | Click in the box and press **Ctrl+V**. |
| Teams says the recipient was not found | Use the person's work sign-in email, not an alias. |
| My edits disappeared | Data is stored per browser. Clearing site data or switching browser or device loses it. Export Excel as a backup. |
| Import Excel or Export Excel does nothing | The page loads SheetJS from `cdnjs.cloudflare.com`. Check that your network allows it. |
| Page updates do not show after pushing to GitHub | Wait a minute for Pages to redeploy, then hard-refresh with **Ctrl+F5**. |

---

## Files

| File | Purpose |
|---|---|
| `index.html` | The whole tracker (HTML, CSS and JavaScript in one file) |
| `README.md` | This guide |

## External libraries (loaded from CDNs)

- [SheetJS](https://sheetjs.com/) 0.18.5, for Excel import and export
- [MSAL Browser](https://github.com/AzureAD/microsoft-authentication-library-for-js) 2.39.0, for Microsoft sign-in
- Google Fonts, for typography

Reminders go through `outlook.office.com`, `teams.microsoft.com` and, when connected, `graph.microsoft.com`.
