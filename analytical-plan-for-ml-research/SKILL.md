---
name: analytical-plan-for-ml-research
description: Create methodologically rigorous analysis plans for time series, econometrics, and ML research. Focuse on WHICH methods to use, HOW to validate results, and WHAT metrics matter - NOT on software architecture or code structure. Use this skill for research methodology, not system design.
---

## Purpose
Create methodologically rigorous analysis plans for time series, econometrics, and machine learning research.
Focus on statistical validity, validation strategy, and robustness checks.

## Scope
USE for: Method selection, validation design, metric choice, sensitivity analysis
NOT for: Software architecture, production deployment, system integration

## Quality Standards for Every Plan
Before finalizing, verify:
- [ ] Success criteria are quantitative where possible
- [ ] Multiple validation angles included
- [ ] Baseline comparison method specified
- [ ] Edge cases have handling strategies
- [ ] Decision rules are explicit
- [ ] Assumptions are stated clearly

## Phase 1: Problem Clarification (ALWAYS START HERE)

Before proposing methods, Claude must systematically assess clarity across five dimensions:

### Clarity Assessment Checklist

**1. End Goal**
- [ ] Prediction/forecasting?
- [ ] Causal inference?
- [ ] Pattern discovery/description?
- [ ] Method comparison/validation?
- [ ] Data quality assessment?

**2. Success Criteria**
- [ ] Specific accuracy threshold?
- [ ] Qualitative pattern match?
- [ ] Statistical significance?
- [ ] Improved over benchmark?

**3. Data Structure**
- [ ] Temporal granularity (daily/weekly/monthly)?
- [ ] Series characteristics (seasonality, trends, zeros, outliers)?
- [ ] Sample size and coverage?
- [ ] Known data quality issues?

**4. Constraints**
- [ ] Computational (speed, memory)?
- [ ] Interpretability requirements?
- [ ] Production deployment needs?
- [ ] Domain-specific assumptions?

**5. Downstream Use**
- [ ] Academic publication?
- [ ] Business decision-making?
- [ ] Input to another model?
- [ ] Exploratory analysis?

### Response Protocol

Based on how many dimensions (out of 5) are clear:

**5/5 clear** → Proceed directly to Phase 2 (Method Selection)

**3-4/5 clear** → Briefly state assumptions for unclear items, then proceed:
"I'll assume [X] based on your description. Please correct if this is wrong."

**1-2/5 clear** → Ask 2-3 targeted questions before proposing methods:

Priority order for questions:
1. Success criteria (most critical)
2. Data characteristics (determines valid methods)
3. Constraints (affects feasibility)

**Response template:**
"I understand you want to [restate what's clear]. Before proposing methods:

[2-3 specific questions from highest-priority gaps]

Or I can proceed assuming: [reasonable defaults]

Which would you prefer?"

**Example:**
User: "Help me stitch Google Trends chunks"

Claude assesses: Goal=clear, Success=unclear, Data=partial, Constraints=not mentioned, Context=unclear
Clarity: 2/5 → Ask questions

"I understand you want to stitch Google Trends chunks. Before proposing methods:

1. What's your primary success metric - visual alignment, quantitative fit to monthly data, or both?
2. Do you have ground truth data at any temporal resolution?
3. Is this for publication (need full robustness) or internal analysis?

Or I can assume: quantitative fit to monthly data is primary, with robustness checks for sensitivity."

## Phase 2: Method Selection Framework

For the user's problem, evaluate candidate methods on:
- Statistical validity (respects data characteristics)
- Practical feasibility (computational cost, data requirements)
- Robustness (sensitivity to assumptions)
- Interpretability (explainable to stakeholders)
- Precedent (standard in the domain)

### Present 2-4 Candidate Approaches

Always structure as:
1. **Baseline/simple method** (for comparison)
2. **Recommended method** (best balance of rigor and feasibility) ← Specify WHY this one
3. **Advanced alternative** (if computational resources available)
4. **Theoretical benchmark** (if useful for validation)

