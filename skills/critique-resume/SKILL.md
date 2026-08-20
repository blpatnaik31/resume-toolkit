---
name: critique-resume
description: "Re-critique existing resume/CV output files against a JD"
---

**User input:** `$ARGUMENTS`

Parse `$ARGUMENTS`:
- Session file path (e.g., `output/Acme/session_acme_engineer.md`) → read session file, derive .tex paths from Output Files
- .tex file path(s) + JD source (existing format) → backward compatible
- Session name (e.g., `acme_engineer`) → find session file via derivation

If no CL .tex provided or found in session file, critique resume/CV alone (Part 7 adjustments noted below).

---

## Safety Rules

**Accuracy > Relevance > Impact > ATS > Brevity**

Read `config.md` Provenance Flags (skip if absent). Verify every claim against `Career_Workbook_Master_Lokapati.md`.
Check `config.md` KB Corrections Log if it exists — do not flag corrected items as errors.
Use the email from `Career_Workbook_Master_Lokapati.md` Personal Info header — flag if a different email appears in output.
FIXED sections (from `config.md` if it exists; otherwise header/contact/education/certifications) are template-locked — do not flag for editing. Flag only VARIABLE sections.

## Metric Fabrication Check (MANDATORY — Part 0 of critique, before scoring)

Before running the 8-dimension scoring, scan every metric (%, $, headcount, named scope) in the resume against the Verified Workbook Metrics table in the parent `SKILL.md`.

For each metric:
- If it appears in the Verified Metrics table → mark ✅ in critique
- If it does NOT appear → mark ⚠️ UNVERIFIED and flag as a Tier 1 fix

Reference: The Optimizely session (2026-05) had 2 unverified Alcon metrics (30% time-to-decision, 25–35% integration effort) that were not in the Workbook. These are the canonical failure examples. Any similar pattern is a Tier 1 fix regardless of how plausible the number sounds.

Also flag:
- Sponsorship gate: if session file or JD says "no sponsorship" and the resume was generated anyway, flag ⛔
- Title inflation: if Experience section shows a title not held per Workbook, flag as Tier 1

## Writing Style Check (MANDATORY — runs alongside Part 0)

- **Em dashes:** grep the resume and CL for `—`. Any hit is a Tier 1 fix — zero em dashes are allowed in final output.
- **AI-fingerprint tone:** scan for repeated verb-first bullet openers (three or more bullets in a row starting "Led/Directed/Drove/Owned/Built"), dense per-bullet bolding of every noun phrase, and parallel-listicle phrasing in the Summary or CL body. Flag as Tier 2 with specific line references — this reads as AI-generated to a human reviewer even when the content is accurate.

---

## User Input During Execution

If the user provides feedback, corrections, or suggestions at any point:
1. Acknowledge the input immediately
2. If it changes scoring criteria or focus: adjust the critique accordingly
3. Never restart — resume from current position

---

## Startup

Read `resume_builder/reference/shared_ops.md` — Fresh Session Startup + Session File Derivation.
Read `CLAUDE.md` — check Active Sessions and KB Corrections.
Read `config.md` — load Provenance Flags, FIXED Sections, email.
Find and read the session file for the .tex being critiqued (use derivation protocol from shared_ops.md).

**Recovery check:**
- If CL Status shows `Markdown DONE` → CL markdown is ready; proceed (DOCX not required for critique)
- If CL Status is PENDING and no CL .md exists → "CL not yet generated. Run `/make-cl` first."
- If Resume .md and CL .md both exist (regardless of DOCX status) → proceed
- If Critique: CURRENT → "Already critiqued (score X/100). Re-run? Waiting for confirmation."
- If Critique: STALE → "Edits made since last critique. Re-critiquing."
- If Critique: PENDING → proceed

---

## Protocol

1. **Read session file** — specifically note:
   - **Company Context** → reviewer persona, "why this company"
   - **Framing Strategy** → intentional reframing decisions (flag only execution inconsistencies, not the strategy itself)
   - **Cover Letter Plan** → CL structure rationale
   - **Critique Context** → reviewer persona, competitive landscape, domain vocabulary
   - If session file lacks Company Context or Critique Context: do 1-2 web searches to fill gaps
2. Read `resume_builder/reference/critique_framework.md`
3. Read `resume_builder/support/ai_fingerprint_rules.md` — use Section 6 checklist in Part 7 verification
3. Read the .md file(s) — derive paths from session file Output Files, or from `$ARGUMENTS`
4. Read the JD (path from `$ARGUMENTS` or session file)
5. Read the relevant bundle (`resume_builder/bundles/bundle_[role_type].md` — from session file)
6. Run char count (if char_count helper exists):
   ```bash
   python3 resume_builder/helpers/char_count.py -f [resume|cv] [file.md]
   ```
   If helper absent: manually review bullet line lengths for OVER violations.
7. If .docx exists: verify page count and layout by reviewing the .md source for density.
   Note any obvious orphans or header wrapping issues from the markdown content.
8. If a prior critique exists (`output/<FolderName>/critique_<name>.md`): read it and note previous score.
8b. **Paper Hook Verification:** If the CL cites named papers, PIs, programs, or publications, web-search to verify title, journal, year, and PI affiliation. Flag factual errors as Tier 1 fixes.

9. **Run the full critique per critique_framework.md. The output MUST contain ALL 8 sections** (even if the framework file has partially compacted, produce every section):

    1. **Domain-Specialist Lens** — 7 elements:
       (a) Reviewer persona (b) Company context (c) JD vocabulary extraction (d) Domain vocabulary map
       (e) Gap ranking (fatal/serious/cosmetic) (f) Methodology transfer test (g) Competitive landscape
    2. **Five-Perspective Read-Through** — ATS, Recruiter (10s), HR (30s), HM (2min), Technical (10min) — each with verdict
    3. **Eight-Dimension Scoring** — weighted table summing to 100
       (ATS 15%, Summary 10%, Skills 10%, Bullets 25%, Publications 10%, Narrative 15%, Visual 5%, Credibility 10%)
    4. **Interview Likelihood** — per-reader probability + ceiling analysis
    5. **Tiered Improvements** — Tier 1 (>=1pt each), Tier 2 (0.3-0.9), Tier 3 (<0.3)
    6. **Interview Bridge Points** — 5-7 resume-to-interview talking points
    7. **Cover Letter Critique** — 6 sub-checks (6A anti-patterns, 6B tailoring, 6C context-specific, 6D ATS, 6E structural, 6F package cohesion)
       - **If no CL provided:** Skip 6A-6E. Run 6F as resume standalone assessment — evaluate whether the resume earns an interview without a CL. Note: "Cover letter not provided — package cohesion not assessed."
    8. **Post-Generation Verification** — mechanical + content + structural checklists

10. Save to `output/<FolderName>/critique_<name>.md`
11. **Update session file** — Critique Summary (score, findings, tier 1 fixes), Status → Critique: CURRENT
12. **Update memory pointer** with new score

Progress: "Reading session file for framing context..." / "Running ATS keyword scan — 16/20 match..." / "Scoring 8 dimensions..." / "Score: 87.0/100"

### >>>>>> MANDATORY STOP <<<<<<
Present: score table + tier 1 actionable fixes + interview likelihood.
**You MUST wait for the user's explicit text response before continuing.**
If edits needed, tell user to run `/edit-resume`.

### When user approves / says "looks good" / finalizes:
Verify all expected files exist in `output/<FolderName>/`:
- session file, resume/CV .tex + .pdf, CL .tex + .pdf, critique .md
- Compile artifacts (.aux, .log, .out)
Confirm to user: "Package complete in output/<FolderName>/ — [list files]"