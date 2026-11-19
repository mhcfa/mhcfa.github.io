---
title: "Running an Autonomous Agent on Costco Equity Research: Why It Called a Short"
subtitle: "In a 4-hour overnight run with zero human intervention, a 22-agent workflow ingested 50 SEC/macro filings, compiled a 6,500-cell live Excel model with f8-engine, and initiated Costco as a $617.94 Short—exposing a fascinating discount-rate puzzle."
description: "What happened when I gave an autonomous multi-agent workflow a three-sentence prompt to value Costco (NASDAQ: COST): 4,228 live Excel formulas, a 21-slide pitchbook, a $617.94 Short call, and a 0.22 2-year beta hidden in the audited data."
pubDate: 2026-11-18
featured: true
readingTime: "8 min read"
tags:
  - "Autonomous Agents"
  - "Equity Research"
  - "Costco (COST)"
  - "f8-engine"
  - "FAAC"
slideDeck:
  title: "Costco Wholesale Corp. (NASDAQ: COST) — Institutional Valuation & Short Initiation"
  subtitle: "9-sheet compiled Excel workbook (4,228 live formulas) & 21-slide presentation generated overnight with zero human edits."
  pdfUrl: "/deliverables/costco/costco-equity-valuation.pdf"
  xlsxUrl: "/deliverables/costco/costco-valuation.xlsx"
  xlsxLabel: "Download Excel Model (costco-valuation.xlsx)"
  slideCount: 21
  slidePrefix: "/images/costco/slide-"
---

## The Autonomous Run

