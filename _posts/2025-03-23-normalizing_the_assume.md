---
title: "Normalizing the Assume Constraint: Elevating Requirements-Based Process Flows"
date: 2025-03-23
layout: post
---

In formal verification, we’ve mastered a distinction that keeps complex designs manageable: *assume* statements and *assert* statements. When verifying a design under test (DUT), we write "assume" statements to constrain the input space say, "Assume the AXI slave receives write address signals per the AXI4 protocol" and "assert" statements to verify the DUT’s behavior, like "Assert the slave responds with a valid write acknowledgment." This split is elegant and effective: assumptions define the environment, assertions check the DUT’s actions. It’s a model of clarity, testability, and precision.

Yet, in requirements-based process flows—common across safety-critical industries like aerospace, automotive, and beyond, this separation vanishes. These methodologies, exemplified by standards like DO-254, lean heavily on "requirements" prefixed with "shall," leaving no formal space for assumptions. This creates a blind spot for dependencies, like input protocol constraints, that muddies traceability and verification. It’s time to normalize an "assume" constraint class in requirements-driven processes, drawing inspiration from formal verification, to make our specifications sharper, more testable, and better aligned with real-world design.

## The Requirements Trap: “Shall” Can’t Do It All

Requirements-based flows aim to ensure a system meets its goals through clear, verifiable, and traceable statements. Take DO-254, the aerospace hardware design standard, as a case study: it demands that every requirement be unambiguous and testable, typically with a "shall." For example:

- "The AXI slave device shall process write transactions within 5 clock cycles."
- "The master shall provide AXI4-compliant write address signals."

This works for behaviors-active, measurable outcomes we can verify with a testbench or oscilloscope. But what about dependencies? Suppose the AXI slave device only functions correctly if the master adheres to the AXI4 protocol write spec. How do we express that?

The instinct is to write: "The AXI slave shall process write transactions correctly when provided AXI4-compliant write address signals." It’s a requirement, but it’s loose-it says the slave *can* handle AXI4 signals, not that it *requires* them as its operational baseline. Tighten it up: "The AXI slave shall perform all specified functions only if provided AXI4-compliant write address signals." Better, but now it’s a verification mess. How do you test "only if" at the slave level? It blankets every function-write processing, data handling, everything-mapping to all tests without a single, focused procedure. It’s a "shall" in name, but it’s really a precondition in disguise.

This is the flaw in requirements-only flows: dependencies like input protocols don’t fit the "shall" mold. They’re not behaviors the device controls-they’re assumptions about the environment. Forcing them into requirements creates ambiguity and traceability sprawl, undermining the process’s own objectives.

## Lessons from Formal Verification

Formal verification nails this distinction. We write SVA properties that essential equate to:

- *Assume*: "The AXI slave receives write address signals per the AXI4 protocol."
- *Assert*: "The AXI slave processes write transactions within 5 clock cycles."

The "assume" limits the input space-proofs only hold if the AXI protocol is followed. The "assert" checks the slave’s response. If the master violates the assumption (e.g., sends malformed address signals), the slave isn’t blamed-the system is. Here’s the kicker: when we move up to top-level simulation, we flip those submodule assumptions into assertions. That AXI slave assumption becomes "Assert the master provides AXI4-compliant write address signals," verifying the higher-level block delivers what the slave expects. This mirrors hardware design perfectly: the slave assumes AXI4 compliance; the master provides it. The flip parallels linking an assumption to a higher-level requirement-a direct tie that ensures system coherence. This isn’t just a neat trick-it’s a lesson in completeness.

## The Pitch: Normalize "Assume" Constraints

Picture a new class in requirements-based processes: "Assumptions" or "Constraints," defined as "preconditions the design depends on to meet its requirements." For our AXI slave:

- **Constraint C1**: "The AXI slave assumes write address signals comply with the AXI4 protocol."
- **Requirement SLV-001**: "The AXI slave shall process write transactions within 5 clock cycles."
- **Requirement MST-023**: "The master shall provide write address signals per the AXI4 protocol."

Here’s why this shines:

1. **Clarity**: C1 is a dependency, not a behavior. It’s clear it’s a condition the slave relies on, not something it “does”—no more mangling "shall" to fit.
2. **Testability**: Constraints don’t need standalone tests-they shape the test environment. Every slave test says, "Stimulate with AXI4-compliant signals (per C1)," then verifies SLV-001. It’s practical, not a catch-all.
3. **Traceability**: C1 traces up to MST-023, ensuring the master delivers. If the master spec changes (e.g., drops AXI4 compliance), the link flags it-just like flipping an assumption to an assertion at the top level. It informs all slave tests without bogging down every "shall" with redundant conditions.

This isn’t about ditching requirements-it’s about enriching them. Standards like DO-254 demand verifiable, traceable requirements, and this fits: constraints link to higher-level specs (e.g., "System shall use AXI4") and guide testing without masquerading as device actions.

## Is This Allowed?

Absolutely. Requirements flows, including DO-254, don’t ban tracing beyond "shall" statements-they *mandate* requirements traceability but leave room for extras. Document it in your process plan (e.g., a DO-254 PHAC): "We define a ‘Constraints’ class for dependencies, traced to provider requirements and applied in verification." Reviewers care about results-reliable, testable designs-not strict labels. If constraints catch mismatches (like a protocol drift), they bolster safety and compliance.

## Why Make It Standard?

Today, teams hack this with interface docs or side notes, but it’s ad hoc. Normalizing "assume" constraints across requirements-based flows would:

- **Unify Dependency Management**: No more assumptions lost in the shuffle.
- **Streamline Testing**: Test setups mirror design intent explicitly.
- **Tighten Traceability**: Changes upstream (e.g., master spec) ripple to constraints, not just requirements, closing gaps.
- **Boost Completeness**: Like formal verification spotting that AXI alignment gap, constraints force us to ask, "Does the system guarantee this?"-exposing holes before they bite.

## Let’s Normalize It

Requirements-based flows have thrived on "shall," but they’re due for a tweak. Formal verification shows the way: split assumptions from assertions, constraints from requirements-and flip those assumptions into higher-level checks to ensure the system holds up. Let’s normalize an "assume" class-Constraints, Assumptions, call it what you will-and weave it into our methodologies. It’s not a breach of standards like DO-254; it’s an evolution. It’s clearer, more testable, and truer to how we design interconnected systems. Plus, it’s a window into completeness we can’t ignore.

Next spec you write, give it a shot: list your "shall" requirements, then your "assume" constraints. Trace them up, test them in context, and watch the clarity-and robustness-emerge. Your design-and your process-will be stronger for it.
