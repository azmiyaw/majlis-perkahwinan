# Wedding invitation: Azmi & Azira

Files:

| File | Purpose |
|------|---------|
| `index.html` | The invitation (gate, invitation, RSVP and wishes, contact, footer) |
| `config.js` | Holds the backend URL and limits |
| `admin.html` | Password-protected RSVP list for the couple |
| `apps-script/Code.gs` | Backend that stores RSVPs in a Google Sheet |
| `music.mp3` | Optional. Put your own file here and the music button appears |

Without a backend URL the page runs in demo mode. Wishes are saved in that browser only, which is fine for trying it out and useless for real guests.

## 1. Set up the Google Sheet backend (about 10 minutes)

1. Create a new Google Sheet, for example "Wedding RSVP".
2. Open **Extensions > Apps Script**.
3. Delete the sample code and paste in the whole of `apps-script/Code.gs`.
4. Change `ADMIN_KEY` near the top to a long secret only you and your partner know.
5. Click **Deploy > New deployment**, choose the type **Web app**, and set:
   - Execute as: **Me**
   - Who has access: **Anyone**
6. Click **Deploy** and approve the permission prompts. If Google warns the app is unverified, choose **Advanced**, then **Go to project**. This is your own script.
7. Copy the **Web app URL**. It ends in `/exec`.
8. Open `config.js` and paste it:
   ```js
   API_URL: "https://script.google.com/macros/s/XXXXXXXX/exec",
   ```

When you change `Code.gs` later, use **Deploy > Manage deployments > Edit > New version**. The URL stays the same.

## 2. Check it works

1. Open `index.html` through any web server, or after deploying (step 3).
2. Tap **Buka Jemputan**, submit an RSVP with a wish, and confirm a new row appears on the `RSVP` tab of your sheet.
3. Open the page on a second device. The wish should show up there within about 20 seconds, with no reload.

## 3. Deploy the site

Pick one. All three are free.

**Netlify (quickest):** go to app.netlify.com/drop and drag the `wedding-invitation` folder onto the page. You get a link straight away. You can rename the site in Site settings.

**GitHub Pages:**
1. Create a repository and upload these files to it.
2. In **Settings > Pages**, set the source to the main branch and the root folder.
3. The site appears at `https://<username>.github.io/<repo>/`.

**Vercel:** import the repository or run `vercel` in the folder. No build settings are needed. Choose "Other" as the framework.

## 4. See the full RSVP list

- **Spreadsheet:** open the Google Sheet. Columns are Time, Name, Headcount, Wish. Use `=SUM(C2:C)` for the total number of guests.
- **Admin page:** open `admin.html` on your site, enter `ADMIN_KEY`, and press **Papar**. It shows each RSVP and the total headcount. The key is checked on the server, not in the page.

Do not share the admin address or the key with guests. The page is marked `noindex`, but anyone with the key can read every entry.

## What is protected, and what is not

- Guest text is added to the page with `textContent`, so it cannot run as HTML or script.
- The server removes HTML tags and control characters, limits name (80), wish (300) and headcount (1 to 10), and stores name and wish as plain text so a value such as `=IMPORTXML(...)` cannot run as a spreadsheet formula.
- A hidden honeypot field turns away simple bots.
- Anyone who has the page can still submit entries, because the form has no login. If someone spams it, delete the rows in the Sheet. For a bigger audience, add a rate limit or a Cloudflare Turnstile check.
- The public wishes list shows name, headcount, wish and time. It never returns anything else from the Sheet.

## Changing details

Names, date, venue and phone numbers are plain text in `index.html`. The countdown target is the `target` date in the script near the bottom (`2026-11-28T09:00:00+08:00`).
