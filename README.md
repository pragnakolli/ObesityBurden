[GitHub-README.md](https://github.com/user-attachments/files/28724018/GitHub-README.md)
# Obesity Burden Navigator
**Turning free public health data into a Medicaid planning tool.**

🔗 **Live demo:** https://pragnakolli.github.io/ObesityBurden/
📄 **v2 sample (member-level, synthetic):** https://pragnakolli.github.io/ObesityBurden/v2-Engine/v2_sample_report.html

---

## The problem
Medicaid and population-health teams plan obesity and GLP-1 budgets off the published state
obesity rate (~28% in New Jersey). For a Medicaid book that number is **wrong three ways**: it's
self-reported (understated), it's the general population (not the low-income Medicaid group), and
it's a single statewide figure that hides where the burden actually sits.

## What this does (the public demo — `index.html`)
Pick any U.S. state; it pulls **live CDC data** and returns:
- A **calibrated Medicaid-eligible obesity rate** (~40% in NJ vs. the ~28% headline) — re-based to
  the low-income population and corrected for survey under-reporting using a factor **derived from
  NHANES 2021–23 vs BRFSS for the low-income group (×1.16)**.
- **County targeting** — a priority list blending a need-index (obesity + diabetes + inactivity)
  with population volume.
- The **obesity–diabetes overlap**, a **disparities lens**, and an editable **GLP-1 budget-impact
  scenario**.
- A built-in **methodology panel** and explicit limitations.
- Multi-state **compare views** across all four Census regions + a national ranking.

## The v2 engine (`v2-Engine/`)
The public demo *estimates* the Medicaid rate from public aggregates. The v2 engine **measures** it
on a plan's own member-level data — exact prevalence, spend, and county counts, plus a GLP-1 scenario
with a differential medical-cost offset (concentrated in the obesity+diabetes subgroup). The sample
report here runs on **synthetic data calibrated to public benchmarks** (no real patients, no PHI).
In a real engagement it runs inside the client's environment under a BAA.

**In one line:** *the public version tells you the problem is real and roughly how big; the version
on your data tells you exactly how big, where, and what to do about it.*

## Data & method
CDC **BRFSS** (state + income), CDC **PLACES** (county), **NHANES** (measured-vs-self-report
correction), **MEPS** (excess cost: $1,861/adult, $3,097 severe — Cawley et al., JMCP 2021), and
2026 FPL/expansion thresholds. This is **calibrated estimation**, not a predictive model.

## Honest caveats
- Public data is a **proxy** for the Medicaid population, not the enrollees themselves.
- The self-report correction is a national-vs-subgroup factor — read calibrated rates as a range.
- Cost and GLP-1 figures are **illustrative** until run on a plan's real claims.
- The v2 sample uses **synthetic** data — it demonstrates the machinery, not real findings.
- Not clinical or individual guidance.

## Build
Single-file HTML calling public APIs from the browser (no backend, no cost); v2 engine in Python.
Built solo, AI-assisted, on $0 of data.
