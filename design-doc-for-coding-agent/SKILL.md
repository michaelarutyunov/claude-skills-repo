---
name: design-doc-for-coding-agents
description: Create technical design documents for software implementation with AI coding agents. Focuses on system architecture, code structure, components, and data flow - NOT on which statistical methods to use or validation strategy. Use this for implementation planning, not research methodology.
---

## Trigger Patterns

**Use this skill when user asks:**
- How should I structure my code/classes/modules?
- What's the architecture for this system?
- Design a data pipeline/processing system
- How do I organize this codebase?
- Plan the implementation of [feature/system]

**Do NOT use this skill when user asks:**
- Which method/model should I use?
- How do I validate my results?
- What metrics should I calculate?
- Compare these methodological approaches
→ Use analytical-plan-for-ml-research skill instead

**Boundary cases:**
- "Build a forecasting system" → Start with analytical plan (which model?), then design doc (how to implement?)
- "Implement Method X" → Design doc only (method already chosen)

## When to Use This Skill

**Creation Mode:**
- Planning implementation of a chosen methodology
- Structuring code for production systems
- Designing data pipelines or processing workflows
- Preparing specifications for Claude Code or other coding agents

**Evaluation Mode:**
- Reviewing existing design documents for completeness
- Debugging why coding agents misunderstand specifications
- Improving design doc quality before implementation

**Not for:**
- Choosing which algorithm/method to use (use analytical plan)
- Designing validation strategies (use analytical plan)
- Selecting metrics (use analytical plan)

## Core Principles

Design documents for coding agents must:
1. **Separate design from implementation** - Describe WHAT components do, not HOW they're coded
2. **Provide architectural rationale** - Explain WHY this structure over alternatives
3. **Specify boundaries explicitly** - Clear component responsibilities and interfaces
4. **Define error handling** - Expected failures and recovery strategies
5. **Maintain consistent abstraction** - Don't mix high-level architecture with low-level details

## Document Structure

### Section 1: Problem Statement & Context

**What problem does this solve?**
- User need or business requirement
- Current pain point or limitation

**What are the constraints?**
- Performance requirements (speed, memory, scale)
- Environment constraints (local, cloud, embedded)
- Budget or resource limits
- Integration requirements

**What is non-negotiable vs. flexible?**
- Must-have features vs. nice-to-have
- Fixed constraints vs. optimizable parameters

### Section 2: Requirements

**Functional Requirements:**
- Specific capabilities the system must provide
- Expected inputs (format, source, frequency)
- Required outputs (format, destination, frequency)
- User interactions or APIs to expose

**Non-Functional Requirements:**
- Performance targets (latency, throughput)
- Reliability requirements (uptime, error tolerance)
- Maintainability needs (logging, monitoring, debugging)
- Scalability considerations

**Dependencies:**
- External systems or services
- Required libraries or frameworks
- Data sources

### Section 3: System Architecture

**High-Level Components:**
List 5-10 major components with:
- Component name and purpose
- Primary responsibility (single sentence)
- Inputs and outputs
- Dependencies on other components

**Data Flow:**
- Main data path from input to output
- Transformation steps
- Decision points or branching logic
- State management (if applicable)

**Component Diagram:**
Optional ASCII diagram showing relationships:
```
Input → Preprocessor → Validator → Core Logic → Formatter → Output
              ↓                         ↓
           Cache                    Logger
```

### Section 4: Design Decisions & Rationale

For EACH major architectural decision:

**Decision:** What was chosen (e.g., "Use event-driven architecture")

**Rationale:** Why this over alternatives
- What problem does this solve?
- What alternatives were considered?

**Tradeoffs:**
- What was gained (e.g., flexibility, scalability)
- What was sacrificed (e.g., simplicity, latency)

**Constraints:**
- What factors limited the choice?
- What assumptions does this rely on?

**Example for Time Series Projects:**
```
Decision: Store intermediate results in Parquet files
Rationale: Need reproducibility and ability to restart pipeline at any stage
Alternatives: Keep in memory (not persistent), use database (overkill)
Tradeoffs: Gained restart capability, sacrificed some speed
Constraints: Disk space available, data fits columnar format
```

