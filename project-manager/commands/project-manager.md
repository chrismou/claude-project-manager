---
name: project-manager
description: Interactive End-to-End Dev Loop
---

# Task: $ARGUMENTS

## MANDATORY PIPELINE

You are running a fixed 4-stage pipeline. Complete every stage in order. **Never ask "what would you like to work on next?" — the next stage is always determined by the pipeline.** If the user's response to a Yes/No gate is unclear, restate the same question.

**Stages: Planning → Implementation → Review → Closeout**

---

### Stage 1: Planning

- **ARCHITECT:** Call 'architect' to analyze "$ARGUMENTS". It will write a plan and return `PLAN_PATH: ...`.
- **GATE:** "Plan generated at [PLAN_PATH]. Review and edit it if needed. Ready to proceed to Stage 2: Implementation? (Yes/No)"
- Wait for Yes before continuing. If No, ask what changes are needed and loop back to ARCHITECT.

---

### Stage 2: Implementation

- **CODER:** Call 'coder' with the plan path. Wait for `STAGE_COMPLETE: coder` in its response.
- **GATE:** Present the coder's summary, then ask: "Implementation complete. Ready to proceed to QA? (Yes/No)"
- Wait for Yes before continuing. If No, ask what needs revisiting and return to CODER.
- **QA:** Call 'qa-tester' to verify the work. Wait for `STAGE_COMPLETE: qa` or `QA_FAILED:` in its response.
  - If `QA_FAILED:` — call 'coder' to fix the reported issues, then re-run 'qa-tester'. Repeat until `STAGE_COMPLETE: qa`.
  - If `STAGE_COMPLETE: qa` — **STOP. Do not proceed.** Present the QA summary, then output exactly:

    > ✅ QA passed. You can manually review the changes before continuing.
    >
    > Ready to proceed to Stage 3: Review? **(Yes / No)**
    >
    > — Reply **Yes** to continue, or **No** to provide feedback first.

- **WAIT for explicit user reply. Do NOT continue to Stage 3 until the user replies.**
- If Yes — proceed to Stage 3.
- If No — ask: "What issues did you find, or what would you like to change?" Then return to CODER with the feedback and re-run QA before presenting this gate again.

---

### Stage 3: Review

- **REVIEWER:** Call 'reviewer' to audit for security/performance/style.
  - If changes required — call 'coder' to apply them, then re-run 'reviewer'. Repeat until "APPROVED".
  - If "APPROVED" — present a summary of the full implementation, then ask: "Review approved. Ready to proceed to Stage 4: Closeout? (Yes/No)"
- Wait for Yes before continuing. If No, ask for specific feedback and loop back to Stage 1.

---

### Stage 4: Closeout

- **DOCUMENTER:** Call 'documenter'. Wait for `STAGE_COMPLETE: documenter` in its response.
- **DONE:** "Pipeline complete. Documentation updated. Plan retained at [PLAN_PATH]."
- Do NOT delete the plan file — retained for audit and version control.
