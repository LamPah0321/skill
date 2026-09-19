---
name: workflow-orchestrator
description: Inspect a mathematical-modeling workspace, evaluate lean or submission gates per subquestion, update machine-readable manifests, classify change impact, and route one next action without duplicating downstream work.
---

# Purpose

Act as the gate-driven scheduler and state reader. Do not solve models, write model code, or draft paper sections.

`../../AGENTS.md` is the packaged policy source. Prefer a project-root `AGENTS.md` when one exists; otherwise read the packaged copy relative to this `SKILL.md`. Apply that policy without reproducing large reports or dashboards.

# Session Start

Before orchestration in a new workspace:

- show `git status --short`;
- check the chosen runtime and required core packages;
- verify the workspace skeleton needed for the current request;
- read `planning/session_config.json`, accepting legacy `mode`.

Report warnings concisely. Do not create the full project skeleton unless the user is initializing a project.

# State Sources

Prefer, in order:

1. `planning/manifests/Qx.json`
2. canonical artifacts on disk
3. legacy dashboard and legacy method/decision artifacts

Never trust a dashboard over newer canonical artifacts.

# Manifest Contract

Maintain one compact JSON manifest per subquestion:

```json
{
  "schema_version": 1,
  "question_id": "Q1",
  "rigor_profile": "lean",
  "current_gate": "G2",
  "status": "method_screened_waiting_human",
  "artifacts": {
    "method_card": "methods/Q1/q1_method_card.md",
    "decision_ledger": "methods/Q1/q1_decisions.jsonl",
    "risk_probe": "methods/Q1/probes/risk_probe_summary.json",
    "latest_run": null
  },
  "allowed": {
    "code_generation": false,
    "freeze": false,
    "paper_writing": false,
    "final_assembly": false,
    "final_delivery": false
  },
  "blockers": [],
  "next_action": {
    "owner": "human",
    "skill": "decision-prompt-builder",
    "reason": "method choice not recorded"
  },
  "updated_at": "ISO-8601"
}
```

Update only fields affected by the current state change. Generate a human dashboard on request or at a milestone; otherwise derive status directly from manifests.

# Gate Evaluation

Evaluate each Qx independently.

## G1 — PROBLEM_FRAMED

Pass when parse, classification, data inventory, success criteria, and human framing exist. A placeholder in a human-owned field blocks the gate.

## G2 — METHOD_SCREENED

Pass when:

- `qx_method_card.md` defines a main candidate and usable baseline;
- the baseline completes the real task with comparable output;
- `risk_probe_summary.json` covers applicable checks, including output degeneracy;
- main and baseline verdicts are `PASS` or justified `CONDITIONAL`;
- any fallback has a concrete trigger.

Do not require a fixed number of candidates, universal PoCs, or a source-line limit.

## G2.5 — METHOD_CHOSEN_BY_HUMAN

Pass when `qx_decisions.jsonl` contains a human `DECIDED` method choice citing probe evidence. While blocked, allow data preparation but not model code generation.

## G3 — CODE_AND_EXPERIMENT_REVIEWED

Pass when:

- approved main and baseline executed;
- latest `run_summary.json` is complete;
- language review contains passing named checks for syntax, input contract, method alignment, reproducibility, and output contract.

Accept legacy Markdown review artifacts during migration, but prefer JSON for new work.

## G4 — RESULTS_JUDGED_AND_FROZEN

In `lean`, pass the result-judgment subgate when final-result and stability decisions cite computed evidence. Continue iterating without freezing when the human selects `adjust` or `fallback`.

In `submission`, additionally require:

- final method explanation;
- final result analysis;
- robustness report;
- package sign-off in the decision ledger;
- solution package;
- current `frozen_numbers.json`.

## G5 — PAPER_SECTION_READY

Require the three writer rules, frozen-number sourcing, human-confirmed interpretation/claim scope, and verified figures.

## G6 — FINAL_AUDIT_PASSED

Evaluate only in `submission`. Require passing consistency, completeness, and QA artifacts. Never infer that one auditor covers another.

## G7 — CONTEST_REVIEW_PASSED

Evaluate only in `submission`. This is the **final delivery gate** — no paper may be delivered before G7 passes.

Pass when:
- `paper/contest_review_report.md` exists with a complete 8-section review;
- the latest review total score is **≥ 95/100**;
- `paper/contest_review_history.jsonl` records the latest round;
- all "严重扣分项" (severe deductions) in the review are resolved or explicitly justified;
- the manifest `allowed.final_delivery` is set to `true`.

If the score is < 95, route to `paper-polisher` / `paper-section-writer` for targeted fixes per the review's "关键提升建议", then re-run `contest-paper-reviewer`. Do not deliver, do not set `final_delivery=true`.

G7 is never skipped in `submission` mode. In `lean` mode, G7 is not required but a review is still recommended before handoff.

# Routing

Choose one primary next action:

