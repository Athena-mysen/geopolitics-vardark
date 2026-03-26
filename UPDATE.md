# Iran Conflict Tracker — Daily Update Instructions

**Live site:** https://geopolitics.vardark.no
**GitHub repo:** https://github.com/Athena-mysen/geopolitics-vardark
**Cloudflare project:** `geopolitics-vardark`

Credentials (Cloudflare token, GitHub PAT) are stored only in the scheduled task prompt — not in this file.

---

## How the daily update works

Each day the scheduled task:
1. Clones the GitHub repo to `/tmp/tracker`
2. Appends today's conflict figures to the `const data` arrays in `index.html`
3. Updates the header date and day counter
4. Commits and pushes back to GitHub
5. Deploys `index.html` to Cloudflare Pages via wrangler

The **repo is the source of truth**. Cloudflare Pages always reflects the latest commit on `main`.

---

## Running an update manually

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

### 2. Clone the repo

```bash
cd /tmp && rm -rf tracker
git clone https://<GITHUB_USER>:<GITHUB_PAT>@github.com/Athena-mysen/geopolitics-vardark.git tracker
```

Verify the file is intact:
```bash
grep -c "Interceptor Stocks" /tmp/tracker/index.html   # must be 1+
wc -l /tmp/tracker/index.html                          # must be 900+ lines
```

### 3. Check what's already there

Read lines ~495–515 of `index.html` to confirm today's date isn't already the last entry (to avoid double-appending on a retry):

```javascript
const data = {
  dates:     ['Feb 28','Mar 1', ...],   // <-- check the last value
  drones:    [720,540, ...],
  ballistic: [480,350, ...],
  cruise:    [15,12, ...],
  intercept: [92,90, ...],
  hits:      [85,70, ...],
```

### 4. Append today's data

Using the **Edit tool**, append today's values to the end of each array. Do not modify existing values.

Also update:
- The date in `Daily Briefing — March NN, 2026`
- The day counter: read the current `<div class="day-num">NN</div>` and set it to `dates.length` (i.e. the new total number of entries)

### 5. Commit and push

```bash
cd /tmp/tracker
git config user.email "task@geopolitics-vardark"
git config user.name "Iran Tracker"
git add index.html
git commit -m "Data update: $(date +'%b %d %Y')"
git push https://<GITHUB_USER>:<GITHUB_PAT>@github.com/Athena-mysen/geopolitics-vardark.git main
```

### 6. Deploy to Cloudflare

```bash
mkdir -p /tmp/deploy-pages
cp /tmp/tracker/index.html /tmp/deploy-pages/index.html
cd /tmp && \
  CLOUDFLARE_API_TOKEN="<CF_TOKEN>" \
  CLOUDFLARE_ACCOUNT_ID="fc26b142068c6a967a58970d4b7f7e71" \
  npx wrangler pages deploy /tmp/deploy-pages \
    --project-name=geopolitics-vardark \
    --commit-dirty=true
```

---

## Rules

- **Never regenerate the HTML from scratch.** Always start from the cloned repo.
- **Only append to data arrays.** Never modify existing values.
- **Do not change the `SYSTEMS` object**, chart code, or methodology section unless explicitly asked.

---

## Interceptor model parameters (for reference)

| System | Start | spk | BM frac | CM frac | Drone frac | Resupply/day |
|---|---|---|---|---|---|---|
| THAAD | 350 | 1.3× | 0.146 | 0.04 | 0.00 | 0 |
| Arrow 2/3 | 170 | 1.25× | 0.066 | 0.00 | 0.00 | 0 |
| Patriot PAC-3 | 2,200 | 1.6× | 0.52 | 0.65 | 0.15 | +5 |
| Iron Dome | 4,000 | 1.5× | 0.00 | 0.12 | 0.65 | +15 |

Calibration anchors: THAAD operationally exhausted ~Day 16 (USNI/DoD), Arrow critically low ~Day 14 (Semafor/US officials). Do not modify without re-running calibration.
