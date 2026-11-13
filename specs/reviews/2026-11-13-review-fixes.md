# Review fixes — 2026-11-13

Source: fresh-context reviewer (Claude subagent) on A1, A2, A3 and their logs.
For each finding: tick **Approve** to accept the proposed fix, or write your change under **Your change**. I apply only approved or changed items.

---

## High

### H1 — The config revert cannot work through the defined write path
- **Where:** A2 2.3 "A revert re-runs the pipeline at the last known-good version"; A2 2.1 config "found by Git diff or in the config store"; A3 3.6, A3 appendix.
- **Problem:** If the bad value is in the config store, re-running the pipeline at an old commit pulls the current bad value (v42) again. Incident A (the reference scenario) does not recover.
- **Proposed fix:** Add a third write action in A2 2.3: **restore a previous config version in the config store** (e.g. v42 → v41). It uses the same gate as every write: review by the different model, policy checks, human approval. Its undo path is to restore the newer version. Extend L2-A7: the config store keeps versioned history and can restore a version. Update A2 Figure 2 caption and A1 1.5 "What the platform can do" (revert = code rollback **or** config restore).
- [x] Approve
- **Your change:**

### H2 — Worksheet method not followed: only Claude was used
- **Where:** All three logs ("Agent: C", link "(this session)"); acceptance check 4 ticked in A1–A3.
- **Problem:** Method step 5 requires Claude **and** ChatGPT, separately, for each question. Check 4 is true only because no second agent was asked. There are no real conversation links.
- **Proposed fix:**
  1. Un-tick acceptance check 4 in A1–A3 until the ChatGPT pass is done.
  2. Each layer owner runs a ChatGPT pass on the decision-bearing questions (I will prepare one prompt per layer that lists our decisions and asks ChatGPT to challenge them).
  3. Record disagreements and the team's resolution; mark rows "C+G".
  4. Conversation link: export this session's transcript into `specs/layer-NN-*/transcripts/` and link it; add the ChatGPT share link.
- [x] Approve
- **Your change:**

### H3 — Data-rule scope still open, but A2 Figure 1 answers it by drawing
- **Where:** A2 log Q2.12 ("before Layer 2 closes"); A2 2.7 ("Layer 6 at latest"); A2 Figure 1 ("Every part is inside the organization boundary"); A3 3.3 (hosted error tracker).
- **Problem:** The open question (does the rule cover existing external tools?) is answered implicitly by the diagram, not by a decision.
- **Proposed fix:** Record a decision now: **"V1 integrates only with tools that run inside the organization (internal or self-hosted). Hosted external tools, including a hosted error tracker, are out of scope for V1."** Add it to A2 2.1 as a Decision and to A2 2.6 non-goals; close the open item; Figure 1 then stays correct. Add to the architecture-breaking list: "if the organization uses hosted tools, integrations must change".
- [x] Approve
- **Your change:** but even for suporting extenal hosted also should be a problem as we woould only need to allow inbound and outbound traffic to that service ntg else would change.

---

## Medium

### M1 — A revert through the pipeline waits for staging and QA
- **Where:** A3 3.6 ("deploys it to staging, QA approves, then … production"); A2 2.3 (revert re-runs the pipeline); A2 phase-1 limit 5 minutes.
- **Problem:** A 2 AM revert would wait for a staging run and QA approval.
- **Proposed fix:** Add to A2 2.3: **a revert redeploys an artifact that already passed staging and QA, straight to production, after human approval (no new staging or QA run).** A config restore (H1) is the same. A **permanent fix** takes the normal path: sandbox proof → PR → staging → QA → production. Note the phase-1 limit covers only proposing the revert, not the deploy time.
- [x] Approve
- **Your change:**

### M2 — Who merges the fix pull request is not stated
- **Where:** A2 2.3 ("A fix is a branch and a pull request that the pipeline deploys").
- **Problem:** The merge actor, and whether platform approval equals PR approval, are not defined.
- **Proposed fix:** Add to A2 2.3: **the platform opens the PR; the human approver reviews and merges it. The merge is the approval for the fix.** Branch protection on the main branch requires one human review; the platform can never merge. Forward the detail to Layer 11 (Rinisha).
- [x] Approve
- **Your change:**

