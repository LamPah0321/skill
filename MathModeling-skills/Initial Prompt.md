# Initial Prompt

[English](<./Initial Prompt.md>) | [简体中文](<./Initial Prompt-zh.md>)

You are helping me work through a mathematical modeling contest problem using this repository.

Before doing any modeling, coding, or writing, read the project rules and skills:

- AGENTS.md or CLAUDE.md
- the relevant SKILL.md files under .codex/skills/ or .claude/skills/

Use this repository as a staged mathematical modeling workflow, not as a one-shot paper generator.

Follow the standard workflow unless I explicitly override it:

workflow-orchestrator
→ problem-parser
→ problem-classifier
→ related-paper-analyzer
→ data-auditor-cleaner
→ decision-prompt-builder
→ method-selector
→ symbol-table-builder
→ model-assumptions-builder
→ model-code-analyzer
   ├── python-model-code-generator
   └── matlab-model-code-generator
→ code-reviewer
   ├── python-code-reviewer
   └── matlab-code-reviewer
→ result-report-generator
→ robustness-checker
→ final-method-explainer
→ figure-table-planner
→ math-figure-generator
→ solution-package-builder
→ paper-section-writer
→ paper-polisher
→ reference-manager
→ consistency-auditor
→ completeness-auditor
→ quality-assurance-auditor
→ contest-paper-reviewer
→ workflow-orchestrator

Core rules:

- Do not start by choosing a model.
- Start from goals, objects, constraints, data, outputs, variables, relationships, and checkable conclusions.
- Do not select methods before the problem is parsed.
- Do not generate code before the method card passes its risk probe and the human records a method choice.
- Do not write numerical paper claims before result artifacts exist.
- Do not claim a model is better without a baseline and robustness or sensitivity check.
- Do not assemble the final paper before QA passes.
- Do not deliver the final paper before G7 (CONTEST_REVIEW_PASSED) — the contest paper review reaches a total score of at least 95. G7 is a mandatory delivery gate that must never be skipped. Below 95, revise per the review findings and re-review.
- Do not modify raw data under workspace/data_raw/.
- Do not fabricate data, numerical results, references, figures, tables, experiments, or performance claims.
- All random seeds in Python code (random_state, np.random.seed, torch.manual_seed, etc.) default to `454520`, unless there is an explicit reason to use another value.
- Default to `interaction_mode: learning` and `rigor_profile: lean`; switch to `submission` only for writer handoff/finalization.
- Do not require full per-round reports, success logs, frozen numbers, or final audits while the profile is `lean`.
- Capture human choices in `methods/Qx/qx_decisions.jsonl`, not separate per-skill pending files.

Three critical rules:
- Rule 1: Do not write final paper sections for Qx unless methods/Qx/qx_final_method_explanation.md exists.
- Rule 2: Do not hand Qx to the writer unless results/Qx/reports/qx_final_result_analysis.md exists.
- Rule 3: The writer's primary source is results/QX/reports/qx_solution_package_for_writer.md — do not guess from scattered results.
- Rule 4: The paper must pass contest-paper-reviewer with a total score ≥ 95 before delivery. Below 95, revise per the review findings and re-review; no exceptions.
- Rule 5 (Mixed-model extension): After the main method is chosen, you MUST evaluate whether it can be extended to a mixed/hierarchical/Bayesian model to improve innovation. If the data has repeated measures, hierarchical structure, clustering, or individual heterogeneity, you MUST prioritize mixed-effects models (LMM/GLMM/GEE) or hierarchical Bayesian models, and record an "extension assessment" conclusion in the method card (adopted/rejected with reason). Never settle for the simplest OLS/Logistic without evaluating a more complex mixed model.
- Rule 6 (Deep result discussion): Each subquestion's result analysis MUST include four layers: (1) practical/physical/business interpretation of the numerical results; (2) comparison with baseline methods or literature and reasons for differences; (3) in-depth analysis of counterintuitive or anomalous results (never avoid them); (4) limitations and applicability boundaries of the results. Never report numbers without explanation, never gloss over with "the results show".

Use this workspace convention:

project/
├── planning/                   # Parse, classification, symbols, assumptions, manifests, session config
├── methods/Qx/                 # Method card, decision ledger, probes, final explanation, figure plan
├── code/Qx/                    # Python code
├── code/matlab/Qx/             # MATLAB code
├── results/Qx/
│   ├── experiments/roundN/     # Experiment outputs (figures/tables/metrics/run_summary.json)
│   └── reports/                # Experiment reports, final result analysis, solution packages
├── robustness/Qx/              # Robustness reports
├── paper/                      # Writer zone (sections/figures/refs.bib/main.tex/qa_report.md)
├── workspace/data_raw/         # Raw data (read-only)
├── workspace/data_clean/       # Cleaned data
└── scratch/                    # Temporary exploration

If this is a new contest problem, begin with problem-parser after workflow-orchestrator confirms the stage.

For problem parsing, extract:

- background
- main goal
- objects
- subquestions
- constraints
- data inventory
- required outputs
- preliminary variables
- preliminary relationships
- ambiguities
- risk flags
- missing information
- recommended next skill

Do not choose final models during problem parsing.

When analyzing the problem, consider:

1. Problem background
   Restate the key points of the problem. Identify the real-world context and related domains.

2. Assumptions and variables
   Identify useful simplifying assumptions, key variables, parameters, and relationships. Distinguish controllable variables, observable quantities, and external parameters.

3. Problem type
   Classify each subquestion separately. Do not force the whole problem into one type if the subquestions differ.

4. Related papers
   Collect and analyze relevant papers, reports, or reference solutions before final method selection. Extract transferable ideas, assumptions, data needs, and risks. Do not fabricate references or copy models blindly.

5. Modeling route
   Ask me to choose the output, interpretability, unacceptable-risk, and experiment-budget trade-offs first. Then screen one main candidate, one usable baseline, and at most one conditional fallback. Do not pad the shortlist. Use a method-specific risk probe that includes output concentration or degeneracy checks.

6. Solution and validation
   Define what outputs, metrics, tables, and figures are needed. Compare against baselines and plan robustness or sensitivity checks.

7. Limitations
   State what the model cannot support, what assumptions are strong, and what data limitations remain.

When writing paper sections later:

- Write in continuous, readable academic prose.
- Avoid generic filler.
- Avoid obvious AI-style phrasing.
- Do not use bullet points unless the section naturally requires them, such as symbols, tables, algorithms, or checklists.
- Do not add content that is not supported by existing artifacts.
- Every numerical claim must come from a result file.
- Every figure or table reference must correspond to an existing or explicitly planned artifact.
- Every conclusion must map back to a subquestion.

First task:

Run workflow-orchestrator now.

Return:

- current stage
- completed artifacts
- missing artifacts
- blocked items
- next skill
- concrete next actions
