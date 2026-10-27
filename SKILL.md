---
name: anvil-cf-seo-preflight
description: >-
  Use before claiming an Anvil hot-keyword site is Cloudflare-ready, go-live, or
  open-index-ready—run Anvil site QA first, then box OSS SEO/SU checks plus
  mainstream market audit tools (PSI/Lighthouse, W3C, securityheaders,
  schema.org, and catalogued commercial crawlers); keep fail-closed noindex
  until the user writes open-index approval.
---
# Anvil CF SEO preflight

Canonical checklist: `PlayGuideLab/anvilwiki-next` → `docs/anvil-site-qa-acceptance.md`  
Tool maps (OSS + market): same repo → `docs/anvil-cf-seo-preflight-oss.md`  
Always also run [Anvil site QA acceptance](sand-workflow:anvil-site-qa-acceptance) (§1–§8 + Emberville §9). This skill **adds** CF/open-index SEO + SU + market-tool gates; it does not replace locale purity.

**SU** = search spam / quality penalty risk (thin scaled pages, doorway/entity-swap, near-duplicates)—not a product name.

## When

- User asks for CF attach, “像真站”, go-live, website体检, or open-index readiness
- After content publish-loop batch, before promoting production alias
- Before any agent says Pass on `site-launch` with indexing intent
- When the user asks to cross-check with **mainstream** SEO/audit tools (not only our OSS)

## Steps

1. Confirm Anvil consumer (not long-form game-sites DB). Preview = `ANVIL_ENVIRONMENT=public-preview`.
2. Build reproducible preview (`ANVIL_EVALUATION_TIME` pinned when needed).
3. Run Anvil contracts: `check-content`, `check-i18n`, `check-links`, `node scripts/check-locale-purity.mjs` (or `pnpm check-locale-purity`).
4. Complete [Anvil site QA acceptance](sand-workflow:anvil-site-qa-acceptance): locale, noindex, evidence, links, mobile, visual, process. Capture Emberville regressions (§9): `bodyBlocks` vs `bodyMarkdown`, Keep exploring ∩ prev/next, inner pages not homepage-only.
5. **SEO fail-closed (preview):** `robots.txt` Disallow:/ ; meta + `X-Robots-Tag` noindex; sitemap empty or gated. Never open index / submit GSC without the user’s **written** open-index sentence.
6. **Box-runnable OSS SU / SEO:**
   - `npx pseolint ./out --ci-threshold concerning` **or** preview URL with `--no-respect-robots --no-respect-noindex --sample-size 40`. Treat `concerning`/`critical` thin, near-dupe, doorway, boilerplate as blockers unless pages are honestly labeled thin and not promoted.
   - `npx linkinator <preview-or-out> --recurse` (prefer after Anvil `check-links`; skip placeholder `example.invalid` hosts until real domain).
   - Sample Lighthouse SEO: home + ≥1 guide EN + ≥1 non-EN locale (`npx lighthouse <url> --only-categories=seo,accessibility --chrome-flags=--headless --quiet`).
   - Optional: `npx @rankture/cli validate robots ./out/robots.txt` and `validate sitemap` on built artifacts.
7. **Market / mainstream tools (required when user wants “市面体检”):** run what is agent-runnable; catalog the rest for the user. Save evidence under a dated `cf-preflight/market-tools-YYYY-MM-DD/` folder.
8. **SU ↔ content policy (crawlers miss these):** evidence grades only; stubs/`thin` labeled; **no invented codes or class tables**; one game entity per site (no doorway farm); preview host must not be logged as backlink-complete.
9. Fill the site-launch acceptance card (§10 template) and attach OSS + market command summaries.
10. **CF attach without open-index:** domain + DNS may proceed while remaining noindex; say so explicitly. **Open-index:** escalate options; do not flip env/robots/canonical alone.
11. Fail-closed: any locale, noindex, SU blocker, or missing written approval → do **not** claim CF-ready-for-index or site-launch Pass.

## Market tool roster

Official URLs, free/paid, what they check, and agent-runnability. Full tables live in `docs/anvil-cf-seo-preflight-oss.md` (Market section)—keep this skill summary in sync when the doc changes.

### Box / API first (agent should run)

| Tool | URL | Free/paid | Use when |
| --- | --- | --- | --- |
| Lighthouse / PSI | https://pagespeed.web.dev/ · API `pagespeedonline/v5` | Free (API key for quota) | Perf + SEO + a11y; local LH if PSI 429 |
| W3C Nu HTML Checker | https://validator.w3.org/nu/ | Free | Markup; `?doc=` or POST `out=json` on home + 1 guide |
| schema.org Validator | https://validator.schema.org/ | Free | JSON-LD / microdata sanity |
| securityheaders.com | https://securityheaders.com/ | Free UI (API key for JSON) | Or curl response headers directly (HSTS/CSP/XFO/…) |
| pseolint | npm `pseolint` | Free OSS | SU thin/near-dupe/doorway + tech SEO |
| linkinator | npm `linkinator` | Free OSS | Dead links after Anvil `check-links` |
| @rankture/cli | npm `@rankture/cli` | Free OSS | Offline robots/sitemap on `out/` |

