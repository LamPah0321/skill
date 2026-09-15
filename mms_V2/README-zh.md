
 
 
 
安装与启动
 
1. 把  MMS_V2  放到项目目录
2. 把  Initial Prompt-zh.md （或英文版）的内容作为第一条消息发给 AI
3. AI 会先运行  workflow-orchestrator  检查当前阶段，然后从  problem-parser  开始
 
 
 
G1–G7 完整生命周期
 
G1 — PROBLEM_FRAMED（问题框架）
 
做什么： problem-parser  解析题目（目标/对象/约束/数据/输出/歧义）→  problem-classifier  给每个子问题定题型 →  data-auditor-cleaner  审计清洗数据 →  related-paper-analyzer  分析相关文献
产出： planning/parse/problem_parse.json 、 planning/classification/problem_classification.json 、 workspace/data_clean/ 、 workspace/data_profile.json 
通过条件：每个子问题都有明确输出、数据清单完整、歧义点已记录
⚠️ 关键规则：题目没解析完，不准选模型
 
 
 
G2 — METHOD_SCREENED（方法筛选）
 
做什么： method-selector  给每个子问题建方法卡（1个主方法 + 1个可用基线 + 最多1个条件备用）→ 跑风险探针（输出退化检查、假设检验、敏感性）
产出： methods/Qx/qx_method_card.md 、 methods/Qx/probes/risk_probe_summary.json 
通过条件：主方法和基线的探针结论为 PASS 或有理由的 CONDITIONAL；备用方法有明确触发条件
⚠️ 关键规则：方法卡没过风险探针，不准生成代码
 
 
 
G2.5 — METHOD_CHOSEN_BY_HUMAN（人工选定方法）
 
做什么： decision-prompt-builder  生成选择卡 → 使用者（你）在  methods/Qx/qx_decisions.jsonl  中记录 DECIDED 决策
产出：决策账本中的  DECIDED  记录
通过条件：每个子问题都有引用探针证据的人工方法选择
⚠️ 这是唯一需要你介入的关卡——AI 不会替你选方法，它会给候选和证据，你拍板
 
 
 
G3 — CODE_AND_EXPERIMENT_REVIEWED（代码与实验）
 
做什么： model-code-analyzer  定实现方案 →  python-model-code-generator （或 matlab）写代码 →  code-reviewer  审查（语法/输入契约/方法对齐/可复现/输出契约）→ 跑实验
产出： code/Qx/*.py 、 results/Qx/experiments/roundN/run_summary.json （含 figures/tables/metrics）
通过条件：主方法和基线都跑通了；run_summary 完整；代码审查通过命名检查
⚠️ 关键规则：没有结果 artifact，不准写带具体数字的论文结论
 
 
 
G4 — RESULTS_JUDGED_AND_FROZEN（结果判定与冻结）
 
做什么： result-report-generator  生成结果分析 →  robustness-checker  做稳健性/敏感性检查 →  final-method-explainer  写最终方法详解
产出： results/Qx/reports/qx_final_result_analysis.md 、 robustness/Qx/ 、 methods/Qx/qx_final_method_explanation.md 
通过条件（lean）：最终结果和稳定性决策有计算证据支撑
通过条件（submission，更严）：还需 final method explanation、final result analysis、robustness report、package sign-off、solution package、 frozen_numbers.json 
⚠️ 关键规则：没有 baseline 和稳健性检查，不准声称模型更优
 
 
 
G5 — PAPER_SECTION_READY（论文就绪）
 
做什么： figure-table-planner  规划图表 →  math-figure-generator  生成图 →  solution-package-builder  组装给论文手的材料包 →  paper-section-writer  分节写论文 →  paper-polisher  润色 →  reference-manager  管参考文献
产出： paper/main.tex （或 sections/）、 paper/figures/ 、 paper/refs.bib 
通过条件：三条写作者规则（最终方法详解存在、最终结果分析存在、论文手只看材料包不猜）；冻结数字可追溯；人工确认解读范围；图表已验证
⚠️ 关键规则：没有最终方法详解，不准写最终论文
 
 
 
G6 — FINAL_AUDIT_PASSED（最终审计）
 
做什么： consistency-auditor （符号/公式/数字前后一致）→  completeness-auditor （四问都答了、图表都有解释）→  quality-assurance-auditor （整体质量）
产出：各审计报告
通过条件：三个审计全部通过，不能互相代替
⚠️ 关键规则：QA 没通过，不准组装最终论文
 
 
 
G7 — CONTEST_REVIEW_PASSED（竞赛评审，新增强制关卡）
 
做什么： contest-paper-reviewer  以资深评委身份做 7 维度百分制评审（摘要10 + 假设符号10 + 模型深度25 + 求解实现20 + 结果验证15 + 创新推广10 + 写作规范10），输出 8 节完整评审报告
产出： paper/contest_review_report.md 、 paper/contest_review_history.jsonl 
通过条件：总分 ≥ 90/100；严重扣分项已解决；manifest 中  allowed.final_delivery = true 
未通过时：按评审报告的"关键提升建议"返回  paper-polisher / paper-section-writer  修改 → 重新评审 → 直到 ≥90
⚠️ 这是最终交付的硬门槛——任何情况下不得跳过，未达90分禁止交付
 
 
 
两种模式
 
 lean（默认） submission（终稿） 
触发 探索阶段 论文手交接或终稿 
G4要求 结果判定即可 还需冻结数字、方法详解、稳健性报告等 
G6 不强制 必须三审计全过 
G7 建议做但不强制 必须 ≥90 才能交付 
 
初始提示词默认  lean ，到终稿阶段切换为  submission  后 G6/G7 全部强制执行。
 
 
 
实际执行中的注意点
 
1. 唯一需要你介入的是 G2.5（选方法），其余 AI 自动推进，但每个 G 的产出物都会落盘，你可以随时检查
2. workflow-orchestrator 是调度器，每个阶段结束后它会检查 manifest，决定下一步路由到哪个 skill，不会跳步
3. 如果你想跳过某个环节（比如不需要相关文献分析），需要在初始提示词里明确说"跳过 related-paper-analyzer"，否则默认全流程
4. G7 的 90 分门槛很严，实际竞赛中一等奖水平才够；如果你的目标只是完成论文，可以在初始提示词里把门槛调低（比如"G7 达到75分即可交付"），但默认是90