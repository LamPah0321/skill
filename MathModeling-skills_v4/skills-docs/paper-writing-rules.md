# Paper Writing Rules

This document gives writing rules for mathematical modeling contest papers generated with this repository.

The paper should describe what was actually done. It should not describe a cleaner or more impressive workflow than the one supported by artifacts.

## Contest paper format (official 华为杯 spec)

For "华为杯" Chinese graduate mathematical modeling contests, all physical formatting follows
[`contest-paper-format-huaweibei.md`](./contest-paper-format-huaweibei.md) (23rd edition, 2026-09-16).
That file is the single source of truth for layout; the hard rules that affect writing are:

- First page is the **abstract page** (title + abstract + keywords); body starts on the next page.
- **No header**; no author-identifying marks anywhere (anonymized).
- Page numbers start on the abstract page, centered in the footer, Arabic numerals from 1.
- Fonts: title 三号黑体 centered, first-level headings 四号黑体 centered, all other Chinese text 小四号宋体, single line spacing.
- Abstract must cover 建模思路 / 主要方法 / 模型 / 结果与结论 / 创新点 / 关键词; ≤ 2 pages; **no English abstract**.
- In-text citations are bracketed numbers `[1][3]`, references listed in order of first appearance; book citations must include page numbers; the final reference list must use the three official Chinese formats (book / journal / web resource); cited programs must note their source.

When these rules conflict with any earlier generic convention, the official spec wins.

## Basic rule

Write from artifacts.

Do not write from memory, intention, or plausible guesses.

A paper section should be based on:

- problem parse
- final method explanation
- human decision ledger
- data profile
- scripts
- frozen numbers and final result analysis
- figures
- robustness report
- solution package
- QA notes

## Do not invent

Never invent:

- data
- numerical results
- references
- figures
- tables
- parameter values
- experiment outcomes
- model performance claims

If evidence is missing, mark the section incomplete.

## Section guidance

### Abstract

The abstract must cover (official 华为杯 spec):

- 建模思路 (modeling idea)
- 主要方法 (main methods actually used)
- 模型 (models)
- 结果与结论 (main results that exist + final conclusions)
- 创新点 (innovations)
- 关键词 (keywords)

Keep it concise, generally no more than two pages. Do not add an English abstract.
Include robustness evidence if available.
Do not include unsupported numbers or claims.

### Problem restatement

Restate the problem in your own words while preserving the original meaning.

Include:

- background
- main goal
- subquestions
- required outputs
- constraints

Do not add new requirements that the problem did not ask for.

### Problem analysis

Explain how the problem is decomposed.

Include:

- task type for each subquestion
- dependencies between subquestions
- why the workflow order makes sense

### Assumptions

Assumptions should be necessary and connected to modeling needs.

Avoid generic assumptions that do not affect the model.

Bad assumption:

```text
Assume all data are accurate and reliable.
```

Better assumption:

```text
Because no independent measurement error information is provided, the recorded demand values are treated as the best available observations. The influence of abnormal values is checked in the data audit and robustness analysis.
```

### **Symbols**

Define variables before using them.

Distinguish:

- decision variables
- state variables
- parameters
- input data
- outputs

Include units where possible.

### **Data preprocessing**

Describe:

- data source
- field meaning
- missing values
- outliers
- unit handling
- transformations
- remaining risks

Do not claim raw data was edited if cleaned copies were used.

### **Model construction**

For each subquestion, explain:

- why the model fits the task
- what usable baseline was compared
- what human-approved main model was used
- variables and equations
- objective and constraints if applicable
- expected output

Do not claim improvement before showing a directly comparable baseline result. A diagnostic reference is not a baseline.

### **Model solution**

Describe how the model was solved.

Include:

- algorithm or solver
- computation procedure
- script reference
- output files

Avoid implementation details that are not supported by code.

### **Results analysis**

Use actual result artifacts.

Keep interpretation proportional to evidence.

Separate:

- what the result shows
- what the result may imply
- what the result cannot prove

### **Robustness and sensitivity analysis**

Use robustness-checker outputs.

Separate:

- stable conclusions
- fragile conclusions
- conclusion boundaries

Do not claim global robustness from limited checks.

### **Strengths and limitations**

Be specific.

Link limitations to:

- assumptions
- data
- method choice
- validation
- robustness findings

### **Conclusion**

Every conclusion should answer a subquestion.

Do not introduce new claims in the conclusion.

### **References**

Follow the official 华为杯 reference rules (see `contest-paper-format-huaweibei.md`):

- In-text citations use bracketed numbers `[1][3]`, in order of first appearance.
- Book citations must include page numbers.
- Listed references use the three Chinese formats:
  - Book: `[编号] 作者，书名，出版地：出版社，起止页码，出版年。`
  - Journal: `[编号] 作者，论文名，杂志名，卷期号：起止页码，出版年。`
  - Web resource: `[编号] 作者，资源标题，网址，访问时间（年月日）。`
- Cited programs must note their source.
- Do not paste raw English BibTeX styling into the final list.

## **Style**

Prefer:

- clear technical prose
- short paragraphs
- explicit links between method and result
- restrained conclusions
- consistent notation

Avoid:

- empty praise of the model
- vague claims such as “good performance”
- excessive adjectives
- unsupported causal language
- decorative wording
- AI-like filler

## **Claims checklist**

Before writing a claim, ask:

1. Does this claim answer a subquestion?
2. Which artifact supports it?
3. Is it a numerical claim?
4. Does the number exist in a result file?
5. Does it require a baseline comparison?
6. Does it require robustness evidence?
7. Is the wording too strong?

If no artifact supports the claim, do not write it.

## **Practical rule**

A weaker claim with evidence is better than a stronger claim without evidence.