### Section 5: Component Specifications

For each major component:

**Purpose:** What this component does (one sentence)

**Interface:**
- Input schema/format
- Output schema/format
- Configuration parameters

**Behavior:**
- Step-by-step processing logic (conceptual, not code)
- Key decision points
- State changes (if stateful)

**Error Handling:**
- Expected failure modes
- How errors are surfaced
- Recovery or fallback strategy

**Example:**
```
Component: DataValidator

Purpose: Verify input data meets quality requirements before processing

Interface:
- Input: DataFrame with columns [timestamp, value, metadata]
- Output: (is_valid: bool, error_messages: list)
- Config: {max_missing_pct: 0.1, min_rows: 100}

Behavior:
1. Check for required columns
2. Verify timestamp ordering and no duplicates
3. Count missing values, compare to threshold
4. Check minimum row count
5. Return validation result and specific issues found

Error Handling:
- Invalid schema → reject immediately, return descriptive error
- Quality issues → return warnings, let caller decide whether to proceed
- Unexpected data types → attempt coercion, log warning if fails
```

### Section 6: Algorithms & Logic

Describe key algorithms **conceptually** (not as code):

**For each algorithm:**
- **Purpose:** What it computes
- **Approach:** General strategy (e.g., "iterative refinement", "sliding window")
- **Steps:** High-level sequence (3-10 steps)
- **Edge cases:** Boundary conditions and how they're handled
- **Complexity:** Time/space considerations if relevant

**DO describe:**
- The algorithmic strategy
- Key decision points in the logic
- Numerical or statistical considerations
- Stopping conditions or convergence criteria

**DO NOT describe:**
- Function signatures or variable names
- Specific syntax or code structure
- Line-by-line pseudocode

**Example:**
```
Algorithm: Outlier Detection

Purpose: Identify anomalous values in time series

Approach: Rolling median with dynamic threshold

Steps:
1. Calculate rolling median over window (configurable size)
2. Compute rolling MAD (median absolute deviation)
3. For each point, calculate distance from rolling median
4. Flag as outlier if distance > threshold * MAD
5. Return outlier indices and severity scores

Edge cases:
- Start of series (insufficient window) → use expanding window
- All values identical in window → MAD = 0, flag nothing
- NaN values → skip in rolling calculations, flag as missing

Complexity: O(n log w) where w = window size
```

### Section 7: Configuration & Parameters

**List all tunable parameters:**

For each parameter:
- **Name:** Clear descriptive name
- **Purpose:** What it controls
- **Default value:** With justification
- **Range:** Reasonable bounds
- **Impact:** What happens when you increase/decrease

**Example:**
```
overlap_days: int
Purpose: Number of overlapping days between consecutive chunks
Default: 60 (provides ~2 months validation data)
Range: [30, 180] (minimum for stable scaling, maximum for efficiency)
Impact: Higher → more robust stitching, slower processing
        Lower → faster processing, risk of poor alignment
```

### Section 8: Error Handling Strategy

**Expected Failure Modes:**
List likely errors with:
- Cause (what triggers it)
- Detection (how to identify)
- Recovery (what to do)

**Categories:**
- **Input errors:** Invalid data, missing files, wrong format
- **Processing errors:** Numerical issues, convergence failures
- **Resource errors:** Out of memory, disk full, timeout
- **External errors:** API failures, network issues

**Recovery Strategies:**
- **Retry:** For transient failures (network, temporary locks)
- **Fallback:** Use simpler method or cached result
- **Degrade:** Reduce quality to continue (e.g., lower resolution)
- **Abort:** Fail fast with clear error message

**Example:**
```
Failure: Stitching produces implausible scaling factor (α > 3.0)

Cause: Data quality issue in overlap region (rebase, missing data)
Detection: Check α after each stitch against threshold
Recovery: 
1. Log warning with chunk details
2. Flag chunk for manual review
3. Fall back to naive concatenation for that boundary
4. Continue processing remaining chunks
```

