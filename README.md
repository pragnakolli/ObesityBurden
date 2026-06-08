# v2 Engine — member-level analysis (synthetic demo)

This folder is the **v2 engine**: the member-level analysis that runs on a health plan's own
enrollment/claims data instead of public aggregates.

## About the data in this demo
The sample here runs on **synthetic data** — fabricated members calibrated to public benchmarks,
containing **no real patients and no PHI**. It demonstrates exactly what the engine computes; the
figures are illustrative, not real-world findings. The engine reads a pluggable CSV (schema below),
so the same analysis runs on a plan's own extract inside their environment (see *Compliance framing*).

## Files
- `v2_engine.py` — generator + analysis + HTML report writer.
- `synthetic_medicaid_members.csv` — 50,000 synthetic NJ Medicaid members (the stand-in "claims extract").
- `v2_sample_report.html` — the engine's output readout. **Open this to see what v2 produces.**

## How to run
```
python3 v2_engine.py      # writes the CSV + the HTML report
```

## Input schema (pluggable — this is what makes it swappable)
One row per enrollee. Point `load_members(path)` at any CSV with these columns:

| column | meaning |
|---|---|
| `member_id` | unique enrollee id |
| `age`, `sex` | demographics |
| `county` | county name (for geographic targeting) |
| `bmi_class` | `<30` / `30-34.9` / `35-39.9` / `40+` |
| `obese`, `severe_obese` | 0/1 flags (BMI ≥30, ≥40) |
| `diabetes` | 0/1 flag |
| `enrolled_months_2023` | 1–12 (for continuous-enrollment filtering / churn) |
| `total_medical_paid_2023` | annual medical $ paid (ex-pharmacy) |

Synthea or a real client extract just needs to be mapped to these columns.

## What it computes (the v2 value)
- **Measured** obesity / diabetes / comorbid prevalence — no FPL proxy, no self-report correction
  (that's the whole point of having real member data).
- **Adjusted excess cost** (age/sex-standardized, continuous enrollees) → total addressable $.
- **County targeting** — need-index × volume priority list, on actual members.
- **GLP-1 scenario** with a **differential offset** (0% obesity-only, ~8% obesity+diabetes) — reflecting
  that offsets concentrate in the comorbid group.

## v1 vs v2 (the difference)
- **v1 (public demo):** *estimates* the Medicaid rate from public data (income proxy + self-report
  correction). Good for "is there a problem here?"
- **v2 (this):** *measures* it on the plan's own members — exact prevalence, spend, and county counts
  they can budget against.

## Compliance framing (real engagement)
In a paid engagement this runs **inside the client's environment under a BAA** — their data never
leaves their walls; only aggregate outputs (county rollups, with small-cell suppression) come back.
The synthetic dataset here is for demonstration only — not real patients, not clinical guidance.

## Calibration (so the demo is defensible)
Synthetic data (seed 42) tuned to: ~40% low-income obesity (NHANES 2021–23), obesity–diabetes county
correlation ≈0.65–0.70, MEPS excess cost $1,861/$3,097 (Cawley 2021). Realized in this run:
40.8% obesity, r=0.65, ~$3,001 adjusted excess/member (higher than $1,861 because obese members carry
comorbid-diabetes cost — itself a finding the engine surfaces).
