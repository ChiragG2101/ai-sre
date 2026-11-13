# Progress Tracker

The **only** place where project status is kept. Other files (README, work split) hold no status.
Update this file whenever a layer moves, a review runs, or an open item is closed.

_Last updated: 2026-11-13_

## Layers

| # | Layer | Owner | Status | Log | Artifact | Notes |
|---|---|---|---|---|---|---|
| 1 | Problem Discovery | Team | Done — review fixes applied; ChatGPT pass pending (H2) | [log](specs/layer-01-problem-discovery/conversation-log.md) | [A1](specs/layer-01-problem-discovery/artifact-1-product-requirements.html) | Revised after first review (recovery first, permanent fix with approval) |
| 2 | Scope and Constraints | Team | Done — review fixes applied; ChatGPT pass pending (H2) | [log](specs/layer-02-scope-and-constraints/conversation-log.md) | [A2](specs/layer-02-scope-and-constraints/artifact-2-scope-and-constraints.html) | |
| 3 | SRE Fundamentals | Dhanush (from Chirag) | Done — review fixes applied; ChatGPT pass pending (H2) | [log](specs/layer-03-sre-fundamentals/conversation-log.md) | [A3](specs/layer-03-sre-fundamentals/artifact-3-incident-and-evidence-catalog.html) | |
| 4 | Requirements | Dhanush (from Chirag) | Done — ChatGPT pass pending (H2) | [log](specs/layer-04-requirements/conversation-log.md) | [A4](specs/layer-04-requirements/artifact-4-requirements-specification.html) | 34 requirements, 5 provisional milestones |
| 5 | Threat Modeling | Dhanush (from Chirag) | Not started — inputs ready (A3, A4) | | | |
| 6 | System Architecture | Manish | Not started | | | Needs A4, A5 |
| 7 | Data, APIs and Events | Manish | Not started | | | Needs A4, A6 |
| 8 | Workflows and State Machines | Dhanush | Not started — prep allowed (Sections A, D) | | | Needs A6, A7 |
| 9 | Agent Architecture | Dhanush | Not started | | | Needs A3, A7, A8 |
| 10 | Inference and Retrieval | Irfan | Not started | | | Needs A2, A9 |
| 11 | Safe Remediation | Rinisha | Not started | | | Needs A5, A8, A9 |
| 12 | Reliability and Evaluation | All, by section (Dhanush merges) | Not started | | | Needs A3, A4, A10, A11 |
| 13 | Implementation Planning | Team | Not started | | | Needs A1–A12 |

## Reviews

| Date | Scope | By | Result |
|---|---|---|---|
| 2026-11-12 | A1 | Claude (same session) | 4 contradictions, recovery-first change, 11 new unknowns — all applied |
| 2026-11-13 | A1, A2, A3 + logs | Claude subagent (fresh context) | 3 High, 12 Medium, 9 Low — team approved fixes; all applied except H2 (team action) |

## Open findings from the 2026-11-13 review

Proposed fixes and approval boxes: [specs/reviews/2026-11-13-review-fixes.md](specs/reviews/2026-11-13-review-fixes.md). Status: **team approved; fixes applied and verified by a fresh-context subagent on 2026-11-13** (second pass fixed the gaps it found: pipeline-only wording, data-rule wording, 20-minute limit, Incident B rollback ID, owners, M11 log row, Layer 10 note). Only H2 remains (team action).

| ID | Sev | Finding | Status |
|---|---|---|---|
| H1 | High | Config revert cannot work: re-running the pipeline at an old commit pulls the current bad config from the config store. A2 has no "restore config version" write action. | Fixed |
| H2 | High | Worksheet method needs Claude **and** ChatGPT per question; only Claude used, so the "disagreements recorded" check is ticked only by omission. No real conversation links. | Open — check 4 un-ticked in A1–A3; team must run the ChatGPT pass |
| H3 | High | Data-rule scope (does it cover existing external tools?) still open; A2 Figure 1 answers it by drawing everything inside the boundary. | Fixed |
| M1 | Med | A revert through the pipeline would wait for staging + QA at 2 AM; no emergency path defined. | Fixed |
| M2 | Med | Who merges the fix pull request, and does human approval equal PR approval? | Fixed |
| M3 | Med | A3 fixtures clash: A's broken config is v42, and B runs on "v42 unchanged". | Fixed |
| M4 | Med | A3 Incident A alert fires at 4 min, but the incident rule needs > 5 min. | Fixed |
| M5 | Med | A3 Incident C: code version "Unknown" vs "9c8d7e"; "errors spread across instances" not supported by evidence. | Fixed |
| M6 | Med | Layer 3 assumptions not closed (L2-A6 trace context, A1 E1 on-call, A1 A2 alert fields, L2-A1); Incident C contradicts L2-A6 without a note. | Fixed |
| M7 | Med | A2 says loop limits are "given" but has no step limit or cost value (A1 B3). | Fixed |
| M8 | Med | GPU capacity ignores HA copies; a 4th SEV1 can wait ~20 min in the queue, which breaks recovery-first. | Fixed |
| M9 | Med | A3 appendix states platform decisions; Layer 3 must not design the platform — reword as constraints forwarded to L4/L11. | Fixed |
| M10 | Med | Layer 1 log names a model (GLiNER / Jev) — Layer 1 must be technology-free; move to L9/L10 prep notes. | Fixed |
| M11 | Med | Self-approval allowed without rationale or a threat check (L5). | Fixed |
| M12 | Med | "Decision model" means two things (Type/System frameworks in A1; an ML model in A2). | Fixed |
| L1–L9 | Low | Overclaim in A1 1.3; chat shown as an interface in A2 Fig 1; A2 "Production access" mislabelled Fixed; L2-A9 stale; owners not matching work split; some log items missing from artifacts; A3 Q3.15 "matches A1" wording; reference-scenario fix scope; "Reviewed by" blank on all artifacts. | Fixed |

## Open items carried across layers

| Item | Owner | Due |
|---|---|---|
| Is every deployment and config change recorded with time and version, and where? | Manish | Layer 6/7 |
| Exact escalation rubric and gate limits (A1 E3) | Dhanush | Layer 9 |
| Self-hosted model choice, GPU sizing incl. HA copies | Irfan | Layer 10 |
| Approval channel, timeouts, write-tool enforcement, sandbox design | Rinisha | Layer 11 |
| Run the ChatGPT pass on A1–A3 decisions and record disagreements (H2) | Layer owners | Before submission |
| Fill "Reviewed by" and "Date" on A1–A3 | Team | Before submission |
| Rebalance load: Dhanush now owns L3–L5, L8–L9 (236 Qs) | Team | Before Layer 4 |
