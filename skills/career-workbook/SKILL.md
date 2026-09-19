---
name: career-workbook
description: "Display, review, and update the Career Workbook before starting a new job application session. Gated entry point: confirms workbook is current before the session prompt is shown."
---

# /career-workbook (Step 1 of /new-session)

**Purpose:** Surface the Career Workbook for a quick review. The user confirms it is current — or provides corrections — before the session prompt template is shown. This is Step 1 of the two-step `/new-session` flow.

**Effort:** low — display + optional edit; no resume generation.

---

## Workbook Path

Primary: `../Career & Certifications/Career_Workbook_Master_Lokapati.md` (relative to the job-applications project root; canonical source: `/GoogleDrive/Lokapati.Personal1/Jobs/Career & Certifications/Career_Workbook_Master_Lokapati.md`)

Fallback derivation order if the primary path does not exist:
1. Check `CLAUDE.md` Knowledge Base section for an overriding path
2. Check `session_learnings.md` for the last confirmed path
3. Ask the user: "I can't find the Career Workbook. Please provide the path or paste the content."

---

## Step 1 — Display Workbook Summary

Do NOT print the full 1800-line workbook. Instead, render this summary table by reading the workbook header and each Workbook N section:

```
## Career Workbook — Current Snapshot
Last reviewed: {today's date}

### Career Timeline
| # | Company | Title | Period | Industry |
|---|---------|-------|--------|----------|
| 1 | {W1}    | ...   | ...    | ...      |
...

### Certifications & Education
{one-line each from workbook header}

### KB Corrections in Effect
{list any corrections noted in CLAUDE.md KB Corrections section — or "None on file"}

### Verified Metrics (canonical)
| Workbook | Metrics |
|----------|---------|
| W1 (Alcon)       | Metrics marked TBD — use directional language only |
| W2 (Baxter Sr)   | 30% release delay reduction; 25% audit readiness; 30% manual process reduction; 40% audit findings reduction; 500+ LoVs |
| W3 (GCHP)        | 98% data accuracy; 15% audit trail; 12% reconciliation error reduction; zero operational disruption |
| W4 (Baxter Jr)   | 30% faster release; 100% supply chain traceability; 30% faster recall cycles; 75% manual reconciliation reduction; 30% onboarding reduction; zero data loss |
| W5 (Kiddie)      | 20% CSAT; 30% onboarding reduction; $5M Series A; 12% operational cost reduction |
| W6 (USD)         | 25% drop-off reduction; 75% laptop recovery; $50K revenue |
| W7 (Nike)        | 6 composite KPI metrics — no company outcomes confirmed |
| W8 (Infosys)     | Merger context ($78.7B TWC–Charter) — no personal metrics confirmed |
| W9 (TCS)         | 1M+ agent platform scale; specific % TBD |
```

Then ask:

> **Does this look current?**
>
> - Reply **"yes"** or **"looks good"** → move to Step 2 (session prompt)
> - Reply with corrections, additions, or new roles → apply updates (Step 1b), then confirm again

---

## Step 1a — No Changes (proceed)

If the user confirms with "yes", "looks good", "correct", "proceed", "continue", or any clear affirmative:

- Log to session learnings (if session_learnings.md exists): `| {date} | Workbook review: no changes |`
- Say: "Workbook confirmed. Moving to Step 2 — session prompt."
- Hand off to Step 2 in `skills/new-session/SKILL.md`

---

## Step 1b — Apply Changes

If the user provides corrections or new information, handle each change type:

### New role or employer
1. Ask for: Company, Title, Period, Industry, Team Size, Reporting To, ~3 bullet responsibilities, any metrics
2. Derive a new Workbook number (next after current highest)
3. Append to `Career_Workbook_Master_Lokapati.md` using the standard Workbook template:
   ```markdown
   # Workbook N — {Company} | {Title} | {Period}
   ## 1. Engagement Overview
   ## 2. Core Responsibilities
   ## 3. Projects & Contributions
   ## 4. Key Metrics & Accomplishments
   ## 5. Skills Demonstrated
   ## 6. Technology Landscape
   ## 7. Career Positioning
   ```
4. Add a row to the Career Timeline table at the top

### Correction to existing role
1. Locate the affected Workbook section
2. Apply the edit inline — do not create a new section
3. If the edit touches a metric in the Verified Metrics table, ask: "Should I update the canonical metrics table in the skill as well?"

### New metric confirmed
1. Add to the workbook's Key Metrics section
2. Note the metric in the KB Corrections section of `CLAUDE.md`:
   ```
   **W{N} ({Company}):** {metric description}. Confirmed {today's date}.
   ```
3. Ask if the Verified Workbook Metrics table in `SKILL.md` (parent skill) should be updated

### Technology or certification update
1. Update the Technology Landscape or Certifications section in the appropriate workbook
2. Update the header summary if certifications change

### After all edits are applied:
- Re-display the Career Timeline summary table (updated)
- Ask: "Changes applied. Does the workbook look correct now?"
- Loop back to Step 1 confirmation — accept "yes" to proceed to Step 2
- Do NOT proceed to Step 2 without explicit confirmation

---

## Step 2 Handoff

Once the user confirms the workbook is current, say:

> **Workbook confirmed. Here is the session prompt — fill in all fields and paste your JD.**

Then execute Step 2 from `skills/new-session/SKILL.md` starting at the **Session Prompt** block (skip the `/new-session` startup check — this flow already handled it).
