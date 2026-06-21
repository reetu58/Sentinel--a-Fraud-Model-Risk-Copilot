# Sentinel — A Fraud Model Risk Copilot

> Monitor a deployed fraud-scoring model over a real transaction stream, detect
> drift and performance/fairness decay daily, retrieve the governing policy via
> RAG, and draft a regulator-style risk memo with a recommended action — gated
> by a human who must approve before anything happens.

## Why this exists

In April 2026, **SR 26-2** replaced SR 11-7 and deliberately placed generative
and agentic AI **outside** model-risk guidance, while keeping traditional ML
fraud models fully in scope. That creates a real gap: the most consequential AI
in a bank — agents that take action — sits in a guidance vacuum, while the
classical models they monitor still demand rigorous oversight.

Sentinel sits on that fault line. It monitors an in-scope traditional fraud
model **and** demonstrates the controls the carve-out now demands for agentic
systems:

- **Defined agent actions** — every agent has a narrow, named responsibility.
- **Mandatory human approval** — nothing side-effecting happens autonomously.
- **Immutable audit trail** — every decision cites its policy source and is
  written to an append-only log.

It's the kind of governance scaffolding a risk function will want before it
lets any agent near a production model.

## What it does

1. Streams public transaction data through an XGBoost fraud scorer
   (Kafka / Redpanda locally).
2. A daily Airflow DAG computes **PSI** on the model score, **CSI** per feature,
   plus FPR and fairness metrics against a frozen baseline, persisted in
   Postgres.
3. A LangGraph agent graph runs over the day's metrics:
   **Monitor → Investigator (RAG over governance corpus) → Drafter → Human gate**.
4. The Drafter produces a regulator-style memo translating drift into a
   **business implication** (false declines / fraud losses / decision
   instability) — never just statistics.
5. A FastAPI + React dashboard surfaces the memo and the approve / reject gate.

## Data policy

**Public datasets only.** Sentinel uses IEEE-CIS, the Bank Account Fraud Suite
(for fairness slices), and PaySim (for scale). Real bank data is confidential
and is **never** used in this project. Raw data and trained model binaries are
gitignored and must be re-derived from documented sources.

## Stack

Python · Kafka/Redpanda · Airflow · Postgres · XGBoost · LangGraph ·
Haystack/RAGFlow · Anthropic + OpenAI (via a thin router) · FastAPI · React ·
Docker · GCP Cloud Run.

## Status

Early scaffolding. See [`CLAUDE.md`](CLAUDE.md) for the canonical spec,
architecture, and conventions, and `docs/research/` for the scoping brief and
policy notes.

## License

MIT — see [`LICENSE`](LICENSE).
