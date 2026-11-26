# Hospitality Revenue Management Platform

A revenue management system for a fictional Munich operator: a 45-room boutique hotel and 30 serviced apartments, run on Apaleo (PMS) and Channex (channel manager), with a BigQuery + dbt + Airflow pipeline for daily pickup, pace vs. same time last year, forecasts, rate-parity checks and a monthly owner report.

**Status:** work in progress (phase 1 of 6: planning and setup).

## For hoteliers
*Coming soon: what this system answers, in plain language.*

## For technical readers
*Coming soon: architecture, data model, how to run it.*

Stack: BigQuery · dbt · Airflow (Docker) · Python · Data Studio · Apaleo · Channex

## Data

Hotel Booking Demand datasets: Antonio, N., de Almeida, A., & Nunes, L. (2019). *Data in Brief*, 22, 41–49. https://doi.org/10.1016/j.dib.2018.11.126

Real booking data from two hotels in Portugal (arrivals July 2015 – August 2017), used for analysis and to give the fictional Munich properties realistic booking patterns.

The source blocks automated downloads. To reproduce:
1. Download `mmc2.zip` (Appendix A) from https://pmc.ncbi.nlm.nih.gov/articles/PMC6297060/
2. Unzip `H1.csv` and `H2.csv` into `data/raw/`
3. Verify the files:

| File | Rows (excl. header) | SHA-256 |
| --- | --- | --- |
| H1.csv (resort hotel) | 40,060 | `6f38fda13794fd6cf6547d2432ffb79666200000eb45fd09e4b97fe49519a6ab` |
| H2.csv (city hotel) | 79,330 | `5107090ffdc5585a9e18299801e9d85c3b459c235542882183a502a73ef63c4d` |

Row counts reconciled against the publication (40,060 + 79,330 = 119,390).

Verify with `shasum -a 256 -c data_checksums.sha256` (macOS), `sha256sum -c data_checksums.sha256` (Linux) or `Get-FileHash -Algorithm SHA256` (Windows).

## Decisions
See [DECISIONS.md](DECISIONS.md) for the main design choices and why they were made.