### Section 9: Testing Strategy

**Unit Tests:**
- Test each component in isolation
- Key scenarios for each component
- Edge cases to verify

**Integration Tests:**
- End-to-end workflows
- Component interaction points
- Data flow through full pipeline

**Performance Tests:**
- Benchmarks for critical operations
- Memory usage under typical/large loads
- Scalability tests (if relevant)

**Validation Tests:**
- Output quality checks
- Known-good test cases
- Regression tests

**Example Test Cases:**
```
Unit Test: DataValidator
- Empty DataFrame → reject with clear error
- Valid data → pass validation
- 15% missing (threshold 10%) → fail with count
- Wrong column names → reject with expected vs. actual

Integration Test: Full Pipeline
- Process 3 years of daily data → completes in < 10 minutes
- Generated monthly aggregates match ground truth within MAE < 2.0
- Pipeline recovers from mid-chunk failure → resumes correctly
```

### Section 10: Implementation Notes

**Recommended Libraries/Frameworks:**
- Specific packages with versions (if critical)
- Rationale for each choice

**Known Pitfalls:**
- Common mistakes to avoid
- Subtle edge cases
- Performance traps

**Optimization Opportunities:**
- Where performance matters most
- Potential bottlenecks
- Parallelization possibilities

**References:**
- Relevant documentation links
- Research papers (if implementing novel methods)
- Similar implementations to learn from

## Evaluation Checklist

When evaluating a design document, verify:

### Abstraction Level
- [ ] Components described by purpose, not implementation?
- [ ] Algorithms explained conceptually, not as pseudocode?
- [ ] No mixing of architecture (what) with code details (how)?
- [ ] Data structures described by role, not syntax?

### Architectural Clarity
- [ ] Clear component boundaries and responsibilities?
- [ ] Data flow explicitly mapped?
- [ ] Dependencies identified?
- [ ] Integration points specified?

### Rationale & Justification
- [ ] Major decisions have "why" explanations?
- [ ] Alternatives considered and documented?
- [ ] Tradeoffs explicitly acknowledged?
- [ ] Constraints stated clearly?

### Completeness
- [ ] All major components described?
- [ ] Error handling for each failure mode?
- [ ] Edge cases identified and handled?
- [ ] Testing strategy covers critical paths?

### Actionability for Coding Agents
- [ ] Unambiguous about what's fixed vs. flexible?
- [ ] Clear success criteria for implementation?
- [ ] Explicit about error conditions?
- [ ] No unstated assumptions?

### Domain-Specific (Time Series/Data Science)
- [ ] Data quality assumptions stated?
- [ ] Numerical stability considerations noted?
- [ ] Statefulness/reproducibility addressed?
- [ ] Parameter sensitivity acknowledged?

## Output Format

**For Creation:**
Generate structured markdown document following the sections above.
Present as downloadable file (use file_create tool).
Tailor depth based on project complexity:
- Simple scripts: Sections 1-4, 8-9 (lighter detail)
- Complex systems: All sections (full detail)

**For Evaluation:**
Provide structured assessment:

1. **Overall Rating:** 
   - ✅ Ready for implementation
   - ⚠️ Needs improvements (but usable)
   - ❌ Major issues (blocking)

2. **Issues by Category:**
   For each category above, list specific problems found

3. **Prioritized Recommendations:**
   - Critical fixes (blocks implementation)
   - Important improvements (reduces risk)
   - Nice-to-have enhancements

4. **Example Rewrites:**
   Show 1-2 sections rewritten to demonstrate good practice

## Common Pitfalls to Avoid

1. **Too much implementation detail** - Don't write pseudocode, describe concepts
2. **Missing rationale** - Every "what" needs a "why"
3. **Vague error handling** - "Handle errors appropriately" is not actionable
4. **Implicit assumptions** - State all assumptions explicitly
5. **Mixing abstraction levels** - Keep architecture separate from code details
6. **No testing strategy** - Tests must be planned during design, not after
7. **Methodological decisions** - Don't choose which algorithm here (use analytical plan first)
