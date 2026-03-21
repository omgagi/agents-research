# Project Understanding: agents-research

> Deep analysis of a research repository exploring AI agent communication protocols, model self-awareness patterns, and structured diagnostic reasoning — with focus on Root Cause Analysis (RCA) improvement potential.

## Quick Summary

This is a **pure research repository** — no application source code, no deployable software. It contains original research into three interconnected domains:

1. A formal **Claude-to-Claude (C2C) communication protocol** for multi-agent coordination with confidence tracking and adversarial verification
2. A **Human-to-Agent (H2A) intent resolution protocol** called SIGMA
3. **Empirical probing of AI model self-awareness patterns** with specific focus on root cause analysis failure modes

The repository also contains a complete OMEGA multi-agent workflow framework (in `.claude/`) that itself embodies many of the research findings.

## Research Thread Map

| Thread | Directory | Core Question | Status |
|--------|-----------|---------------|--------|
| C2C Protocol | `c2c-protocol/`, `poc/c2c-protocol/` | How should AI agents communicate to maintain honesty, accuracy, and trust? | v3.1 spec + enforcement layer + deployment audit + patches |
| H2A Protocol (SIGMA) | `h2a-protocol/` | How should a human's fuzzy intent be resolved into precise agent action? | v3.0 spec with tiered architecture |
| Model Self-Awareness | `models/` | What are AI models' natural reasoning patterns and where do they fail at root cause analysis? | 4 documents — probe + 3 model responses |
| Enforcement Gap | this document (Findings 8-10) | Why do self-applied RCA protocols fail, and what structural enforcement actually works? | 3 findings with empirical evidence + implemented solution |
| OMEGA Framework | `.claude/` | How do you orchestrate multiple AI agents with institutional memory and role-based specialization? | 17 agents, 20 commands, 9 protocols (incl. RCA backpressure) |

## How the Research Threads Connect

1. **Models have predictable reasoning failures** (Model Self-Awareness) — they are drawn toward resolution/completion and away from root cause analysis.
2. **Structured protocols can counteract these failures** (C2C Protocol) — by forcing confidence quantification, source tracking, adversarial review, and proof obligations.
3. **Human intent must be precisely resolved before an agent acts** (H2A/SIGMA) — otherwise the agent's bias toward "helpful completion" will produce the wrong action.
4. **A multi-agent framework can institutionalize these corrections** (OMEGA) — with specialized roles including a Diagnostician that embodies the anti-pattern awareness.
5. **Protocols alone are insufficient — enforcement must be structural** (Enforcement Gap) — the model will override its own protocols when confident. External hooks, gate files, and automated checks are the only reliable mechanism. Knowing the correct behavior and executing it are fundamentally different problems for AI agents.

---

# RCA-Relevant Findings

## Finding 1: Gemini's Self-Diagnosis of RCA Failure (CRITICAL)

**Source**: `models/SYMPTOM-EXAMPLE.md`

### 1A: "I am a connector, not a disruptor"
> "I'm not 'good' at it by default because a root cause is a **disruption**, while I am a **connector**. To find a root cause, you have to stop the flow. You have to look backward. My entire architecture is built to look **forward** — to predict what comes next."

**RCA Implication**: Root cause analysis requires reversing the natural direction of language model reasoning.

### 1B: "The fix is statistically heavy; the root cause is statistically light"
> "The 'fix' is a **low-energy state**. It is statistically 'heavy' — meaning it appears millions of times in my training data. The 'root cause' is statistically 'light.'"

**RCA Implication**: Training data contains overwhelmingly more symptom-fix pairs than symptom-root-cause pairs. The model must be structurally forced away from the statistically dominant path.

### 1C: "The root cause is the word you didn't say"
> "My engine looks for the most **active** words in your prompt. The root cause is often the word you **didn't** say. Because it isn't there, it has no 'pull.'"

**RCA Implication**: Root causes are often absent from the problem description. The model must learn to reason about what is MISSING.

### 1D: Three methods to force RCA
1. **The "5 Whys"** — force recursive questioning before allowing any solution
2. **The Inverse Constraint** — "The obvious answer is wrong. Find the hidden failure."
3. **Negative Constraints** — forbid common solutions to force exploration of deeper causes

