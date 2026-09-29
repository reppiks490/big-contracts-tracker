# Big Contracts Tracker

A public **data bucket** for large business contracts: who won them, current status, and where they sit (recipient HQ and place of performance).

This repo does **not** contain every federal or commercial contract on earth. Full award files run to millions of rows. Instead it collects:

- Authoritative public sources you can query or download
- A stable schema for tracking a deal
- Snapshot notes on recent mega-awards (multi-billion dollar range)
- How status and location are defined in official data

## What “moving big contracts” means here

| Aspect | Meaning in public data |
| --- | --- |
| Award / win | Prime award or IDV task order recorded in SAM.gov / USAspending |
| Movement | New award, modification, option year exercise, transfer of work, or change of recipient |
| Status | Active vs closed-out CAR; period of performance; last action date; correction/delete flag on deltas |
| Where it sits | Recipient location (legal business / UEI address) and **place of performance** (city, state, country) |

Commercial private contracts are not systematically public. Material contracts sometimes appear in SEC 8-K / 10-K exhibits.

## Primary public sources

1. **USAspending.gov** — spending, obligations, recipient and place-of-performance geography. Bulk archives and API: https://www.usaspending.gov/ and https://api.usaspending.gov/
2. **SAM.gov Contract Awards** — authoritative record-level award actions (FPDS search now lives here). Account required for detailed search: https://sam.gov/contracting
3. **Award Data Archive** — pre-built FY full + monthly delta ZIPs: https://www.usaspending.gov/download_center/award_data_archive
4. **GovCon news** — GovCon Wire, Executive Gov, agency press releases for narrative on who won what this week.

Official API (no key): `https://api.usaspending.gov/api/v2/`

Useful endpoints:
- `POST /api/v2/search/spending_by_award/` — filtered award search
- `POST /api/v2/download/awards/` — async CSV/ZIP generation
- `GET /api/v2/awards/{generated_unique_award_id}/` — single award detail

Delta files include `correction_delete_ind`: blank = new, `C` = corrected, `D` = deleted.

## Tracking schema

See `schema/contract_record.json` and `data/mega_awards_snapshot.md`.

Required fields:

- `award_id` / PIID / generated unique award ID
- `recipient_name` + UEI
- `funding_agency` / awarding agency
- `potential_value_usd` and `obligated_usd` if known
- `award_date` and `pop_end` (period of performance end)
- `status` (active / closed / modification / option exercised / protested / recompete)
- `recipient_location` (city, state, country)
- `place_of_performance` (city, state, country)
- `naics` / `psc` when available
- `source_url`
- `as_of` date of the snapshot

## How to keep the bucket current

1. Pull the latest **delta** file from the Award Data Archive each month (posted around the 15th; deltas more often).
2. Filter rows where `federal_action_obligation` or potential value exceeds a threshold you care about (e.g. $100M+ or $1B+).
3. Join recipient UEI to SAM entity registration for current address / socio-economic flags.
4. Watch GovCon Wire / agency award announcements for narrative status (protest, protest withdrawn, option year, stop-work).
5. Commit a dated snapshot under `data/snapshots/YYYY-MM-DD.md`.

DoD contract details can lag ~90 days on public sites.

## Limits

- Not all commercial “big contracts” are public.
- Subawards are thinner than primes.
- “Constant status” in official systems is batch-updated, not live tick-by-tick.
- This repo is a starting bucket, not a production CLM system.

## License

Government award data is U.S. Government work (public domain). Commentary and structure in this repo: use freely.
