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
| Social | Per-channel stats (FB/IG + future slots), followers & views KPIs, socialDaily trend charts, top posts, GA4 social referrals (Not connected yet), 75% nationwide mix |
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
| `social` | Channel URLs/handles, mix rule, prepared/scheduled counts, **`stats`**, **`socialDaily[]`**, **`topPosts[]`**, **`siteReferrals`** |
| `social.stats.<channel>` | Per channel (`facebook`, `instagram`, plus slots `pinterest`, `reddit`, `x`): `followers`, `followersChange7d`, `followersChange30d`, `reach30d` / `viewers30d` (IG), `views30d`, `viewsChange30dPct`, `views3s30d`, `views1m30d`, `watchTime30d`, `viewsFromNonFollowersPct` / `viewsFromFollowersPct`, `interactions30d`, `reactions30d`, `comments30d`, `shares30d`, `engagementRate30d`, `linkClicks30d`, `postsPublished30d`, `profileVisits30d`, `avgImpressionsPerPost30d`, `scheduledAhead`, `topPost30d` `{title,date,views,url}`, `asOf`, `source`, `status` |
| `social.socialDaily[]` | `{date, channel, followers, views}` history for trend charts (daily job appends) |
| `social.topPosts[]` | Top posts last 30d: `{channel, title, date, views, url}` |
| `social.siteReferrals` | GA4 facebook/instagram sessions → site; stays *Not connected yet* until Analytics access |
| `openQuestions[]` | `{priority, item}` for Javier |
| `links[]` | `{label, url}` public links |
| `changelog[]` | `{date, summary}` |

### Daily routine (expected)

1. Read Google Ads for both campaign IDs (impressions, clicks, cost, eligibility, keyword review).
2. Append a row to `adsMetrics.dailyRows` for each campaign (yesterday’s closed day).
3. Update `adsMetrics.totals` / `byCampaign` and `meta.updatedAt`.
4. If GA4 / Search Console access exists, fill `siteTraffic.metrics`; otherwise leave *Not connected yet*.
5. Refresh CJ pending/declined/joined when checked.
6. **Social (Facebook):** refresh `social.stats.facebook` from Facebook Professional dashboard Insights (followers, views30d, engagement) and Buffer (`list_posts` / `get_aggregated_post_metrics` on the BenefitBoost Facebook Page only) for `postsPublished30d` and `scheduledAhead`. Append `{date, channel:"facebook", followers, views}` to `social.socialDaily`. Update `social.topPosts`. Leave `reach30d`, `linkClicks30d`, and `profileVisits30d` null/`Pending` when Insights does not show them.
7. **Social (Instagram):** refresh `social.stats.instagram` from Instagram Professional dashboard (Last 30 days) for `@benefitboost.us` in the **BenefitBoost** Chrome profile only (never the default/aifeed profile). Fill followers, views30d, viewers30d (IG shows Viewers instead of reach), profileVisits30d, interactions30d / engagementRate30d, topPosts. Append `{date, channel:"instagram", followers, views}` (also `views30d` when snapshotting) to `social.socialDaily`. Follower deltas: after the Nov 9 baseline of 23, compute 7d/30d from prior `socialDaily` rows; until history exists keep the note `Not shown — tracking starts today`. Account-level link taps often not shown on desktop → store `"Not shown on desktop"`. Update `social.combined` (FB+IG totals).
8. **Social referrals:** if GA4 access exists, fill `social.siteReferrals.facebookSessions30d` / `instagramSessions30d` (sessionSource facebook/instagram); otherwise keep *Not connected yet*.
9. Push `data.json` to `main`.

### Why these social fields

| Field | Why we track it |
|---|---|
| `followers` + 7d/30d change | Audience growth — are nationwide posts actually adding people? |
| `views30d` / `reach30d` / `viewers30d` | Distribution health (views = impressions; reach or IG Viewers = unique people) |
| `interactions30d` + `engagementRate30d` | Content quality — views without engagement mean weak creative or targeting |
| `linkClicks30d` | Mid-funnel action on-platform (still not the same as site sessions) |
| `postsPublished30d` + `scheduledAhead` | Cadence vs the 75% nationwide mix rule |
| `profileVisits30d` | Interest beyond the feed (Pending until Insights shows it) |
| `topPost30d` / `topPosts[]` | What to double down on next |
| `socialDaily[]` | Trend charts so one spike doesn’t look like a strategy |
| `siteReferrals` (GA4) | Did social traffic actually hit getbenefitboost.com? |
| Extra FB view splits (`views3s30d`, watch time, follower vs non-follower %) | Reel/video health on a young Page |

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