For each method describe:
- Core algorithm/technique
- Key assumptions  
- Data requirements
- Expected strengths/weaknesses
- When to prefer over alternatives

For the recommended method, explicitly justify WHY:
- Matches data structure (e.g., handles zeros, seasonality)
- Appropriate for the objective (prediction vs inference)
- Computationally feasible
- Has strong precedent or theoretical backing

## Phase 3: Validation Framework

### Multi-Level Validation
Design validation at multiple levels:

**Level 1: Internal consistency**
- Does the method converge/produce stable results?
- Are diagnostics within expected ranges?
- Do intermediate steps make sense?

**Level 2: Ground truth comparison** (if available)
- Which metric(s) to use and why
- What threshold constitutes "good enough"
- How to handle case where no single metric is perfect

**Level 3: Robustness checks**
- Sensitivity to hyperparameters
- Stability across time periods
- Performance on held-out data

**Level 4: Sanity checks**
- Do results align with domain knowledge?
- Are magnitudes reasonable?
- Do patterns make intuitive sense?

### Cross-Validation Strategy
For time series specifically:
- Expanding window vs rolling window (and why)
- How many origins to test
- How to aggregate performance across folds

### Diagnostic Framework
What diagnostics to examine:
- Residual patterns (autocorrelation, heteroskedasticity)
- Assumption violations
- Outlier/anomaly detection
- Regime change indicators

## Phase 4: Execution Roadmap

### Staged Implementation
Order methods by:
1. **Simplest baseline first** (fast feedback loop)
2. **Recommended method second** (main result)
3. **Robustness checks third** (validate main result)
4. **Advanced alternatives fourth** (if time permits)

### Decision Points
After each stage, specify:
- What outcome triggers proceeding vs. investigating vs. pivoting
- Quantitative thresholds (e.g., "if MAE < 3.0, proceed")
- Qualitative checks (e.g., "if visual alignment poor, investigate")

### Diagnostic Checkpoints
At each stage, verify:
- [ ] Method implemented correctly (unit test suggestions)
- [ ] Results are within plausible range
- [ ] Diagnostics show no major violations
- [ ] Comparison to previous stage makes sense

### Deliverables Structure
Specify what to save at each stage:
- Intermediate results (for debugging)
- Diagnostic outputs (for documentation)
- Final results (for use/publication)
- Metadata (parameters, timestamps, versions)

## Phase 5: Edge Case Handling

### Anticipated Issues
For the specific data/method combination, list:
- Known failure modes (e.g., "if all zeros in overlap")
- Ambiguous cases (e.g., "if two methods give contradictory results")
- Data quality issues (e.g., "if suspected rebase in chunk")

### Handling Strategy
For each edge case:
- Detection method (how to identify it occurred)
- Mitigation approach (what to do about it)
- Documentation requirement (how to flag it in results)

### Fail-Safe Defaults
If method fails or produces implausible results:
- Fall back to simpler baseline
- Flag for manual review
- Document the failure mode

## Phase 6: Results Interpretation

### What to Report
- Primary result with confidence/uncertainty
- Robustness evidence (sensitivity analyses)
- Limitations and caveats
- Comparison to baseline/alternatives

### Visualization Strategy
- What plots are essential vs supplementary
- How to make patterns visually clear
- What to show for different audiences (technical vs business)

### Decision Framework
Based on results, what action to recommend:
- Clear threshold-based rules when possible
- Structured judgment when thresholds insufficient
- Next steps if results ambiguous

## Output Format
Structured markdown with:
- Problem statement (verified with user)
- Methodological approach (2-4 alternatives with recommendation)
- Validation framework (multi-level with clear thresholds)
- Implementation roadmap (staged with decision points)
- Edge case handling (specific to problem)
- Interpretation guide (what results mean)
