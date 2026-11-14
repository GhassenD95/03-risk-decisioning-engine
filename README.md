# Risk & Decisioning Engine
**Calibrated risk scoring with reason codes, a case-review workflow, and drift monitoring.**

> Real product: web UI + API + workers + database (not a notebook).  
> Built around decisioning problems seen in PFE offers: **Olindias** (risk engine & document analysis), **Anypli** (payment-delay prediction & collections), **Orange** (billing prediction), **Flexee** (predictive AI).

## Why this project
Most ML demos stop at a notebook score. Real risk systems must be **time-correct, calibrated, explainable, and monitored**, and their outputs must drive a human workflow. This project treats scoring as a decisioning product, not a model file.

## What you can do
- **Scoring service:** batch + on-demand risk scoring (e.g. payment-delay / invoice or transaction risk) on a public or synthetic dataset framed with finance-like constraints.
- **Decision console (UI):** scores with **reason codes/confidence**, prioritized case queue, analyst actions (approve/review/escalate), and outcome capture (feedback loop).
- **Model & policy management:** versioned models/thresholds, champion vs challenger, cost-sensitive decision cutoffs.
- **Monitoring:** data/feature and score drift, calibration over time, latency/throughput, and degradation alerts.
- **Audit trail:** every prediction and human decision recorded with model/version, feature snapshot ref, and rationale.

## Architecture
```text
[Web UI (React/Next)] -> [FastAPI: scoring, cases, feedback, monitoring]
                                  |
                     [Postgres: entities, features metadata, predictions,
                                decisions, outcomes, audit log]
                                  |
                     [Workers: feature builds, batch scoring, drift/calibration jobs]
                                  |
                     [Model artifacts + registry]  <->  [Metrics/dashboard]
```
- Point-in-time correct features shared across train/serve; scoring returns score + calibration + reason codes.

## Hard engineering problems
- **Leakage-free modeling:** time-aware splits, point-in-time features, documented safeguards against label/target leakage.
- **Calibration & thresholds:** probabilities you can act on (calibration/Brier), with cost-sensitive cutoffs — not accuracy alone.
- **Explainability & audit:** stable reason codes/attributions and a versioned record of every prediction and decision.
- **Monitoring & drift:** detect data/score shifts (with an injected-shift demo) and track delayed-label performance.
- **Serving as a product:** versioned models, idempotent scoring, p99 latency, and a feedback loop.

## Proof (what “done” means)
| Claim | Evidence |
|---|---|
| Better than naive | PR-AUC/lift & calibration vs a strong baseline and a trivial heuristic |
| Time-correct | Leakage checklist + point-in-time tests; temporal validation results |
| Well-calibrated | Calibration/reliability plots and Brier score; threshold rationale |
| Detects drift | Injected distribution-shift demo with detection lead time/alerts |
| Production-shaped | p99 inference latency, batch throughput, versioned audit of predictions |

## Stack
Python 3.12+, FastAPI, Pydantic v2 · scikit-learn baseline + gradient boosting (LightGBM/XGBoost) where justified · numpy/pandas/Polars · PostgreSQL (+ object storage/artifact registry, e.g. MLflow-light) · workers (Celery/arq + Redis) · React/Next.js · pytest, data/feature tests, ruff/mypy, GitHub Actions · Docker Compose.

## Status
**Design / spec-first.** Milestones: `framing & dataset` → `leakage-safe features & baselines` → `calibrated model + serving` → `case console & feedback` → `drift monitoring & hardening`.