On November 17 at 1:07 AM, I kicked off an autonomous multi-agent research workflow (part of [**FAAC — Financial Analysis as Code**](https://github.com/mhcfa/faac), with the compiled Costco TypeScript model open-sourced in [`public-equity/costco`](https://github.com/mhcfa/faac/tree/main/public-equity/costco)) with a three-sentence prompt:

> `/autonomous-research`
>
> This is a new project that I would like you to take on, this is an autonomous equity valuation task on Costco. Our goal is to analyze the business, and provide a valuation. Plz follow the instructions, delegate to subagents, and proceed with your judgement.

Just after 5:06 AM, the run finished and committed the final deliverables.

## The Result

Above, you can browse or download the two **unedited** deliverables the agent produced overnight:

- **[Excel Financial Model (costco-valuation.xlsx)](/deliverables/costco/costco-valuation.xlsx)**: Compiled with [`f8-engine`](https://github.com/mhcfa/f8) — 9 worksheets, 35 tables, 6,500 cells, and 4,228 live Excel formulas across Base, Bull, and Bear cases. Zero macros, zero hardcoded plugs, and balance sheet checks tying to $0.00.
- **[Valuation Presentation (costco-equity-valuation.pdf)](/deliverables/costco/costco-equity-valuation.pdf)**: A 21-slide (16:9) presentation compiled from TypeScript, where every table, chart, callout, and footnote is bound directly to the compiled model and audited source data.

Between 1:07 AM and 5:06 AM, with zero human intervention, the workflow:

1. **Acquired and triaged 50 raw source files** from SEC EDGAR, FRED, US Treasury, and market data feeds—ingesting **30 audited JSON bundles** (37 schedules, 148 tables, 5,108 quantitative cells) covering 5 years of Costco 10-Ks (FY22–FY26), Q4 FY26 earnings releases, the November 10, 2026 $30.00/sh special dividend 8-K, September/October 2026 monthly sales releases, and 5 retail peers (BJ, PSMT, WMT including Sam's Club segment breakouts, TGT, and KR).
2. **Built a 15-year integrated 3-statement model** (5Y historical FY22A–FY26A + 10Y explicit forecast FY27E–FY36E) driven bottom-up by regional warehouse openings (U.S., Canada, Other International), core ex-gas/FX comparable sales, new-club Year-1 productivity ramp, paid membership density and Executive tier fee yield, working capital float (DIO, DPO, deferred membership fees), owned PP&E vs. ASC 842 operating leases, and individual senior note maturities.
3. **Ran an unlevered DCF, trading comps, and unit-economics benchmark**, complete with a post-FY26 liquidity bridge for the $13.3B ($30.00/sh) special cash dividend declared on November 10, 2026 and the $2.25B May/June 2027 debt maturities.
4. **Rendered and visually audited a 21-page slide deck**, including automated layout linting and a visual review pass.

---

## Interesting Findings: Why the Agent Called a Short (and the 0.22 Beta Puzzle)

One of the most interesting results of this autonomous run was its conclusion: **it initiated Costco as a Short** ($617.94 Base blended target vs. the $1,106.21 cum-$30 special-dividend close on 2026-11-13, while Street consensus averaged $1,081).

### 1. Zero Prestige Bias or Reverse-Engineering

Financial analysis by LLMs typically fails in two ways: **prestige bias** (Costco is a beloved compounder, so it must be a Buy) and **reverse-engineering** (tuning WACC, terminal growth g, or exit multiples until the DCF lands near the current stock price).

Here, the agent's **operating forecast was not bearish at all**:

- **Aligned with Street consensus on operations**: Base Case FY27E revenue ($328.9B) and EBITDA ($15.70B) tracked Street consensus ($327.9B revenue, $15.72B EBITDA—a -0.1% delta). Over the 10-year forecast (FY27E–FY36E), the agent modeled steady compounding (~28–30 net new clubs/year, revenue growing from $303.2B to $551.1B, and ROIC expanding from 31.0% to 47.3%).
- **The Short came strictly from cash-yield and discount-rate math in a 5.15% 10-Year Treasury regime**:
  - At $1,106.21 ($1,076.21 ex-$30 special dividend; $476.5B ex-operating-lease EV), Costco's FY27E unlevered FCFF yield is **1.88%** (consensus FCF yield **2.18%**)—more than 300 bps below the risk-free **5.15% 10Y US Treasury** (2.91% 10Y TIPS real yield).
  - Even after 10 years of compounding, Base FY36E FCFF divided by _today's_ enterprise value reaches only **3.67%** (0 of 10 forecast years clear the 5.15% risk-free rate).
  - Anchoring WACC on Costco's 5-year monthly equity beta (0.85), Rf = 5.15%, and ERP = 4.50% gives Ke = 8.98% and WACC = 8.90%. With terminal growth bounded at 3.50% (below the ~3.7% US nominal GDP ceiling = 2.24% 10Y breakeven inflation + ~1.5% real GDP), the model refused to invent margin expansion that violates Costco's ~11% merchandise gross-margin ceiling or plug an impossible 7.45% perpetual growth rate (2x US nominal GDP).

### 2. The 0.22 2-Year Beta Hidden in the Audited Data

Digging into the repository reveals an even more fascinating detail: **the agent actually computed and ingested a 2-year weekly beta of 0.22 for Costco**, which sits in `data/audited/doc_equity_quotes_2026_11_13.json`:

- `beta_5y_monthly`: COST = 0.85 (WMT = 0.53, PSMT = 0.81, TGT = 0.97, KR = 0.34, BJ = 0.14)
- `beta_2y_weekly`: **COST = 0.22** (WMT = 0.48, PSMT = 0.42, TGT = 0.55, KR = -0.35, BJ = -0.48)

During modeling, however, `models/kg.ts` and `models/valuation/wacc.ts` bound `WaccSchedule` exclusively to the through-the-cycle 5-year monthly beta (0.85).

If you evaluate the exact same compiled `f8-engine` model (`models/index.ts`) replacing 0.85 with Walmart's 5Y beta (0.53) or Costco's 2Y weekly beta (0.22)—holding all operating cash flows, Rf = 5.15%, and terminal parameters constant—here is what happens:

| Beta Basis                           | Scenario | Cost of Equity (Ke) | Blended WACC | Terminal g | WACC - g Spread | Perp DCF (50%) | Exit DCF (25%) | Comps (25%) | **Blended Target (Cum-Div)** |
| :----------------------------------- | :------- | ------------------: | -----------: | ---------: | --------------: | -------------: | -------------: | ----------: | ---------------------------: |
| **0.85 (5Y Monthly — Model Used)**   | Base     |               8.98% |        8.90% |      3.50% |           5.40% |        $421.87 |        $742.98 |     $885.06 |                  **$617.94** |
| **0.85 (5Y Monthly — Model Used)**   | Bull     |               8.76% |        8.70% |      3.80% |           4.90% |        $578.59 |      $1,016.73 |   $1,023.94 |                  **$799.46** |
| **0.53 (WMT 5Y Benchmark)**          | Base     |               7.54% |        7.49% |      3.50% |           3.99% |        $564.69 |        $830.30 |     $885.06 |                  **$711.18** |
| **0.53 (WMT 5Y Benchmark)**          | Bull     |               7.40% |        7.36% |      3.80% |           3.56% |        $794.61 |      $1,132.47 |   $1,023.94 |                  **$936.41** |
| **0.22 (2Y Weekly — In Audited Data)** | Base   |               6.14% |        6.11% |      3.50% |           2.61% |    **$852.16** |    **$927.18** |     $885.06 |                  **$879.14** |
| **0.22 (2Y Weekly — In Audited Data)** | Bull   |               6.08% |        6.06% |      3.80% |           2.26% |  **$1,250.55** |  **$1,260.02** |   $1,023.94 |                **$1,196.26** |

At Beta = 0.22, the Gordon Growth denominator (WACC - g) cuts in half (from 5.40% to 2.61% in Base, and from 4.90% to 2.26% in Bull). That shifts the Base–Bull Blended valuation range to **$879.14 – $1,196.26** (midpoint **$1,037.70**, and Perpetuity DCF midpoint **$1,051.36**)—landing right on top of the **$1,081 Street consensus price target** and the **$1,076.21 ex-dividend ($1,106.21 cum-dividend) market price**.

### 3. Macro Regime vs. Fundamental DCF: What the Market Is Actually Pricing

This contrast exposes the real investment debate around Costco:

- **It is a discount-rate regime shift, not a cash-flow disagreement**: Because the agent held WACC = 8.90% (5Y Beta = 0.85) fixed in its Reverse DCF, it concluded that the market at $1,106 was pricing in **7.45% perpetual growth** (2.0x US nominal GDP). In reality, the market agrees on Costco's cash flows (~3.5–3.8% terminal growth) but is pricing Costco at a **~6.1% safe-haven discount rate (2Y Beta = 0.22)**.
- **Why 2Y Beta collapsed across defensive retail**: Against an AI/mega-cap tech-concentrated S&P 500, tariff and policy volatility (visible in Q4 FY26 IEEPA tariff refunds), and higher-for-longer rates, investors treated Costco's 92.3% US/Canada membership renewal annuity and price leadership as an inflation-protected safe haven (an equity bond proxy)—amplified near-term by positioning ahead of the $30.00/sh ($13.3B) special cash dividend declared on November 10, 2026.
- **Two-sided analytical lesson (avoiding directional bias)**:
  - _Why blindly adopting 0.22 creates a false Long/Consensus bias_: Plugging a 0.22 short-window beta into a perpetuity DCF assumes a retailer's cost of equity is 6.14% forever—barely 99 bps over the 10Y Treasury (5.15%) and 35 bps over Costco's own marginal pre-tax AA debt cost (5.79%). That extrapolates a cyclical flight-to-safety regime into eternity.
  - _Why ignoring 0.22 creates a mechanical textbook-DCF Short bias_: Ignoring the 0.22 2-year beta mistakes a macro safe-haven bid for an operating growth delusion. When a valuation premium is driven by discount-rate compression and event positioning rather than extravagant consensus EPS estimates, an ordinary in-line earnings print will not automatically trigger a de-rating—forcing the analyst to weigh whether an upcoming catalyst will actually break the safe-haven regime, or whether a neutral (Market-Perform / No View) posture is warranted while that regime persists.

---

## Time, Tokens, and Cost

The entire run executed on **Gemini 4 Hydrogen (XHigh)** across **1 Lead Orchestrator** and **21 Subagents** (22 sessions total):

- **Wall-Clock Time**: **3 hours, 58 minutes, 30 seconds** (01:07:58 to 05:06:28 CST).
- **Cumulative Agent Compute Time**: **11 hours, 7 minutes, 26 seconds** (3h 58m lead orchestrator + 7h 09m across the 21 subagents, since Stage 1 acquisition and Stage 2 ingestion fanned out 4 subagents in parallel).
- **Total Steps & API Calls**: **3,999 trajectory steps** and **1,377 LLM API calls**.
- **Total Tokens Processed**: **318,718,528 tokens (~318.7M)**, with a **93.76% implicit context cache hit rate**.

### Token & Cost Breakdown (Gemini 4 Hydrogen Standard API Rates)

At standard API rates for `gemini-4-hydrogen` (**$1.50 / 1M** uncached input, **$0.075 / 1M** cached input, **$7.50 / 1M** output including thinking tokens), the entire 4-hour engagement cost **$70.28**:

| Token Category                         |     Token Count | Rate (per 1M) |  Lead Orchestrator |       21 Subagents |  **Total Cost** |
| :------------------------------------- | --------------: | ------------: | -----------------: | -----------------: | --------------: |
| **Uncached Input (`input_tokens`)**    |      19,745,996 |         $1.50 |             $10.08 |             $19.54 |      **$29.62** |
| **Cached Input (`cache_read_tokens`)** |     296,515,610 |        $0.075 |              $3.15 |             $19.09 |      **$22.24** |
| **Output (`output_tokens`)**           |       2,456,922 |         $7.50 |              $2.09 |             $16.33 |      **$18.43** |
| ├─ _Internal Thinking Tokens_          |     _1,124,298_ |       _$7.50_ |            _$1.32_ |            _$7.11_ |  _**(~$8.43)**_ |
| └─ _Response & Tool-Call Tokens_       |     _1,332,624_ |       _$7.50_ |            _$0.77_ |            _$9.23_ | _**(~$10.00)**_ |
| **Total**                              | **318,718,528** |             — | **$15.32** _(22%)_ | **$54.96** _(78%)_ |      **$70.28** |

### Telemetry by Workflow Stage

| Stage                                                  | Subagents | Wall Window (CST) | LLM Calls | Total Tokens (In + Cache + Out) |  Est. Cost |
| :----------------------------------------------------- | --------: | :---------------- | --------: | ------------------------------: | ---------: |
| **Lead Orchestrator (Across All Stages)**              |         1 | 01:07 – 05:06     |       176 |                          48.97M |     $15.32 |
| **Stage 1: Parallel Source Acquisition**               |         4 | 01:11 – 01:26     |        81 |                           8.35M |      $2.15 |
| **Stage 2: Audited Ingestion, Review & Remediation**   |         8 | 01:31 – 02:59     |       683 |                         139.96M |     $26.15 |
| **Stage 4: 3-Statement + DCF Model & Review Loop**     |         4 | 03:09 – 04:14     |       242 |                          71.72M |     $13.92 |
| **Stage 5: Pitchbook Generation & Visual Review Loop** |         5 | 04:19 – 05:05     |       195 |                          49.72M |     $12.74 |
| **Total**                                              |    **22** | **3h 58m**        | **1,377** |                     **318.72M** | **$70.28** |

> _Disclaimer: This write-up and the accompanying deliverables are AI-generated technical demonstrations of autonomous financial modeling with [`f8-engine`](https://github.com/mhcfa/f8) and do not constitute financial analysis or investment advice._
