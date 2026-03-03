# Avanse Funding Dashboard

A lightweight static dashboard that reads funding data from Google Sheets via a Google Apps Script Web App URL.

## How to execute (run) this dashboard

Because this project is plain HTML/CSS/JS, you don't need a build step.

### 1) Run locally

From the project folder:

```bash
cd /workspace/avanse-dashboard
python -m http.server 4173
```

Then open:

- `http://localhost:4173/index.html`

> You can use any static server/port; `4173` is just an example.

### 2) Connect live Google Sheet data

In the dashboard UI:

1. Click **⚙ Settings**
2. Paste your Google Apps Script Web App URL in **Apps Script Web App URL**
3. Click **Connect & Load Live Data**

If your script is deployed correctly, the dashboard will load live rows.

### 3) Share with teammates (no manual pasting for them)

After your URL is set:

1. Click **Copy Team Link**
2. Send that copied URL to your team

When they open it, the dashboard reads the `?source=...` parameter and auto-loads your sheet source.

---

## Google Apps Script deployment checklist

Use this Apps Script in your sheet project and deploy as a Web App:

```javascript
function doGet(e) {
  var sheet = SpreadsheetApp.getActiveSpreadsheet().getSheets()[0];
  var data = sheet.getDataRange().getValues();
  var headers = data[0];
  var rows = data.slice(1).map(function(row) {
    var obj = {};
    headers.forEach(function(h, i) { obj[String(h).trim()] = row[i]; });
    return obj;
  });
  var cb = e.parameter.callback;
  var json = JSON.stringify(rows);
  var out = cb
    ? ContentService.createTextOutput(cb+'('+json+')')
        .setMimeType(ContentService.MimeType.JAVASCRIPT)
    : ContentService.createTextOutput(json)
        .setMimeType(ContentService.MimeType.JSON);
  return out;
}
```

Deployment settings:

- **Deploy → New deployment → Web app**
- **Execute as:** Me
- **Who has access:** Anyone

If teammates see a timeout/error, this access setting is usually the cause.

---

## Troubleshooting

- **Blank or timed-out load**
  - Re-check Web App access is **Anyone**.
  - Re-deploy Apps Script after code updates.
- **Copy Team Link doesn’t copy**
  - Some browsers block clipboard in some contexts; copy the shown link manually.
- **Need to force refresh**
  - Click **↻ Sync Data** in the top bar.

