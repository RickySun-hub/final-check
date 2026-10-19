---
name: final-check
description: Review whether a task or deliverable is ready for its intended next step, identifying consequential omissions and unsupported completion claims. Use when the user asks for a final check, completion review, full-scope check, or what they may have missed. Not a mandatory gate for routine work.
metadata:
  version: "2.0.0"
---

# Final Check

Determine whether the evidence supports the intended outcome. Focus on omissions that could change the user's decision; do not manufacture findings to justify the review.

## Scope and authority

Recover the goal, requested deliverables, acceptance criteria, latest corrections, and intended next step from the conversation and actual artifacts. Preserve earlier requirements that were not superseded. Ask only when an ambiguity materially changes the review and cannot be resolved from available evidence.

A review request is read-only by default. If the user also authorized fixes, make small, reversible corrections within that scope and verify the affected behavior. Existing authorization remains valid; the skill grants no new permission to send, publish, deploy, spend, access sensitive data, or perform destructive operations. Continue independent authorized work when one part is blocked.

## Evidence-driven review

Choose depth according to consequences, uncertainty, and the cost of being wrong. A text edit may need a single readback; a deployment or research claim may need evidence from several stages. Do not apply every domain check to every task.

- Compare the actual result against the requested outcome, including less visible requirements and final handoff. Inspect the relevant files or state instead of relying only on an earlier assistant summary.
- For consequential claims, identify the supporting artifact, its version or timestamp when relevant, and what it actually proves. Distinguish observed facts, user-provided facts, inferences, and unknowns. An inaccessible source is unknown, not evidence of absence.
- Look for the most plausible consequential failure: a missing prerequisite, a contradicted assumption, a broken boundary case, or a result that cannot be used as intended. Trace it to a specific requirement or concrete consequence before reporting it.
- Close material uncertainty with the smallest permitted check that could change the conclusion. Reuse existing verification when the relevant code, inputs, dependencies, configuration, and environment still match; explain its scope. Rerun only when changes, failures, stale external state, or unresolved risk justify it.
- Check the final handoff where applicable: correct artifact and destination, required content, readable/rendered output, working references, and whether a claimed external action actually occurred. Do not add new deliverables merely to demonstrate thoroughness.

Match claims to evidence: a build does not establish live behavior; a mock does not establish an integration; submission does not establish success; a draft is not a sent message. Prefer specific evidence over confidence scores or claims of exhaustive certainty.

## Findings and decisions

Report only actionable defects or uncertainties that matter to the intended next step. For each, give the affected requirement, supporting evidence or missing evidence, consequence, and smallest remedy. Include exact file locations or source links when available. Clearly label an unverified hypothesis; do not describe it as a reproduced defect.

Separate:

- **Blocking:** a demonstrated defect, missing prerequisite, or unresolved uncertainty that prevents a required outcome or makes the next step unsafe.
- **Non-blocking:** a concrete issue the user can reasonably accept without defeating the required outcome.
- **Optional:** a preference or improvement beyond acceptance criteria; normally omit it unless requested.

Do not turn every untested possibility into a blocker. Conversely, a critical unknown can block readiness even without a demonstrated failure. Explain that distinction using the actual task.

## Stop and report

Stop when requested requirements have been reviewed, material findings are fixed and checked or explicitly reported, and further checks are unlikely to change the decision. Do not repeat whole test suites, reopen settled decisions, delegate automatically, or iterate toward an arbitrary quality score.

Lead with a bounded conclusion: ready for the named next step, ready with stated limitations, or not yet ready. State what was checked and any material limits. If no consequential issue was found, say so directly within the reviewed scope; an empty findings list is valid. Never claim global correctness from a limited review.

Use the user's language and preferred format. A short paragraph is enough for simple work. For larger reviews, a compact findings list with evidence and next actions is useful. Mention skipped checks only when they affect the conclusion; avoid empty sections and generic warnings.

## Domain cues (only when relevant)

- **Code:** actual diff and surrounding behavior, regression scenario, applicable checks, user changes, and local versus deployed state. Lint, types, build, and runtime tests answer different questions; select what is needed.
- **Research/data/HPC:** input/config identity, units and schema, leakage or methodological gaps, job status versus output validity, fresh outputs, and reproducibility of the claimed result. Training metrics alone do not establish a scientific outcome. Expensive reruns need authorization.
- **Applications/decisions:** current authoritative requirements, eligibility, deadlines and timezone, correct document version, and unsupported factual claims. Do not guess sensitive answers.
- **Messages:** intended sender and recipient, factual claims, links/attachments, and draft versus sent state. Reviewing a message does not authorize sending it.
- **Documents/UI:** requested content, canonical file, actual rendering and critical interactions when relevant, readable export, and usable delivery path. Fix acceptance failures; do not prolong review over subjective polish.