### M3 — A3 fixtures clash (B runs on A's broken config)
- **Where:** A3 3.6 and Figure 2 (A: config v41 → v42 broken; B: "v42 unchanged"; both base code a1b2c3).
- **Problem:** The incidents become the V1 fixtures, so shared IDs make B run on A's broken config.
- **Proposed fix:** State in A3 that **each incident is an independent fixture** (its own world). Give distinct IDs: A — code `a1b2c3`, config `v41 → v42`; B — code `b7c8d9 → e4f5a6`, config `v17` (unchanged); C — code `9c8d7e`, config unknown. Update the table and Figure 2.
- [x] Approve
- **Your change:**

### M4 — Incident A alert fires before the incident rule is met
- **Where:** A3 3.1 rule "> 2% … for more than 5 minutes"; Incident A alert "98% … for 4 minutes" at 02:05.
- **Proposed fix:** Move Incident A's alert to **02:07** ("98% for 6 minutes"); update Figure 2.
- [x] Approve
- **Your change:**

### M5 — Incident C internal problems
- **Where:** A3 at-a-glance "Code version: Unknown" vs 3.6 "9c8d7e (unchanged for 24 h)"; 3.9 "errors are spread across instances".
- **Problem:** Version mismatch; the "spread" claim has no evidence (logs and metrics from 2 of 3 instances are missing).
- **Proposed fix:** At-a-glance → "`9c8d7e`, no deploy in 24 h". Rewrite 3.9 observed: **"30% errors fits one bad instance out of three, which supports the memory hint, but logs and metrics from the other two instances are missing, so it cannot be confirmed."** Root cause stays "cannot be established"; escalate.
- [x] Approve
- **Your change:**

### M6 — Assumptions due in Layer 3 not closed; Incident C contradicts L2-A6
- **Where:** L2-A6 (trace context), A1 E1 (on-call schedule), A1 A2 (alert fields), L2-A1 (repo/pipeline mapping).
- **Proposed fix:** Add a **"Validation status"** table to the A3 appendix:
  - L2-A6 — true for `shop-api`; **deliberately false for `orders`** (Incident C tests missing telemetry). Real-world: open.
  - A1 A2 (alert has rule, limit, severity) — validated for fixtures (section 3.1). Real-world: open.
  - L2-A1 (one repo and one pipeline per service) — validated for fixtures. Real-world: open.
  - A1 E1 (on-call schedule readable) — still open; add an on-call schedule to the fixtures.
- [x] Approve
- **Your change:**

### M7 — A2 says loop limits are given, but step and cost limits are missing
- **Where:** A2 appendix B3 row "Values given. No conflict".
- **Proposed fix:** Set provisional numbers in A2 2.4 (tuned by the Q2.16 experiments): **phase 1: 5 minutes; full investigation: 20 minutes (upper bound of 15–20); steps: 30 tool calls per investigation; cost: 10 GPU-minutes per incident.** Change the appendix row to "Provisional values; tune by experiment".
- [x] Approve
- **Your change:**

### M8 — GPU capacity ignores HA copies; a 4th SEV1 waits in the queue
- **Where:** A2 2.4 / 2.5, L2-A10, L2-A12.
- **Proposed fix:**
  1. L2-A10 becomes: "existing GPUs can hold **2 models × 2 copies (HA)** and serve 3 concurrent incidents".
  2. Add to A2 2.4: **recovery work (phase 1) pre-empts fix-phase work**; a new SEV1 takes the slot of a running fix-phase job, which is paused and re-queued. The queue order stays severity, then age.
- [x] Approve
- **Your change:**

### M9 — Layer 3 states platform design decisions
- **Where:** A3 appendix ("the platform does not propose a rollback … It escalates"; "a config revert uses the same write path"); parts of A3 3.10.
- **Proposed fix:** Reword the A3 appendix rows as **"Finding forwarded as a constraint"** to Layer 4 (requirements) and Layer 11 (Rinisha), not as platform decisions. Keep 3.10 as "expected escalation behavior" (the template requires it) but phrase it as the expected behavior for the catalog, not as design.
- [x] Approve
- **Your change:**

### M10 — Layer 1 log names a model
- **Where:** L1 log, E3 row ("GLiNER2.5-Decide; 'Jev' family").
- **Proposed fix:** Remove the model name from the L1 log; keep "decision method to be chosen in Layer 9/10". Move the GLiNER note to `specs/layer-09-agent-architecture/prep-notes.md` (Dhanush) and reference it from Layer 10 (Irfan).
- [x] Approve
- **Your change:**