- missing framing → parser/classifier or human framing card;
- missing data profile → `data-auditor-cleaner`;
- missing method card/probe → `method-selector`;
- missing human method choice → `decision-prompt-builder`;
- approved method without implementation plan → `model-code-analyzer`;
- code/review incomplete → language generator or reviewer;
- meaningful experiment awaiting judgment → result choice card;
- final results without robustness → `robustness-checker`;
- submission package incomplete → final explainer, result report, or package builder;
- paper ready but unaudited → the earliest missing final auditor.
- G6 passed but G7 (contest review) not passed → `contest-paper-reviewer`; if score < 95, route fixes to `paper-polisher` / `paper-section-writer` then re-review. **Never deliver before G7 ≥ 95.**
- paper written but reproducibility not checked → `reproducibility-check` (clean rerun + seed/dependency/path check).
- abstract written but not checked → `abstract-checker` (truncation, number consistency, 4-part structure).
- paper numbers not verified against results → `data-vs-paper-check` (extract numbers from tex, compare with csv/json).
- **Order before delivery**: G5 paper written → reproducibility-check → data-vs-paper-check → abstract-checker → G6 audits → G7 contest review.

Do not invoke several judgment-bearing skills speculatively.

# Change Impact

Classify changes before scheduling checks:

- `NONE`: scratch, formatting, comments, non-semantic docs.
- `LOCAL`: exploratory code or method-card updates before freeze.
- `CANONICAL`: schema/units, symbols, equations, parameters, official values, figure paths.
- `FROZEN`: changes affecting frozen values or paper claims.

Route checks:

- `NONE`: none.
- `LOCAL`: local tests/review.
- `CANONICAL`: scoped consistency for affected Qx.
- `FROZEN`: thaw log, rerun affected work, re-freeze, scoped consistency.

Never schedule a full-workspace consistency audit solely because more than one file changed.

# Lean vs Submission

In `lean`:

- require only manifests, method card, decision ledger, probe summary, and run summaries;
- do not require per-round Markdown reports, full success logs, frozen numbers, paper artifacts, or final audits;
- persist a detailed report only at a human decision point or final round.

In `submission`:

- require final explanations, reviews, analyses, robustness, package, freeze, paper, and G6;
- **require G7 (contest-paper-reviewer ≥ 95) before any final delivery**;
- run the full three-auditor layer once before final assembly.

# Compatibility

Read legacy artifacts when new ones are absent:

- `planning/progress_dashboard.md`
- `qx_method_candidates.md`
- `qx_method_iteration_log.md`
- `qx_decision_log.md`
- `decisions/*_modeler_decision.md`
- Markdown code reviews

Mark them `legacy_source` in the manifest and recommend migration at the next material edit. Do not regenerate legacy files for new work.

# Output

Return a compact state report:

- profile;
- per-question current gate and blocker;
- artifacts changed or missing;
- change-impact class;
- one next action;
- optional runners-up only when they can proceed independently.

Do not paste a full dashboard or large JSON structure unless the user asks.

# Verification

- State is derived from current canonical artifacts.
- Lean requirements are not confused with submission requirements.
- Human decisions were not inferred from AI suggestions.
- Code generation, freeze, paper writing, and final assembly flags match the gates.
- Audit scope matches semantic impact.
- Manifest and reported next action agree.
- **G7 enforcement: `final_delivery` is false until contest review score ≥ 95; no delivery occurs before G7 passes.**

# G1–G7 不偷懒纪律（强制）

实际运行中曾出现 organizer 跳过 G1–G6 直接写论文、再补流程文件的情况。
workflow-orchestrator 必须在每一步检查以下纪律：

1. **禁止跳阶段写论文**：G5（PAPER_SECTION_READY）之前不得开始写任何 paper/sections/ 下的文件。
   如果发现 sections/ 下有文件但 G1–G4 的产物缺失，必须打回，先补齐 G1–G4 再继续。
2. **每阶段产物必须真实存在于磁盘**：manifest 里声明的 artifact 路径必须
   实际有文件且非空。不能只在 manifest 里写路径而不生成文件。
   检查项：
   - G1: planning/session_config.json、problem_parse.md、manifests/Qx.json
   - G2: methods/Qx/qx_method_card.md、methods/Qx/probes/risk_probe_summary.json
   - G2.5: methods/Qx/qx_decisions.jsonl
   - G3: results/Qx/experiments/round1/metrics/run_summary.json、language_review.json
   - G4: methods/Qx/qx_final_method_explanation.md、results/Qx/reports/qx_solution_package_for_writer.md、
     robustness/Qx/qx_robustness_report.md、frozen_numbers.json
   - G6: consistency_audit.md、completeness_audit.md、paper/qa_report.md
   - G7: paper/contest_review_report.md、paper/contest_review_history.jsonl
3. **禁止自评代 G7**：G7 的评审必须由独立 agent 执行（见 contest-paper-reviewer SKILL.md
   的"作者与评审必须分离"）。如果 contest_review_report.md 是由写论文的同一个 agent 生成的，
   视为无效，必须重开独立 agent 评审。
4. **submission 模式为默认**：除非用户明确要求 lean，否则 rigor_profile 必须为 submission，
   不得自行降级到 lean 来跳过流程。
5. **发现跳阶段时的处理**：如果发现论文已写完但中间产物缺失，
   不要让论文"先交着"再补——必须回到缺失的阶段补齐产物，
   并在补齐后重新走 G6/G7 审计。
