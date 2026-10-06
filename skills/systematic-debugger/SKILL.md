---
name: systematic-debugger
description: Scientific, evidence-driven root-cause debugging methodology for Antigravity. Strictly halts guesswork and trial-and-error fixes. Enforces hypothesis generation, minimal reproduction isolation, telemetry instrumentation, subagent red-teaming, and regression prevention. Triggers on "/debug", "debug this", "why is this failing", "investigate bug", "intermittent failure", "root cause analysis", "systematic debug".
---

# Systematic Debugger for Google Antigravity

A disciplined, evidence-driven debugging methodology that replaces guesswork with the scientific method.

---

## The Prime Directive

> **NO FIXES WITHOUT PROVEN ROOT CAUSE FIRST.**
> Never patch symptoms. Never edit source code based on an unverified hunch. If you cannot explain *why* the failure occurs with measurable evidence, you do not understand the problem.

---

## 5-Phase Debugging Workflow

### Phase 1: Problem Clarification & State Capture
1. **Define Expected vs Actual**: Explicitly document what should have happened versus what actually occurred.
2. **Collect Hard Evidence**: Extract exact stack traces, environment variables, dependencies, and relevant log lines.
3. **Capture Telemetry in Artifact**: Create an initial `debug-investigation.md` Antigravity Artifact to record state and avoid forgetting context during deep investigations.

### Phase 2: Competing Hypotheses Formulation
Before modifying or inspecting deep code, formulate **at least 3 competing hypotheses**:
* **Hypothesis A**: Immediate local state/logic anomaly (e.g. race condition, unhandled null).
* **Hypothesis B**: Boundary/Contract mismatch (e.g. API payload schema change, type coercion).
* **Hypothesis C**: Environmental/Configuration discrepancy (e.g. OS path formatting on Windows, timezone/locale, Node/runtime version).

### Phase 3: Minimal Reproduction Isolation (Repro Case)
* Do not debug inside a noisy, monolithic app if possible.
* Construct a minimal, isolated test script (`scratch/repro.*` or a targeted unit test) that reproduces the failure in isolation.
* Verify the test **consistently fails** under the reproduction conditions.

### Phase 4: Instrumentation & Evidence-Driven Isolation
1. **Instrument, Don't Guess**: Add targeted non-destructive logging (`console.error`, `logging.debug`, or conditional breakpoints) along the execution path.
2. **Binary Search the Pipeline**: Narrow down the exact line or state transition where clean data turns corrupt or where execution branches incorrectly.
3. **5-Whys Deep Drill**:
   * *Why did it crash?* -> Nil pointer dereference.
   * *Why was the pointer nil?* -> Fetch returned null.
   * *Why did fetch return null?* -> Upstream timeout after 5000ms.
   * *Why did upstream timeout?* -> Unindexed database query under load.
   * *Target fix:* Fix the query index and add timeout resilience, not just a `?.` null-check!

### Phase 5: Solution, Verification & Multi-Agent Red-Teaming
1. **Implement Root-Cause Fix**: Apply the single minimal change targeting the fundamental cause.
2. **Verify Repro Case**: Re-run the Phase 3 reproduction test and confirm it now passes cleanly.
3. **Antigravity Multi-Agent Red-Teaming**:
   * Use `invoke_subagent` to fan out a **`Breaker` / `Adversarial Critic`** subagent (Model: `pro`).
   * Challenge the subagent: *"Review this fix. Try to break it with extreme boundary values, concurrent race conditions, or unhandled exceptions."*
4. **Permanent Regression Test**: Commit the reproduction test into the permanent test suite so the bug never resurfaces.

---

## Antigravity-Specific Directives

* **Terminal Commands on Windows**:
  * Execute test suites and commands cleanly inside the sandboxed PowerShell environment.
  * Pay attention to path separators (`\` vs `/`) and quoting.
* **Context Preservation**:
  * Store diagnostic findings, benchmark timing, and diffs in Antigravity **Artifacts** (`<appDataDir>/brain/<conversation-id>/`).
* **Subagent Invocation**:
  * Separate the analyzer/fixer (`Builder`) from the adversarial reviewer (`Critic`) to maintain zero confirmation bias.

---

## Trigger Commands

* `/debug [problem description]`
* `debug this: [stack trace or error]`
* `find root cause for [issue]`
