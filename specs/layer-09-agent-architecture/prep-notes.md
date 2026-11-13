# Layer 9 prep notes (provisional — not a log, not an artifact)

Owner: Dhanush. Layer 9 cannot start until A3, A7 and A8 exist. These notes only carry ideas forward.

## Candidate for the System 2 escalation gate (from Layer 1, E3)

- **Decision model** (term reserved for this kind of ML model, review M12): a small schema-constrained classifier such as **GLiNER2.5-Decide** (open weights, Apache 2.0, 340M parameters, runs on CPU; compared against the "Jev" family, e.g. JevK5).
- Input: incident text + a schema of typed questions (escalate?, reason, evidence strength, revert safe?) with cross-question rules. Output: one rule-consistent answer set with probabilities.
- Use as **one gate signal next to the fixed rules**, never the only decider; unsure or schema-invalid output = escalate.
- Limits: vendor's own benchmark (~60% on unseen tasks); no evidence spans for classification; probabilities not proven calibrated; needs validation on our fixtures.
- Model choice and hosting with Irfan (Layer 10).
- Source: https://fastino.ai/blog/gliner-2-5-decide-open-weight-decision-model
