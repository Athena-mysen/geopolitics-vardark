# Iran Conflict Tracker — Daily Update Instructions

This document describes how to update `iran_conflict_briefing.html` with a new day's data and redeploy to Cloudflare Pages.

**Live site:** https://geopolitics.vardark.no
**Cloudflare project:** `geopolitics-vardark`
**API token:** `<CF_TOKEN_REDACTED>`
**Account ID:** `fc26b142068c6a967a58970d4b7f7e71`

> **Note:** The scheduled task fetches the live HTML directly from the site (via `curl`) so it works in any session without local path dependencies. Follow the same approach when running manually.

---

## What to update each day

### 1. Research today's figures

Search for today's news on the Iran conflict. Gather estimates for:

| Field | Description |
|---|---|
| `dates` | Label, e.g. `'Mar 26'` |
| `drones` | Shaheds / one-way attack drones fired (integer) |
| `ballistic` | Ballistic missiles fired (integer) |
| `cruise` | Cruise missiles fired (integer) |
| `intercept` | Intercept rate as integer percentage, e.g. `91` |
| `hits` | Confirmed impacts / successful hits (integer) |

If exact figures are unavailable, estimate conservatively from the 3-day trend.

### 2. Fetch the current live HTML

```bash
curl -s https://geopolitics.vardark.no -o /tmp/iran_briefing.html
grep -c "Interceptor Stocks" /tmp/iran_briefing.html   # should return 1+
wc -l /tmp/iran_briefing.html                          # should be 900+ lines
```

### 3. Edit the data arrays

Read lines ~495–515 of `/tmp/iran_briefing.html` to see the current arrays:

```javascript
const data = {
  dates:     ['Feb 28','Mar 1', ...],
  drones:    [720,540, ...],
  ballistic: [480,350, ...],
  cruise:    [15,12, ...],
  intercept: [92,90, ...],
  hits:      [85,70, ...],
```

Using the **Edit tool**, append today's value to the end of each array. Do not modify any existing values.

Also update two header fields:
- Find `Daily Briefing — March NN, 2026` → update to today's date
- Find `<div class="day-num">NN</div>` → increment by 1

### 4. Deploy

```bash
mkdir -p /tmp/deploy-pages
cp /tmp/iran_briefing.html /tmp/deploy-pages/index.html

cd /tmp && \
  CLOUDFLARE_API_TOKEN="<CF_TOKEN_REDACTED>" \
  CLOUDFLARE_ACCOUNT_ID="fc26b142068c6a967a58970d4b7f7e71" \
  npx wrangler pages deploy /tmp/deploy-pages \
    --project-name=geopolitics-vardark \
    --commit-dirty=true
```

### 5. Verify

After deployment, confirm:
- Wrangler reported success
- The live site shows the new date in the header
- Both tabs (Munitions Fired, Interceptor Stocks) are present

---

## ⚠️ Important rules

- **Never regenerate the HTML from scratch.** Always start from the `curl`'d live version.
- **Only append to data arrays.** Do not modify existing historical values.
- **Do not change the `SYSTEMS` object** or any chart/model code unless explicitly asked.

---

## Interceptor model parameters (for reference)

| System | Start | spk | BM frac | CM frac | Drone frac | Resupply/day |
|---|---|---|---|---|---|---|
| THAAD | 350 | 1.3× | 0.146 | 0.04 | 0.00 | 0 |
| Arrow 2/3 | 170 | 1.25× | 0.066 | 0.00 | 0.00 | 0 |
| Patriot PAC-3 | 2,200 | 1.6× | 0.52 | 0.65 | 0.15 | +5 |
| Iron Dome | 4,000 | 1.5× | 0.00 | 0.12 | 0.65 | +15 |

Calibration anchors: THAAD operationally exhausted ~Day 16 (USNI/DoD), Arrow critically low ~Day 14 (Semafor/US officials). Do not modify these without re-running calibration.
