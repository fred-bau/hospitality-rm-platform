# Decisions

Each entry: what was decided, why, and what else was considered.

## 001 – Original source files instead of a pre-cleaned copy (2026-11-26)
**Decision:** Use the authors' original H1.csv and H2.csv (Data in Brief, 2019), not the TidyTuesday combined file.
**Why:** Primary source; combining, trimming and labelling the two hotels becomes visible work in dbt staging.
**Considered:** TidyTuesday `hotels.csv` (already combined and cleaned by third parties).

## 002 – Data not committed; manual download verified by checksums (2026-11-26)
**Decision:** `data/` is git-ignored. Files are downloaded manually and verified with SHA-256 (`data_checksums.sha256`) and row counts.
**Why:** PubMed blocks scripted downloads; data in Git bloats history; checksums prove every copy is identical.
**Considered:** Committing the CSVs; scripted download (blocked, returns HTML).

## 003 – BigQuery in the EU multi-region (2026-11-25)
**Decision:** All datasets in location `EU`.
**Why:** Location cannot be changed later and datasets in different locations cannot be joined; EU fits GDPR expectations of German hotels.
**Considered:** Single region `europe-west3`.

## 004 – Cost control: €5 budget alert, credits excluded (2026-11-25)
**Decision:** Monthly €5 budget, alerts at 50/90/100%, savings and credits unticked. Per-query limit to follow in dbt (`maximum_bytes_billed`).
**Why:** With credits included, the trial credit hides real usage and alerts never fire. Spend caps do not cover BigQuery.
**Considered:** Spend cap enforcement (not available for BigQuery).

## 005 – Fictional properties in Munich (2026-11-24)
**Decision:** One fictional operator with a 45-room boutique hotel and 30 serviced apartments in Munich; 4 + 3 room types.
**Why:** Event-driven demand (Oktoberfest, trade fairs); Inside Airbnb has Munich data for a comp set; few room types keep pricing, mapping and parity checks manageable.
**Considered:** Berlin, Frankfurt (no Inside Airbnb data).

## 006 – Channex staging with the official Apaleo Import Tool (2026-11-25)
**Decision:** Connect Apaleo to Channex staging via the Import Tool, using a dedicated integration user.
**Why:** Mirrors a real hotel setup; confirmed by Channex. API limit: 10 price/restriction + 10 availability calls per minute per property, so any own sync sends only changes and retries on HTTP 429.
**Considered:** Own Python sync as the main route (kept as optional extra).

## 007 – Own pricing engine instead of PriceLabs (2026-11-26)
**Decision:** Build PriceLabs-style rules (base, min/max, occupancy, lead time, day of week, events) and push rates to Apaleo via API.
**Why:** PriceLabs confirmed it only connects to Apaleo production accounts, not developer accounts.
**Considered:** PriceLabs trial (not connectable); PriceLabs demo kept for product knowledge.

## 008 – Data Studio as BI tool (2026-11-24)
**Decision:** Dashboards in Data Studio (formerly Looker Studio).
**Why:** Free for viewers, native BigQuery connector, scheduled email delivery for owner reports; adds a second BI tool next to Tableau (Project 1).
**Considered:** Tableau Public.

## 009 – Raw layer loaded as STRING, typed in staging (2026-11-26)
**Decision:** H1 and H2 are loaded into `raw.h1_bookings` and `raw.h2_bookings` with every column as STRING; trimming, NULL handling and type casts happen in dbt staging.
**Why:** The source pads values with spaces and writes NULL as text, so type autodetection is unreliable. Raw stays an exact copy of the source, loads never fail on odd values, and every conversion is visible SQL covered by tests (ELT pattern).
**Considered:** `--autodetect` (risk of wrong types); a hand-written schema file at load time (explicit, but duplicates the typing logic that belongs in staging).