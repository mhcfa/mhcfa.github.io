---
title: "f8: Turning Excel Financial Models into Compiled Artifacts for AI Agents"
subtitle: "Why AI agents struggle with raw spreadsheets—and how a semantic 2D DSL and 6-pass Rust compiler let agents and humans author verified, live .xlsx financial models in Python and TypeScript."
description: "An introduction to f8 (f8-engine), an open-source Rust compiler with Python and TypeScript SDKs that compiles semantic 2D financial models into verified, live Excel workbooks."
pubDate: 2026-11-17
featured: false
readingTime: "6 min read"
tags:
  - "f8-engine"
  - "Rust"
  - "Financial Modeling"
  - "Python"
  - "TypeScript"
---

## Why I Built `f8`

I am a CFA charterholder working in private M&A and project finance, where complex Excel models remain the non-negotiable deliverable despite fragile manual work, silent copy-paste errors, and zero version control.

When I started building autonomous AI agents to run institutional equity research and transaction models, I quickly hit a wall. AI agents struggle to manipulate raw spreadsheets (`openpyxl`, Office.js, or GUI) for two fundamental reasons:

1. **Brittle spatial coordinates**: Agents must reason in spatial cell addresses (`C42:G42`) instead of domain semantics. Inserting a single row shifts every downstream reference.
2. **No verification loop**: Spreadsheet writer libraries do not evaluate formulas in memory, leaving agents completely blind until the `.xlsx` file is opened in Excel and manually inspected.

