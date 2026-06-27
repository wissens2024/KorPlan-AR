# KorPlan-AR

**Building-Code-Aware Autoregressive Vector Floor-Plan Generation with Modular Regulatory Verification**

Reference implementation for the paper. KorPlan-AR generates Korean apartment vector
floor plans as wall-cycle token sequences with constrained autoregressive decoding, then
verifies and repairs them against a **modular Korean building-code engine** inside a
**probabilistic–deterministic hybrid loop** (generate → verify → local repair *or* regenerate).

> This is a clean-room implementation; no third-party generator source code is reused.

## What this releases

- **Engine** — `src/plan2graph/generators/wall_cycle.py` (decoder-only AR + constrained decoding),
  `src/plan2graph/wallcycle_codec.py` (tokenizer / decoder), `data/vocab/*.json` (vocabulary).
- **Regulatory verifier** — `src/plan2graph/rules_legal.py` (6 mandatory rules, each traceable to
  a statute article and Korea Law Information Center serial id), `src/plan2graph/gen_verify.py`
  (geometric + regulatory verify, rule-targeted repair).
- **Representation / render** — `geomgraph.py`, `graph_repair.py`, `cadrender.py` (SVG/PNG + DXF).
- **Training / evaluation** — `scripts/train_wall_cycle.py`, `scripts/eval_ar_geom.py`,
  `scripts/diag_legal_rules.py` (per-rule compliance), `scripts/diag_rejection_gated.py`
  (draw-budget), `scripts/render_geomclean.py` (strict "drawable" metric).

**Not included** (by design): the internal review dashboard, source drawings, and trained
checkpoints. Checkpoints (R / K / RK) are released separately (see *Models*). Source datasets
(RPLAN, AI-Hub) are redistributed by their providers only; the preprocessing scripts here
regenerate the processed token corpora from the originals.

## Install

```bash
python -m venv .venv && source .venv/bin/activate
pip install -r requirements.txt
```

Run with the source and scripts directories on the path, from the repository root:

```bash
PYTHONPATH=src:scripts python scripts/eval_ar_geom.py \
    --ckpt <checkpoint>.pt --vocab data/vocab/tokens_korean_gated.json \
    --n 200 --constrained --orthogonal --country 0
```

## Models

Trained checkpoints (R = RPLAN, K = AI-Hub target-only, RK = RPLAN→AI-Hub fine-tuned) are
archived with a DOI; see the release page. Place a `.pt` file anywhere and pass it via `--ckpt`.

## Reproducing the paper

| Result | Command |
|---|---|
| Geometric ablation (Table 3) | `scripts/eval_ar_geom.py --ckpt <model> --n 200 --constrained --orthogonal` |
| Per-rule compliance (Table 6) | `scripts/diag_legal_rules.py --ckpt <RK> --vocab data/vocab/tokens_korean_gated.json --n 200` |
| Rejection draw-budget (Table 5) | `scripts/diag_rejection_gated.py --ckpt <RK> --vocab data/vocab/tokens_korean_gated.json --n 200` |

## License

MIT (see `LICENSE`). Source datasets and trained models are governed by their providers' terms.

## Citation

```
Oh, J.C.; Park, J.W. Building-Code-Aware Autoregressive Vector Floor-Plan Generation
with Modular Regulatory Verification. (under review).
```
