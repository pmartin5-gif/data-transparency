# Bundled datasets

Public data stored with the page so lookups happen locally: no visitor data is sent anywhere to use these. Bundled in devlog 030 (2026-11-26). Read by the engine since devlog 032; the industry files since devlog 037. The page fetches these only for US connections.

## ZIP files (Census ACS)

**Source:** US Census Bureau, American Community Survey 5-year estimates, **2020–2024** (latest release). Table-based Summary File, downloaded 2026-11-26 from `www2.census.gov/programs-surveys/acs/summary_file/2024/table-based-SF/data/5YRData/` (the Census API now requires a key; the bulk files don't).

**Geography:** ZIP Code Tabulation Areas (ZCTAs), 33,772 areas. ZCTAs are the Census's approximation of USPS ZIP codes; most residential ZIPs match one-to-one, but PO-box-only and single-business ZIPs have no ZCTA, so a lookup by the ZIP from the IP lookup can miss.

**Format:** one JSON object per file, keyed by 5-digit ZIP. `_fields` names the array positions. `null` = Census didn't publish a value (too few households or no sample).

| File | Census tables | Fields |
|---|---|---|
| `zip-income-housing.json` (1.2 MB) | B19013, B25064, B25003 | median household income, its margin of error (90%), median gross rent, % of occupied homes owner-occupied, number of occupied homes |
| `zip-language.json` (2.3 MB) | C16001 | population 5+, then % speaking at home: English only, Spanish, French/Haitian/Cajun, German/West Germanic, Russian/Polish/Slavic, other Indo-European, Korean, Chinese, Vietnamese, Tagalog, other Asian/Pacific, Arabic, other |
| `zip-industry.json` (2.4 MB) | C24050 (lines 2–14) | civilian workers 16+, then % in each of 13 industry sectors |
| `zip-industry-earnings.json` (2.9 MB, added in devlog 037) | B24031 (lines 1, 2, 5–9, 12, 13, 16, 20, 23, 26, 27) | median earnings of all workers, then median earnings in each of the same 13 sectors. A sector's figure is `null` when its margin of error is over 30% of the estimate (top-coded $250,000+ figures are kept) |

**Caveats for rules:**
- Income is **top-coded at 250,001** (= $250,000 or more); rent at **3,501** (= $3,500 or more). Treat these as "at least."
- Income is missing for 3,225 ZIPs and rent for 8,021. Small ZIPs have large margins of error (e.g. 12440: $106,553 ± $19,479); rules should check the count and margin before leaning on a figure.
- Dollar figures are in 2024 dollars.
- Industry shares and earnings describe residents of the ZIP by the sector they work in, wherever their job is. Earnings are per worker, in 2024 dollars.

## `gpc-states.json`

The 11 states whose privacy law requires businesses to honor a universal opt-out signal such as Global Privacy Control, with the law and the date the requirement applies. `gpcNamed` marks the four whose regulators have said explicitly that GPC counts (California, Colorado, Connecticut, New Jersey; per the GPC project's FAQ, Jan 2025). In the others the law requires honoring opt-out signals in general, and GPC is the main one in use.

Sources (checked 2026-11-26):
- [GPC FAQ](https://globalprivacycontrol.org/faq): the four states with explicit guidance
- [Koley Jessen, "Universal Opt-Out Mechanisms Explained"](https://www.koleyjessen.com/insights/publications/universal-opt-out-mechanisms-explained), [Fisher Phillips, 2025 state privacy laws](https://www.fisherphillips.com/en/news-insights/2025-state-privacy-laws-taking-effect.html), [FPF effective dates chart v1.7](https://fpf.org/wp-content/uploads/2025/07/FPF-Effective-Dates-Chart-v1.7.pdf), [DWT Maryland tracker](https://dwt.com/gcp/state-privacy-laws-tracker/broad-state-privacy-law-tracker/maryland), [IAPP on Colorado](https://iapp.org/news/a/colorados-approach-to-universal-opt-out-requirements)

**Checked in devlog 037:**
- **Maryland removed.** The enacted act (2024 Md. Laws ch. 455, SB 541; Md. Com. Law § 14-4607(F)(3)) lets a business meet its opt-out duty *either* with a link on its site *or* by honoring an opt-out preference signal, so honoring GPC isn't required. The April 1, 2026 date in secondary sources is the applicability date of § 14-4612 only. Source: [enrolled chapter 455](https://mgaleg.maryland.gov/2024RS/chapters_noln/Ch_455_sb0541E.pdf)
- **New Jersey confirmed:** in force 2025-01-15; the signal requirement applies six months later, 2025-07-15 (Baker Donelson, Hunton, White & Case summaries agree)

**Open checks:**
- States whose laws took effect in 2026 without a signal requirement (e.g. Indiana, Kentucky, Rhode Island) are left out. Recheck the list when new laws take effect.
- California's Opt Me Out Act (AB 566) requires browsers to offer an opt-out signal by 2027-01-01. It applies to browsers, not sites, so it isn't on this list.