[**`f8`** (`github.com/mhcfa/f8`)](https://github.com/mhcfa/f8) solves this by turning the `.xlsx` workbook into a **compiled artifact** rather than a hand-edited canvas:

- **Semantic 2D modeling**: Author models with readable table, row, and column identifiers; `f8` handles spatial layout and lowers logic into native Excel formulas.
- **Compiler-enforced rigor**: A 6-pass Rust compiler verifies grid completeness (zero unassigned cells or formula overlaps) and enforces a strict DAG via Tarjan cycle detection.
- **Closed-loop verification**: Evaluates formulas in memory so AI agents and test suites (`pytest`, `vitest`) can audit tables, trace dependencies, and verify accounting checks in milliseconds, without opening Excel.
- **Verified Excel parity**: Matches Excel across all 34 supported functions, combining compile-time type and shape verification with exact Excel domain error propagation and the 1900 leap day bug.

---

## Who `f8` Is For

1. **People who want AI to reliably build complex Excel models (financial and beyond)**: Give AI agents a semantic 2D DSL and compile-time verification to produce clean and error-free `.xlsx` workbooks with live formulas.
2. **People doing financial analysis in pure code (no Excel required)**: Run row-by-row financial statements, corkscrew schedules, and cross-table dependency graphs in Python or TypeScript (where columnar DataFrames fall short), with `compiled.save("model.xlsx")` ready whenever a stakeholder asks for the spreadsheet.

---

## A Quick Look

`f8` ships on both PyPI and npm under the package name **`f8-engine`**:

```bash
# Python
pip install f8-engine   # or: uv add f8-engine

# TypeScript / JavaScript
npm install f8-engine   # or: pnpm add f8-engine
```

Here is a PP&E corkscrew schedule written in **Python**:

```python
from f8 import Model

years = ["2025A", "2026E", "2027E"]
capex = {"2025A": 25.0, "2026E": 30.0, "2027E": 35.0}
depr = {"2025A": -15.0, "2026E": -18.0, "2027E": -22.0}

model = Model("Operating Forecast")
sheet = model.add_sheet("Summary")

ppe = sheet.add_table("PPE", "PP&E Corkscrew Schedule", "($ in millions)")
ppe.add_column("item", role="descriptor", header="Line Item")
for year in years:
    ppe.add_column(year, header=year, format="currency", decimals=1)

ppe.add_row(
    "beg_balance",
    cells={"item": "Beginning Balance", "2025A": 100.0},
    expr='PPE["end_balance", col.prev]',
)
ppe.add_row("capex", cells={"item": "Capital Expenditures", **capex})
ppe.add_row("depreciation", cells={"item": "Depreciation", **depr})
ppe.add_row(
    "end_balance",
    cells={"item": "Ending Balance"},
    expr='SUM(PPE["beg_balance".."depreciation", @col])',
    style="total",
)

compiled = model.compile()

# Audit evaluated values in memory (or pass view="formulas" to inspect lowered Excel formulas)
print(compiled.table("PPE").to_markdown())

# Emit a live .xlsx workbook with native Excel formulas and styling
compiled.save("forecast.xlsx")
```

And the same schedule in **TypeScript**:

```typescript
import { Model } from "f8-engine"

const years = ["2025A", "2026E", "2027E"] as const
const capex = { "2025A": 25.0, "2026E": 30.0, "2027E": 35.0 }
const depr = { "2025A": -15.0, "2026E": -18.0, "2027E": -22.0 }

const model = new Model({ name: "Operating Forecast" })
const sheet = model.addSheet("Summary")

const ppe = sheet.addTable("PPE", "PP&E Corkscrew Schedule", {
  subtitle: "($ in millions)",
})
ppe.addColumn("item", { role: "descriptor", header="Line Item" })
for (const year of years) {
  ppe.addColumn(year, { header: year, format: "currency", decimals: 1 })
}

ppe.addRow("beg_balance", {
  cells: { item: "Beginning Balance", "2025A": 100.0 },
  expr: 'PPE["end_balance", col.prev]',
})
ppe.addRow("capex", { cells: { item: "Capital Expenditures", ...capex } })
ppe.addRow("depreciation", { cells: { item: "Depreciation", ...depr } })
ppe.addRow("end_balance", {
  cells: { item: "Ending Balance" },
  expr: 'SUM(PPE["beg_balance".."depreciation", @col])',
  style: "total",
})

const compiled = model.compile()
console.log(compiled.table("PPE").toMarkdown())
compiled.save("forecast.xlsx")
```

In-memory evaluated output:

| Line Item            |      2025A |      2026E |      2027E |
| :------------------- | ---------: | ---------: | ---------: |
| Beginning Balance    |     $100.0 |     $110.0 |     $122.0 |
| Capital Expenditures |      $25.0 |      $30.0 |      $35.0 |
| Depreciation         |    ($15.0) |    ($18.0) |    ($22.0) |
| **Ending Balance**   | **$110.0** | **$122.0** | **$135.0** |

---

## Sample Compiled Deliverables

Under the hood, `f8` runs a 6-pass compilation pipeline in Rust (`crates/f8-core/`):

`Spec -> IR Expansion -> DAG & Cycle Check -> Topological Evaluation -> Spatial Layout -> Formula Lowering -> .xlsx Emission`

Here are real-world deliverables compiled with `f8`:

- **[`costco-valuation.xlsx`](/deliverables/costco/costco-valuation.xlsx)**: Compiled 3-statement, DCF, and trading comparables financial model with 4,228 live Excel formulas ([read the full Costco autonomous run write-up](/blog/autonomous-costco-equity-research/)).
- **[`lbo_model.xlsx`](/deliverables/f8/lbo_model.xlsx)**: Multi-sheet leveraged buyout (LBO) model with transaction structuring, debt waterfall, and returns analysis ([source code at `github.com/mhcfa/f8-demo`](https://github.com/mhcfa/f8-demo)).

![LBO Executive Dashboard](/images/f8/lbo-dashboard.png)

![LBO Transaction Balance Sheet Bridge](/images/f8/lbo-transaction.png)

---

Explore the repository, documentation, and bundled AI agent modeling skills at **[github.com/mhcfa/f8](https://github.com/mhcfa/f8)**.
