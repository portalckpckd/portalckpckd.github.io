# QC Docs · DC Cikupa

Photo & video documentation for **QC Outbound** and **Unboxing Retur**, saved straight to a Google Shared Drive.

- **UI** — `qcdocs/index.html` in `portalckpckd.github.io` → https://portalckpckd.github.io/qcdocs/
- **Backend** — Google Apps Script (Drive folders, log sheet, upload sessions). Its code is kept in Apps Script, not in this public repo.

Operators don't need a Google account: they enter the DC PIN once and pick their name.

## Naming

```
Shared Drive / QC | RETUR / yyyy-MM-dd / INV0000000 - 2026-11-20 - 10486930 OBELIA BASE CABINET
                                         └─ …_P1.jpg, …_P2.jpg, …_V1.mp4
```

## Spreadsheet tabs

| Tab | Content |
|---|---|
| `Master_SKU` | A = SKU, B = description (row 1 = header). Format column A as plain text. |
| `Operator` | A = operator names (row 1 = header). Created by `setup()`. |
| `Log` | One row per case. Created by `setup()`. |

## Setup

### 1. Apps Script (backend)
1. Open the spreadsheet → **Extensions → Apps Script** (or a standalone project).
2. Project Settings → tick **Show "appsscript.json"**.
3. Paste `apps-script/Code.gs` and `apps-script/appsscript.json`.
4. **Project Settings → Script properties → Add property**: `PIN` = the DC PIN (6+ characters).
   The PIN lives only here, never in GitHub. `ALLOWED_ORIGINS` in `CFG` is already `https://portalckpckd.github.io`.
5. Run `setup()` once and approve the permissions.
6. **Deploy → New deployment → Web app**
   - Execute as: **Me**
   - Who has access: **Anyone**
7. Copy the `/exec` URL.

> Every change to `Code.gs` needs **Deploy → Manage deployments → ✏️ → New version** to go live.

### 2. GitHub Pages (UI)
1. In `qcdocs/index.html`, set `API_URL` to the `/exec` URL.
2. Commit the `qcdocs/` folder to `portalckpckd.github.io` (Pages is already on).
3. Open https://portalckpckd.github.io/qcdocs/ on a phone in Chrome → **Add to Home screen**.

## How uploads work
Apps Script opens a resumable upload session in the case folder; the phone sends the file in 5 MB pieces
directly to Google Drive. If the browser can't reach Drive directly, it switches automatically to sending
the pieces through Apps Script (slower, same result). The Upload card shows which route is in use.

## Security notes
- The PIN is checked on every request; after 30 wrong PINs the API pauses for 10 minutes.
- Folder IDs handed to the phone are signed, so uploads can only go into case folders the script created.
- To change the PIN, edit the `PIN` script property — takes effect immediately, no redeploy. Phones will ask for the new one.
