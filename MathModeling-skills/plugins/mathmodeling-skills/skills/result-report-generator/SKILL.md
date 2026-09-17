---
name: result-report-generator
description: Summarize modeling experiment evidence, compare the approved main method with a usable baseline, surface fallback triggers, and produce a decision-point or final report without creating routine per-round prose.
---

# Purpose

Turn saved experiment artifacts into compact evidence. Do not treat ordinary successful runs as requiring a long report, and do not choose the winning method.

# Inputs

- `run_summary.json`
- method card and probe summary
- decision ledger
- saved tables, metrics, and figures
- session `rigor_profile`

Stop if the run summary claims outputs that do not exist or if main and baseline are not comparable.

# Modes

## Ordinary lean round

- Validate the run summary and referenced artifacts.
- Return a compact evidence digest in the conversation.
- Do not save a Markdown report unless:
  - a fallback trigger fired;
  - a material anomaly or contradiction exists;
  - the human must make a proceed/adjust/fallback decision.

## Decision-point round

Save:

`results/Qx/experiments/roundN/qx_decision_report.md`

Include only:

- main vs baseline metrics;
- output-degeneracy/concentration evidence;
- assumption or feasibility warnings;
- robustness evidence already available;
- fallback trigger state;
- unresolved trade-offs.

Then invoke `decision-prompt-builder`. After the human answers, route the answer to `modeler-decision-logger`.

## Final/submission mode

Save:

`results/Qx/reports/qx_final_result_analysis.md`

Include:

- final main/baseline comparison;
- uncertainty and error;
- concentration/degeneracy interpretation;
- robustness links;
- limitations and applicable scope;
- exact source paths for numerical claims.

**Deep result discussion (mandatory, four layers per Initial Prompt Rule 6):**

The final result analysis MUST contain a dedicated "结果讨论" (Discussion) section with four subsections:

1. **实际意义解释 (Practical interpretation)**: Translate every key numerical result into physical/domain/business meaning. Do not leave numbers unexplained. For example, if a coefficient is 0.174, explain what a 1-standard-deviation increase in that variable means for the outcome in real-world units.

2. **与基线/文献对比 (Comparison)**: Compare the main model's results with the baseline and with relevant literature findings. Explain why differences exist — is it due to data characteristics, model assumptions, or sample composition? If no literature is available, state that explicitly and compare with the baseline only.

3. **反直觉结果分析 (Counterintuitive results)**: Actively identify and analyze any counterintuitive, anomalous, or unexpected results. Do NOT avoid or gloss over them. For example: if a variable has the opposite sign from theory, if AUC is near 0.5 for a classification task, or if a known predictor is insignificant — investigate and explain possible causes (confounding, multicollinearity, sample size, model misspecification).

4. **局限性与适用边界 (Limitations and boundaries)**: State explicitly what the results do NOT cover, what assumptions could be violated, and under what conditions the conclusions might break. Include sample limitations, model assumptions, external validity concerns, and computational constraints.

A result analysis that only reports metrics without these four discussion layers is INCOMPLETE and must be revised before handoff to the writer.

# Rejection and Fallback

- Archive a method only after a human `result_verdict` or `fallback_activation` decision.
- Move rejected code and outputs to `workspace/archived/<Qx>/<method>_REJECTED_roundN/`.
- Add one compact history line to `qx_method_card.md`; do not create a separate iteration log.
- Do not archive from an AI suggestion alone.

# Rules

- Do not fabricate metrics, comparisons, or interpretations.
- Separate facts from human verdicts.
- Do not create `result-report-generator_modeler_decision.md`.
- Do not repeat the full run summary; cite it and extract only decision-relevant evidence.
- Do not call a diagnostic reference a usable baseline.
- Do not generate paper prose.

# Verification

- Every reported number resolves to a saved artifact.
- Main/baseline comparison uses the same split, unit, and metric definition.
- Output concentration and fallback trigger are addressed.
- Reports are generated only at decision points or final mode.
- Human verdicts are read from or appended to the canonical JSONL ledger.
