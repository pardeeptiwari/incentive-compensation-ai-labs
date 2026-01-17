# Incentive Compensation Diagnostics — Executive Summary (Synthetic Data)

**Audience:** AI Labs / Product, CRO / VP Sales Ops  
**Dataset:** 50,000 employee-year records (2010–2024), roles/regions/plan types, quota/attainment/payout, tenure, team, product mix.  
**Analytic subset:** 30,655 valid records (quota > 0, non-negative attainment, required dimensions present).

---

## 1) What we set out to learn
1) **Data quality readiness:** missingness, invalid values, outliers, and how we handle them.  
2) **Business patterns:** attainment and payout behavior across role/region/plan type, year shocks, and tenure cohorts.  
3) **Reusable product signals:** interpretable metrics that can power AI agents, dashboards, and alerting.

---

## 2) Data quality observations (and practical handling)
**Key issues observed**
- **Missing fields** exist in tenure (~2.0%), quota (~0.5%), payout (~0.5%), and plan type (~0.1%).  
- **Invalid values** are present (e.g., non-positive quotas and occasional negative attainment/payout), consistent with real-world realities like corrections, clawbacks, or ingestion errors.
- **Heavy tails / outliers** in attainment and payout ratios (e.g., rare extreme over-attainment and high payout-to-quota points).

**How we handled it (for decision-quality insights)**
- Core analyses use a **valid analytic subset**: quota > 0, attainment ≥ 0, and required dimensions present.  
- For visualization only, we **cap** extreme ratios (attainment_ratio ≤ 3.0, payout_to_quota ≤ 0.8) to avoid outliers dominating charts.
- Negative attainment/payout rows are **flagged for audit** and excluded from performance distribution summaries.

**Assumptions**
- Quota and attainment are in comparable units per record (or have been standardized).  
- Payout amount represents total incentive payout for the fiscal year.  
- If multiple rows exist per employee-year in real data (mid-year plan changes), we recommend adding a `plan_segment_id` and defining an aggregation rule.

---

## 3) Business insights (SVP-ready takeaways)

### Insight A — Attainment is mixed, with a meaningful under-attainment segment
Across the population, attainment distribution breaks into bands:
- **< 0.8:** 30.4%
- **0.8–1.0:** 24.6%
- **1.0–1.2:** 19.2%
- **> 1.2:** 25.8%

**Implication:** There is enough under-attainment to create **quota stress and disengagement risk**, while the over-attainment tail drives **budget volatility**.

---

### Insight B — Plan types differ in generosity at near-quota performance
At similar performance (attainment between 0.9 and 1.1), median **payout-to-quota** differs by plan type:
- **SPIF-heavy:** 11.68% of quota
- **standard:** 9.70% of quota
- **new-logo:** 8.07% of quota

**Implication:** Some plans are systematically **more generous** at target performance, which affects **fairness perception**, comp compression, and budget predictability.

---

### Insight C — “Quota stress pockets” exist by role × region
Highest under-0.8 attainment pockets (top examples):
region              role  pct_under_0p8
  APAC   Channel Manager           34.8
 LATAM  Customer Success           33.7
 LATAM Account Executive           33.5
  APAC  Customer Success           32.3
 LATAM   Channel Manager           32.3
  APAC     Sales Manager           31.9
 LATAM     Sales Manager           31.4
 LATAM               BDR           31.3

**Implication:** These pockets are strong candidates for **quota calibration**, territory/coverage review, or ramping guardrails.

---

### Insight D — Year-over-year shifts show macro/quota-reset effects
Median attainment varies by year; the **lowest median** occurs in **2020** (median ~0.918) and the **highest** in **2021** (median ~0.991).

**Implication:** A product should separate **plan design signals** from **macro-year shocks**, and support year normalization for fair comparisons.

---

### Insight E — Tenure strongly affects volatility (ramp risk)
New-hire attainment variance is about **1.80×** the variance of experienced reps (≥24 months).

**Implication:** A “ramp-aware” quota/plan experience reduces churn risk and improves manager coaching decisions.

---

### Insight F — Overpayment risk is rare but material
In the near-quota band (0.9–1.1), payouts above the **99th percentile threshold** (>0.162 payout-to-quota) occur in **~1.00%** of records.

**Implication:** Even a small rate can represent meaningful dollars; flagging these cases supports **auditability** and **trust**.

---

## 4) Product opportunities (AI + analytics)
1) **Plan Health Dashboard**
   - Compare generosity, punitive drop-off, accelerator sensitivity, and volatility across plan types and cohorts.

2) **Quota Calibration Assistant**
   - Detect quota stress pockets and recommend adjustments based on historical distributions and tenure mix.

3) **Overpayment & Anomaly Monitor**
   - Alert on extreme payout-to-quota given attainment + plan context; provide investigation workflow.

4) **Manager Coaching View**
   - Surface ramp-risk reps and high-variance cohorts; support proactive enablement actions.

---

## 5) Proposed reusable feature catalog (for AI agents)
- **Quota Stress Index (QSI):** % reps with attainment_ratio < 0.8 (by role/region/team/year)  
- **Attainment Consistency Score:** stability of attainment over time (inverse variance)  
- **Plan Generosity Index:** payout-to-quota at near-quota attainment vs baseline  
- **Punitive Drop-off Score:** payout drop between 1.0 and 0.8 attainment  
- **Accelerator Sensitivity:** slope of payout-to-quota above 1.1 attainment  
- **Overpayment Risk Score (0–100):** anomaly score for payout-to-quota given context  
- **Quota Reasonableness Label (Green/Amber/Red):** driven by stress + tenure mix + year normalization  
- **Team Concentration Risk:** payout share driven by top 10% earners within team-year  
- **Ramp Penalty Indicator:** attainment gap between new (<6m) and experienced (>24m) reps  
- **Product Mix Complexity:** mix entropy; association with attainment volatility

---

## 6) Recommended next steps (what I’d do with real data)
- Validate comp rules (caps, accelerators, SPIF stacking, clawbacks) to reduce false positives.  
- Add plan design tables (rate cards, caps, thresholds) and unify measure definitions for attainment.  
- Calibrate risk thresholds with finance/comp ops to align alerts to actionable triage.