### M11 — Self-approval allowed without rationale
- **Where:** A1 1.2, 1.5 ("The approver can be the author of the change").
- **Proposed fix:** Add the rationale to A1: **at 2 AM the author is often the only person on call; other controls still apply — a different-model review, policy checks, an undo path, and the audit trail.** Add an open item for Layer 5 (Chirag): review separation of duties (e.g. forbid self-approval for SEV1 if the threat model shows the risk is too high).
- [x] Approve
- **Your change:**

### M12 — "Decision model" means two things
- **Where:** A1 1.5 "decision models" (Type 1/2, System 1/2 frameworks); A2 2.5 "The decision model … run[s] on CPU" (an ML classifier).
- **Proposed fix:** Rename the ML model to **"gate model"** in A2 (2.5, 2.6, Figure 3) and logs. A1 keeps "decision models" for the frameworks.
- [ ] Approve
- **Your change:** use the term decision models for the gliner, jev kinda model, use some other term for the other decision, such as rules, policies.

---

## Low

### L1 — A1 1.3 overclaims when errors continue after a revert
- **Where:** A1 1.3 "Reports that the change was not the cause".
- **Proposed fix:** "Reports that the change **may not be the only cause** (the revert can be incomplete, or the config may not have reloaded)."
- [x ] Approve
- **Your change:**

### L2 — A2 Figure 1 shows chat as an interface
- **Where:** A2 Figure 1 "Engineers: CLI, HTTP API, chat"; 2.3 says CLI and HTTP API only.
- **Proposed fix:** Figure 1 label → "CLI, HTTP API (chat: open, Layer 11)".
- [ x] Approve
- **Your change:**

### L3 — A2 "Production access" mislabelled as Fixed requirement
- **Where:** A2 2.3 "Production access … Fixed requirement, source: Artifact 1".
- **Proposed fix:** Relabel as **Decision** (rationale: Q2.14; consistent with A1's approval rule).
- [ x] Approve
- **Your change:**

### L4 — L2-A9 is stale
- **Where:** A2 appendix and log L2-A9 ("small GPU server").
- **Proposed fix:** Remove L2-A9; it is replaced by the Q2.15 decision (existing GPUs) and L2-A12 (reserved).
- [ x] Approve
- **Your change:**

### L5 — Owners do not match the work split
- **Where:** L3-A2 → Dhanush for Layer 4 (owner Chirag); L2-A2 and "demo setup" → Dhanush for Layer 6 (owner Manish).
- **Proposed fix:** Apply the rule "owner = owner of the validating layer": L3-A2 → **Chirag**; L2-A2 and demo setup → **Manish**. Layer 2 items with no later layer stay with Dhanush (coordinator).
- [ ] Approve
- **Your change:** whoever is working in that layer is the owner upate chirag to dhanush.

### L6 — Log items missing from the artifacts
- **Where:** L1 A5 (business impact measure); L2: Python framework, sandbox design (Rinisha, L11), queue-order owner, custom-model revisit trigger; L3: database-migration question, L3-A2 SLO values; PostgreSQL rationale differs between A2 log and A2 2.7.
- **Proposed fix:** Add each missing item to the matching artifact appendix (open items or assumptions) with its owner; add the SLO values (99.9%, p95 < 300 ms) to A3 3.1; make the A2 log and A2 2.7 PostgreSQL rationale identical.
- [ x] Approve
- **Your change:**

### L7 — A3 log Q3.15 claims a match with A1 that is not exact
- **Where:** A3 log Q3.15 ("This matches Artifact 1's life cycle").
- **Proposed fix:** "Today's human order puts 'fix proposed' before 'mitigated'; Artifact 1 mitigates first (recovery first). This difference is intended."
- [ x] Approve
- **Your change:**

### L8 — Reference-scenario permanent fix equals the revert; real fix may touch the pipeline
- **Where:** A3 3.7 Incident A ("Correct value 30, and validate config types before deploy"); A1 1.7 non-goal "Write to any service other than the affected service".
- **Proposed fix:** Incident A permanent fix → **"Correct value `30`, and add type validation in `shop-api`'s own config loader, so a bad value fails at startup, not on every request."** This stays inside the affected service. Pipeline-level validation becomes a recommendation for the owner, not an action.
- [ x] Approve
- **Your change:**

### L9 — "Reviewed by" blank; stale placeholder in A1 log
- **Where:** All three artifacts; A1 log "Artifact 1 draft sections — fill after all sections A–D are done".
- **Proposed fix:** Remove the stale placeholder. Leave "Reviewed by / Date" blank until a team member reviews after these fixes; add "Fill Reviewed by" to PROGRESS.md open items (already there).
- [ x] Approve
- **Your change:**