### Google / Bing (free; account or property often required)

| Tool | URL | Notes |
| --- | --- | --- |
| Rich Results Test | https://search.google.com/test/rich-results | Prefer **code paste** if URL fetch hits robots Disallow |
| Mobile-Friendly Test | — | **Deprecated** (Dec 2023); use Lighthouse/PSI |
| Search Console URL Inspection | https://search.google.com/search-console | Needs GSC property — escalate |
| Bing Webmaster SEO reports | https://www.bing.com/webmasters | Needs property — escalate |

### Desktop / SaaS (user-run; not on box)

| Tool | URL | Free tier hint |
| --- | --- | --- |
| Screaming Frog | https://www.screamingfrog.co.uk/seo-spider/ | Free ≤500 URLs |
| Sitebulb | https://sitebulb.com/ | Trial / paid |
| Ahrefs Site Audit | https://ahrefs.com/site-audit | Free AWT when verified |
| Semrush Site Audit | https://www.semrush.com/siteaudit/ | Tiny free quota / paid |
| Moz Pro | https://moz.com/products/pro | Paid |
| Seobility | https://www.seobility.net/ | Freemium; bots often CF-blocked |
| Sitechecker | https://sitechecker.pro/ | Freemium; login walls |
| SEO Site Checkup | https://seositecheckup.com/ | Freemium |

### Links / perf / a11y / SSL

| Tool | URL | Notes |
| --- | --- | --- |
| Dead Link Checker | https://www.deadlinkchecker.com/ | Browser; prefer linkinator on box |
| Dr. Link Check | https://www.drlinkcheck.com/ | Browser / SaaS |
| GTmetrix | https://gtmetrix.com/ | Browser / API key |
| WebPageTest | https://www.webpagetest.org/ | API often needs key; public may CF-block |
| Pingdom Tools | https://tools.pingdom.com/ | Browser |
| WAVE | https://wave.webaim.org/ | Browser or WAVE API |
| SSL Labs | https://www.ssllabs.com/ssltest/ | **After custom domain**; N/A on placeholder host |

## Interpreting results: preview vs production

| Signal | Preview (fail-closed) | Production (open-index) |
| --- | --- | --- |
| Lighthouse / PSI `is-crawlable` = 0 | **Expected Pass** for noindex claim | **Blocker** — fix robots/meta/headers |
| pseolint `tech/robots-noindex-conflict`, `canonical-noindex-conflict` | Expected while staging | Resolve before claiming index-ready |
| meta `follow` vs header `nofollow` | Nit; Google takes stricter; align before open-index | Align both |
| Commercial crawler “blocked by robots” | Expected if Disallow:/ | Should crawl |
| Placeholder `example.invalid` in canonical/OG | Expected until real domain | **Blocker** |
| securityheaders missing CSP/XFO/… | Nit for CF-noindex attach; harden before public trust | Fix or accept risk |
| W3C `<time>` without `datetime` | Nit / factory fix | Fix before launch polish |
| Thin / near-dupe from pseolint | Document honesty; do not promote thin hubs | Blocker if scaled doorway farm |
| GSC / Bing / SSL Labs | Skip or N/A | Required after domain + open-index |

## Box-runnable command cheatsheet

```bash
# Fail-closed spot-check
curl -sSI "$PREVIEW/" | rg -i 'x-robots-tag|strict-transport'
curl -sS "$PREVIEW/robots.txt"

# OSS
npx pseolint "$PREVIEW" --no-respect-robots --no-respect-noindex --sample-size 40
npx linkinator "$PREVIEW" --recurse --skip 'example\.invalid'
npx lighthouse "$PREVIEW/" --only-categories=seo,accessibility --chrome-flags=--headless --quiet --output=json --output-path=lh-home.json

# Market APIs
curl -sS -G 'https://validator.w3.org/nu/' --data-urlencode "doc=$PREVIEW/" --data-urlencode 'out=json'
# PSI (optional key): https://www.googleapis.com/pagespeedonline/v5/runPagespeed?url=...&strategy=mobile&category=seo
# security: curl -sSI "$PREVIEW/" and checklist HSTS/CSP/XFO/X-CTO/Referrer-Policy/Permissions-Policy
```

## SU ↔ Anvil mapping (unchanged)

| Risk | Tool signal | Anvil gate |
| --- | --- | --- |
| Thin / scaled | pseolint `spam/thin-content` | §3 density; label stubs; no promote |
| Near-dupe / template | pseolint SimHash / doorway | Unique observation per guide |
| Doorway / entity-swap | pseolint entity-swap | One game entity per site |
| Invented codes/tables | **Not** caught by crawlers | Evidence grades; no invent |
| Locale mix | Anvil `check-locale-purity` | §1 — crawlers miss this |
| Preview as “backlink done” | Ops | Preview host ≠ submit target |

## Out of scope (escalate)

Domain purchase, Cloudflare passwords, AdSense, production index flip, GSC/Bing ownership, paid Ahrefs/Semrush/Moz seats, real outbound link submissions, Windows Compose for backlink layer B.
