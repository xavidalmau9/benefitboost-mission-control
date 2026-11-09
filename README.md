# Miami Metro · BenefitBoost Mission Control

Live growth dashboard for **BenefitBoost** ([getbenefitboost.com](https://getbenefitboost.com)), operated by **Miami Metro LLC**.

- **Live URL:** https://xavidalmau9.github.io/benefitboost-mission-control/
- **Repo:** https://github.com/xavidalmau9/benefitboost-mission-control

Layout and polish follow the same glassmorphic Mission Control pattern used on Javier’s other dashboards (dark theme, Space Mono + DM Sans, cyan/purple/green accents, Chart.js). This repo is **BenefitBoost / Miami Metro only** — separate brand, separate campaigns, separate data.

## Sections

| Tab | Contents |
|---|---|
| Overview | Budget KPIs, campaign cards, affiliate pipeline chart, open questions, changelog, links |
| Google Ads | Nationwide (`24448608398`, $20/day) + Florida (`24438311912`, $10/day), settings, daily metrics table |
| Site traffic | GA4 `G-GX501BDC70` + Search Console status (metrics stay *Not connected yet* until API access) |
| Affiliates | CJ publisher CID `8113194` — joined / pending / declined / win-backs / Impact / Lifeline partner |
| Social | Facebook Page, Instagram handle (to confirm), 75% nationwide mix rule, Buffer queue status |
| Open items | Blockers for Javier + reference links |

## Updating data

Edit **`data.json` only**. Never invent numbers — use real Ads / CJ / analytics values, or leave fields null / `"Not connected yet"`.

Commit and push to `main`. GitHub Pages redeploys in about a minute via `.github/workflows/pages.yml`.

### Schema (top-level keys)

| Key | Purpose |
|---|---|
| `meta` | Title, brand, product, site URL, timezone, `updatedAt`, `dataWindow`, notes |
| `budgets` | `dailyTotalUsd`, `nationwideDailyUsd`, `floridaDailyUsd`, `maxCpcUsd`, bidding label |
| `campaigns[]` | Per-campaign settings (id, campaignId, name, status, eligibility, budget, geo, keywords, negatives, RSA, notes) |
| `adsMetrics` | `totals`, `byCampaign`, and **`dailyRows[]`** (date, campaign, impressions, clicks, costUsd, status) — daily job appends here |
| `siteTraffic` | GA4 id, Search Console status, `metrics` (null until connected) |
| `affiliates` | CJ CID, joined count, `pending[]`, `declined[]`, `winBacks[]`, Impact, Lifeline partner |
| `social` | Facebook URL, Instagram handle, content mix rule, prepared/scheduled post counts |
| `openQuestions[]` | `{priority, item}` for Javier |
| `links[]` | `{label, url}` public links |
| `changelog[]` | `{date, summary}` |

### Daily routine (expected)

1. Read Google Ads for both campaign IDs (impressions, clicks, cost, eligibility, keyword review).
2. Append a row to `adsMetrics.dailyRows` for each campaign (yesterday’s closed day).
3. Update `adsMetrics.totals` / `byCampaign` and `meta.updatedAt`.
4. If GA4 / Search Console access exists, fill `siteTraffic.metrics`; otherwise leave *Not connected yet*.
5. Refresh CJ pending/declined/joined when checked.
6. Push `data.json` to `main`.

## Public safety

This repo is **public**. Do **not** commit:

- Secrets, tokens, vault IDs, passwords
- EIN / tax IDs
- Google Ads customer / billing account IDs
- Private email credentials or personal cell numbers

Public site URLs, campaign IDs (the long numeric campaign IDs shown in Ads UI), and CJ publisher CID are fine.

## Local preview

```bash
python3 -m http.server 8080
# open http://localhost:8080
```

## Design notes

Free CDN libraries only (Google Fonts, Chart.js). No paid services. Favicon is an inline SVG “BB” mark.