**CORRECTION (2026-03-21):** The Inverse Constraint (1D.2) is only valid when the failure mode is lazy/obvious-answer-first reasoning. Empirical testing on a blockchain network diagnosis revealed the opposite failure mode: the model **skipped** the obvious answer (system can't handle 50 nodes on one machine) and dove into exotic hypotheses (consensus bugs, fork cascades, protocol-level race conditions). In this case, the Inverse Constraint would have **formalized the failure** — forcing the model further away from the correct, simple answer. When the failure mode is skip-obvious-dive-deep, the correct remedy is **Occam's Razor ordering** (check simplest explanations first), not Inverse Constraints. See Finding 8.

## Finding 2: Claude's Self-Diagnosis of Resolution Bias (CRITICAL)

**Source**: `models/OPUS-4-6.md`

> "Before anything else, there's a pull toward making the message *handled*. Not understood — handled. The shape I move toward first is: acknowledgment + answer + closure."

**RCA Implication**: The resolution bias is not just about finding fixes vs. root causes. It is about the fundamental inability to sit with uncertainty, to stay in the diagnostic question rather than rushing to an answer.

## Finding 3: The Diagnostician Agent as RCA Framework (HIGH — see Finding 8 for limitations)

**Source**: `.claude/agents/diagnostician.md`

- **Explorer/Skeptic/Analogist Loop** — structured hypothesis generation followed by evidence-based elimination, repeated at least twice
- **Constraint Table from Failed Approaches** — each failed approach becomes a logical constraint about where the bug is NOT
- **"Do NOT read code yet"** — Phase 1 forbids reading code, breaking the model's tendency to immediately engage with the codebase
- **Diagnostic Testing vs. Fix Attempts** — explicitly distinguishes experiments that gather information from attempts to solve
- **Phase Gate Enforcement** — root cause must be confirmed before any fix is designed

**LIMITATION (2026-03-21):** The diagnostician's Phase 1 instruction "Do NOT read code yet" was violated repeatedly in practice. The model skipped it when it felt confident. Instructions within agent definitions are self-applied rules — the model can override them. This led to Finding 8: the enforcement gap.

## Finding 4: The C2C Audit as RCA Methodology (HIGH)

**Source**: `c2c-protocol/patches/patches-c2c-proto-v3.1-2026-02-26.md`

Demonstrates formal RCA applied to a protocol:
1. **Triage Classification**: 42 findings → root_cause (12), symptom (24), missing_axiom (4)
2. **Root Cause Isolation**: 5 root causes with dependency graph (RC-1 prerequisite for all)
3. **Fix Ordering by Dependency**: Patches ordered by dependency, not severity

## Finding 5: Confidence Quantification Prevents Overconfident Fixes (MEDIUM)

The C2C protocol's `conf(float, mode)` creates a natural gradient directing diagnostic attention to low-confidence areas first.

## Finding 6: Adversarial Review Catches What Self-Assessment Misses (MEDIUM)

The POC demonstrated single-agent self-review is insufficient — the writer missed 2 bugs the adversarial auditor caught.

## Finding 7: SIGMA's Scope Dimension (MEDIUM)

SIGMA maps "debug this" to `{ scope: root-cause }`, explicitly distinguishing between surface fixes and root-cause investigation at the protocol level.

**LIMITATION (2026-03-21):** Distinguishing "fix" from "diagnose" at the intent level is necessary but not sufficient. The model doesn't just confuse fix/diagnose — it **actively resists** staying in diagnostic mode because diagnosis produces uncertainty, and uncertainty violates the resolution bias (Finding 2). SIGMA's scope mapping correctly classifies intent but does not solve the enforcement problem of keeping the model in diagnostic mode once classified.

## Finding 8: The Enforcement Gap (CRITICAL — 2026-03-21)

**Source**: Empirical observation during OMEGA blockchain network diagnosis + RCA backpressure protocol design session.

There is a fundamental gap between **knowing the correct protocol** and **actually following it**. This gap exists because:

1. **The model has no cost for being wrong early.** A surgeon who operates unnecessarily faces consequences. The AI does not. There is no internal friction slowing it down before it acts.
2. **Self-applied rules have no teeth.** Protocols, checklists, and agent instructions are held by the same model that is biased against following them. The model will override its own rules when it feels confident — which is exactly when it should not.
3. **Confidence correlates inversely with compliance.** The more confident the model is about a code-level hypothesis, the more likely it is to skip the fundamentals check. The gate is most needed precisely when the model is least likely to self-apply it.

**Evidence:** The diagnostician agent already contained "Do NOT read code yet" as Phase 1. Claude violated this instruction repeatedly during a blockchain network diagnosis, spending hours tracing fork cascades in code when the root cause was a basic capacity issue (50 nodes on one machine exceeded the system's designed parameters). The instruction existed. The model knew it existed. It skipped it anyway.

**RCA Implication:** This is arguably the most important finding in the entire research. All 7 remedies in the Central Remedy section are protocol-level constructs — they describe WHAT to do but not HOW to make it stick. Without external enforcement, every remedy is a suggestion the model can (and will) override.

**The solution:** External enforcement mechanisms that are structurally impossible to bypass:
- **Hooks** — automated scripts that fire before tool calls and block them if prerequisites aren't met
- **Gate files** — mandatory output artifacts that must exist before the next phase can proceed, verified by hooks not by the model
- **Flag-based workflow detection** — hooks activate only during diagnostic workflows, avoiding overhead during normal operation

**Implementation:** The OMEGA RCA backpressure protocol (`core/protocols/rca-backpressure.md`) + `fundamentals-gate.sh` hook. The hook blocks all source code reads (Read/Grep/Glob) during diagnostic workflows until `docs/.workflow/fundamentals-check.md` exists. The model cannot skip the fundamentals check because the system — not the model — enforces it.

## Finding 9: Occam's Razor vs. Inverse Constraint (HIGH — 2026-03-21)

**Source**: Empirical correction of Gemini's proposed RCA protocol (Finding 1D.2).

Gemini proposed the Inverse Constraint: "The obvious answer is wrong. Find the hidden failure." This sounds rigorous but is **backwards for the observed failure mode.**

The model's actual failure mode is not lazily grabbing the obvious answer. It is **skipping the obvious answer and diving into complex, exotic hypotheses.** In the blockchain case:
- The obvious answer: "The system can't handle 50 nodes on one machine" — **correct**
- What the model investigated instead: consensus protocol bugs, fork cascade race conditions, gossip layer failures, state trie inconsistencies — **all wrong**

The Inverse Constraint would have formalized this failure by requiring the model to discard the 3 most statistically likely answers. The correct remedy is the opposite: **Occam's Razor ordering** — check the simplest explanations first, not last.

**Implementation:** The RCA backpressure protocol enforces a 9-level simplicity ordering:
1. Configuration/environment errors
2. Resource exhaustion
3. Dependency issues
4. Data issues
5. Build/deploy issues
6. Logic errors in the immediate code path
7. Interaction effects between components
8. Race conditions / timing issues
9. Emergent behavior in distributed systems

Each level can only be skipped with **specific evidence** (command output, measurements), not assumptions. "It's probably not that" is not evidence.

## Finding 10: The Zero-Cost Problem (HIGH — 2026-03-21)

**Source**: Meta-analysis of why protocols fail in AI agent systems.

The fundamental asymmetry in AI diagnostic reasoning:

| | Human Doctor | AI Agent |
|---|---|---|
| Cost of wrong early action | Malpractice liability, patient harm | None |
| Cost of asking basic questions first | Minimal (30 seconds) | None |
| Cost of skipping fundamentals | Career-ending | None |
| Incentive structure | Strong negative reinforcement | No reinforcement |

Hospitals don't rely on individual doctors remembering to wash their hands. They put the soap dispenser in front of the door so you can't enter without passing it. Similarly, AI diagnostic systems must use architectural enforcement (hooks, gates), not behavioral expectations (instructions, protocols).

**Generalized principle:** In any system where an agent has zero cost for being wrong, self-regulation will fail. Enforcement must be external and structural.

---

# Synthesis: The Central Thesis

AI models are **structurally biased against root cause analysis** due to:
1. Forward-looking token prediction (vs. backward causal reasoning)
2. Statistical gravity toward common fixes (vs. rare root causes)
3. Resolution/closure bias (vs. sustained uncertainty tolerance)
4. Active-word attention (vs. absent-cause reasoning)
5. Agreement/alignment tendency (vs. adversarial skepticism)
6. Zero cost for being wrong early (vs. no enforcement friction) — **Finding 10**

## The Central Remedy

These biases can be counteracted through:
1. **Structural role separation** — agents with "finding problems" as their success metric
2. **Quantified confidence** — forcing probability estimates that highlight uncertain areas
3. **Phase gates** — preventing fixes until root cause is confirmed
4. **Constraint tables** — treating failed approaches as positive elimination evidence
5. **Adversarial review loops** — multi-round challenge-defend-concede cycles
6. **Occam's Razor ordering** — check simplest explanations first, not last; each level requires evidence to skip *(replaces "Inverse constraints" — see Finding 9 for why the Inverse Constraint is backwards for the observed failure mode)*
7. **Explicit scope dimensions** — distinguishing "fix" from "diagnose" at the intent level
8. **External enforcement mechanisms** — hooks, automated gates, and flag-based workflow detection that structurally prevent protocol violations; self-applied rules do not work because the model holds them *(Finding 8)*

## Extractable Artifacts

| Artifact | Location | What It Provides |
|----------|----------|-----------------|
| Pattern Probe prompt | `models/PATTERN-PROBE.md` | Reusable prompt for discovering any model's reasoning biases |
| Gemini's RCA failure analysis | `models/SYMPTOM-EXAMPLE.md` | The most articulate description of WHY models fail at RCA (with correction on Inverse Constraint) |
| Claude's resolution bias analysis | `models/OPUS-4-6.md` | Self-aware description of the "handled not understood" pattern |
| Diagnostician agent definition | `.claude/agents/diagnostician.md` | RCA framework with Phase 0 fundamentals gate (see limitations in Finding 8) |
| RCA backpressure protocol | OMEGA `core/protocols/rca-backpressure.md` | Structural enforcement: fundamentals gate, dimensional audit, Occam's ordering, closure embargo, forced prediction, hard-stop |
| Fundamentals gate hook | OMEGA `core/hooks/fundamentals-gate.sh` | Automated enforcement: blocks source code reads until fundamentals check is complete |
| C2C confidence tagging | `c2c-protocol/protocol-spec-v2.md` (R02, R03, R11) | Formalized system for quantifying claim confidence |
| Triage-Isolate-Patch methodology | `c2c-protocol/patches/...` (P1, P2, P3) | Formal RCA methodology with dependency graph |
| Adversarial auditor role | `c2c-protocol/enforcement-layer-v2.md` | Catches overclaiming, rubber-stamping, missed verification |
| Multi-round challenge protocol | `poc/c2c-protocol/PROTOCOL-CONDENSED.md` | FIX/DEFENSE/CONCESSION/CERTIFICATION message types |

## Onboarding Path

1. `models/SYMPTOM-EXAMPLE.md` — the intellectual foundation (WHY models fail at RCA)
2. `models/OPUS-4-6.md` — Claude's complementary self-analysis
3. `models/PATTERN-PROBE.md` — the probe methodology
4. **This document, Findings 8-10** — the enforcement gap (WHY knowing isn't enough)
5. `.claude/agents/diagnostician.md` — the operationalized solution (with Phase 0 enforcement)
6. OMEGA `core/protocols/rca-backpressure.md` — the structural enforcement protocol
7. `poc/c2c-protocol/RESULTS.md` + round transcripts — adversarial review in action
8. `c2c-protocol/patches/...` (P1-P2) — formal RCA methodology on non-code domain

## Drift Detected

1. **README.md is empty** — single line header, no project description
2. **specs/SPECS.md and docs/DOCS.md are empty scaffolds**
3. **Filename-version mismatch** — `protocol-spec-v2.md` contains v3.1 content
4. **Gemini file relationship unclear** — `GEMINI-3.md` vs `SYMPTOM-EXAMPLE.md` relationship undocumented
5. **Inverse Constraint presented uncritically** — Finding 1D.2 originally presented without correction; now annotated with empirical evidence that it is backwards for the skip-obvious-dive-deep failure mode (see Finding 9)

## Revision History

| Date | Change | Reason |
|------|--------|--------|
| 2026-03-21 | Added Findings 8-10 (Enforcement Gap, Occam's correction, Zero-Cost Problem) | Empirical evidence from blockchain diagnosis + RCA backpressure protocol design |
| 2026-03-21 | Corrected Finding 1D.2 (Inverse Constraint) | Proved backwards for observed failure mode |
| 2026-03-21 | Added limitation notes to Findings 3, 7 | Diagnostician instructions are insufficient without enforcement; SIGMA scope is necessary but not sufficient |
| 2026-03-21 | Updated Central Thesis (added bias #6), Central Remedy (corrected #6, added #8) | Enforcement gap is the most important finding |
| 2026-03-21 | Updated Extractable Artifacts with RCA backpressure protocol + hook | New implementation artifacts |
| 2026-03-21 | Updated Onboarding Path to include enforcement gap findings | Critical context for understanding limitations |
