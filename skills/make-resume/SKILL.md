---
name: make-resume
description: "Generate a tailored resume/CV from a JD"
---

**User input:** `$ARGUMENTS`

Parse `$ARGUMENTS`:
- File path (e.g., `JDs/*.txt`) → read that file for the JD
- Text after the path starting with "Focus:"/"Emphasize:"/"Downplay:" → focus directive
- "Quick:" prefix → Quick Mode (see below)
- Empty → ask the user for the JD
- Inline JD text (no file path) → save to `JDs/temp_<company>.txt`, proceed normally
- Job URL → read that URL for the JD
---

## Primary Knowledge Source

**Career Workbook:** `Career_Workbook_Master_Lokapati.md` (in the project working directory)
This is the authoritative source of all bullet content. Each employer is a numbered Workbook with sections: Projects & Contributions (detailed project bullets), Key Metrics & Accomplishments, Skills Demonstrated, and Career Positioning. Use these sections to identify and draft bullet candidates. The `resume_builder/experience/` KB files are optional — read them only if they exist alongside the workbook.

---

## Safety Rules (ALWAYS ENFORCED)

**Accuracy > Relevance > Impact > ATS > Brevity**

Read `config.md` Provenance Flags before generating any content (if `config.md` exists; otherwise derive provenance from the Career Workbook). Verify every claim against the Career Workbook.

- Use the email from the Career Workbook Personal Info header in all outputs
- Resume bullets: ALL variable bullets are 2L (CV: 2L/3L mix OK)
- Source ALL bullet content from `Career_Workbook_Master_Lokapati.md`. Never fabricate.
- Run `python3 resume_builder/helpers/char_count.py` after each section — the tool is authoritative

## Metric Verification Gate (MANDATORY — runs before any bullet is presented to user)

Every metric in every bullet must be traceable to a named Workbook section. Use the canonical list from the parent `SKILL.md` Verified Workbook Metrics table.

**For each metric in a bullet, annotate internally as `[W#-P#]` during generation, then strip citations before final output.**

If a metric cannot be traced to the Workbook:
- Replace with directional language: "improved X", "reduced Y", "accelerated Z"
- Never invent a percentage, dollar figure, or headcount

The Optimizely session (2026-05) is the reference failure case: 2 Alcon metrics (30% time-to-decision, 25–35% integration effort) were not in the Workbook and were flagged by critique post-generation. This gate prevents recurrence.

## Sponsorship & Viability Gate (runs before Phase 0 outputs)

Check the session inputs or CLAUDE.md for sponsorship status:
- If JD or session notes say "US citizen/GC only", "no sponsorship", or "citizen only": output **⛔ STOP** and ask for explicit user override before proceeding.
- If C2C requested but role is FTE-only: output **⚠️ C2C mismatch** and ask how to proceed.
- Otherwise: output **✅ PROCEED**.

---

## Writer Role Selection

Determine the writer role from the target job title and seniority signals in the JD. Record as **Writer Role** in the session file and apply consistently through Summary and all bullets.

| Seniority signals in JD title / level | Role |
|---|---|
| Director, Senior Director, VP, SVP, EVP, Managing Director, Head of, Principal (strategic scope), Partner, C-suite | **CERW** — Certified Executive Résumé Writer |
| Manager, Senior Manager, Lead, Staff, Senior IC, Specialist, Analyst, Individual Contributor | **CPRW** — Certified Professional Résumé Writer |

**CERW writing philosophy** — Strategic scope first, execution second:
- Open bullets with organizational impact, decisions driven, and business outcomes before task detail
- Executive register: "Orchestrated", "Spearheaded", "Architected", "Drove enterprise-wide…"
- De-emphasize tools and tactical steps; elevate cross-functional influence, stakeholder tier, and program scope
- Summary: thought leader / change agent framing; 3–4 lines; no task listing; positions for Director+ next step

**CPRW writing philosophy** — Achievement-forward, metrics-anchored:
- Lead every bullet with a strong action verb tied to a quantified result (XYZ / CAR format)
- Include technical tools and domain terms that are explicit JD requirements
- Demonstrate execution depth: scope, timelines, and metrics moved
- Summary: skills-forward value proposition; 2–3 lines tightly aligned to JD keywords

---

## User Input During Execution

If the user provides feedback, corrections, or suggestions at any point:
1. Acknowledge the input immediately
2. If it affects an already-written section: go back, fix it, re-run char count gate
3. If it changes the bullet plan: update session file Bullet Plan
4. If it's a question: answer it, then continue from current step
5. Never restart a phase — resume from current position

---

## Startup

Read `session_learnings.md` first if it exists — it lists which optional files are absent so you don't waste tokens trying to read them.

Then:
1. **Read `Career_Workbook_Master_Lokapati.md`** — this is the primary source; load it fully into context now so it does not need to be re-read in later phases
2. Read `CLAUDE.md` — check Active Sessions and KB Corrections (skip if file absent)
3. Skip `config.md`, `resume_builder/reference/`, `resume_builder/support/`, `resume_builder/bundles/`, `resume_builder/templates/` unless session_learnings.md confirms they exist
4. If session file exists for this JD:
   - Read session file, check Status
   - Phase 0: DONE, Phase 1: PENDING → resume at Phase 1
   - Phase 1: DONE → resume at Budget Gate
   - Phase 2: IN_PROGRESS → read .md, check what sections exist, resume from checkpoint
   - Phase 2: DONE → "Resume already done. Run /make-cl next." Show next command. Stop.
5. If no session file: proceed to Phase 0

---

## Quick Mode

Trigger: `$ARGUMENTS` starts with "Quick:"

Defaults:
- Select all HIGH priority achievements from bundle's Priority Matrix as 2L
- Fill remaining budget with MEDIUM priority in Priority Matrix order
- Default format: 2-page resume (unless JD clearly requires CV)
- Skip Phase 0 STOP and Phase 1 STOP
- Keep Budget Gate (auto-pass if within target) and end-of-resume STOP
- Run all phases with progress commentary instead of interactive stops

---

## Phase 0: Research & Session Setup

**Read these files:**
1. The JD (from `$ARGUMENTS` or session file if `/new-session` was run first)
2. `resume_builder/reference/resume_reference.md` — Budget Card, Section Specs, Char Limits, Page Budgets (skip if absent; use standard 2-page / 5-7 bullets per position defaults)
3. `config.md` — Role-Type Decision Tree (skip if absent; derive role type from Career Workbook "Career Positioning" sections and JD)
Note: Career Workbook is already in context from Startup — do NOT re-read it here.

**Run Sponsorship & Viability Gate first** (see above). Output ⛔/⚠️/✅ before proceeding.

**Web Search (MANDATORY — 2-3 searches).** Load WebSearch via ToolSearch first.
1. `[Company] research & development [key JD domain]` — products, recent projects
2. `[Company] [specific technology from JD]` — concrete hooks for cover letter
3. `[Company] careers [role type] culture` OR recent news — hiring context

If web search returns no results: use JD text + training knowledge. Flag: "Web search returned limited results — CL hooks may be generic."

**Produce ALL of these — every section is mandatory; incomplete Phase 0 produces generic bullets:**

- **Writer Role** — Determine CERW or CPRW (see Writer Role Selection). State: `Writer Role: [CERW/CPRW] — Target title "[exact title from JD]" signals [Director+/Manager-level] scope.`
- **JD Analysis** — Requirements Table: every requirement classified as Direct / Bridge (with confidence) / Gap; Workbook Evidence column required for every Direct/Bridge row. ATS Keyword Bank by category (Delivery/Execution | Agile/Tools | Risk/Compliance | Stakeholder | Domain | Leadership).
- **Gap Assessment** — for every Gap row: propose closest truthful bridge OR label "cannot bridge — omit"
- **Company Context** — mission, role purpose, culture signals, "why them" angle (from web research)
- **Framing Strategy** — lead narrative + **Reframing Map** (Workbook Language → JD/ATS Target Language; must cover all 4 primary positions AND at least one Early Career line) + emphasize/downplay + CL hooks + user focus directives
- **Compensation Analysis** — populate this table (use market estimate if comp not posted):

  | Field | Value |
  |---|---|
  | JD-stated comp range | |
  | Market rate range | |
  | Employment type | |
  | Vendor layers (3 or 4) | |
  | C2C bill rate estimate | |
  | 3-layer C2C takeaway | |
  | 4-layer C2C takeaway | |
  | W2 equivalent | |
  | Recommended ask (C2C) | |
  | Recommended ask (W2/FTE) | |
  | Negotiating floor | |

  Layer models:
  - 3-layer: Client → Vendor (15–25% margin) → Employer (10–15% overhead) → Candidate
  - 4-layer: Client → Prime (10–15%) → Mid-Vendor (10%) → Employer (10%) → Candidate
  - W2 equivalent ≈ C2C takeaway ÷ 1.25

- **Critique Context** — reviewer persona, competitive landscape, domain vocabulary
- **Cover Letter Plan** — institution type, paragraph structure, hooks, jargon level

**Create output folder and name session:**
- Derive folder name from JD filename: `JDs/JD_Acme.txt` → `output/Acme/`
- Name the session using the format: **`Job Title | Client | Vendor | Employer`**
  - Client: end-client company (blank if direct hire)
  - Vendor: staffing/services vendor (blank if direct)
  - Employer: the direct employer of record
  - Examples: `Senior PM | Santander | OkayaInfocom | OkayaInfocom` | `TPM | [direct] | Exaways | Altimetrik`
- If `/new-session` was run first, the folder and stub already exist — append to that file.
- Otherwise create:
```bash
mkdir -p output/<FolderName>/
```
Write session file to `output/<FolderName>/session_<name>.md` (NOT flat `output/`).
All subsequent output files go in this folder.

**Verify completeness:** Confirm these 9 sections are non-empty: Viability Gate, JD Info, Requirements Table, ATS Keywords, Gap Assessment, Company Context, Framing Strategy (with Reframing Map), Compensation Analysis, Cover Letter Plan. Fill any missing section before presenting.

**Write memory pointer** to `CLAUDE.md` Active Sessions.

**Update session file Status:** `Phase 0: DONE`

Progress: "Searching for [company] + [domain]..." / "JD analysis: X/Y requirements direct match, Z bridges, W gaps" / "Comp: [3-layer|4-layer] → $XX/hr C2C takeaway"

### >>>>>> MANDATORY STOP — DO NOT PROCEED <<<<<<
Present: viability gate result, research summary, role type, framing strategy, reframing map, comp analysis.
Ask user to confirm: (1) role type + format, (2) framing strategy + reframing map, (3) comp figures (correct or adjust?).
**You MUST wait for the user's explicit text response before continuing.**
Proceeding without confirmation misaligns the entire resume and requires full regeneration.

---

## Phase 1: Plan Bullets

**Re-read `output/<FolderName>/session_<name>.md`** — specifically Framing Strategy and ATS Keywords.

**Sources (read only what exists — Career Workbook is already in context):**
1. If `resume_builder/bundles/bundle_[role_type].md` exists → read Section 1 (Priority Matrix). Otherwise derive priority from Career Workbook "Career Positioning" + "Key Metrics & Accomplishments" sections.
2. If `resume_builder/experience/` files exist → read them for pre-drafted bullet variants. Otherwise source directly from Career Workbook "Projects & Contributions" sub-sections.
3. If `resume_builder/support/achievement_reframing_guide.md` exists → read it. Otherwise derive reframing from Career Workbook "Career Positioning" sections.
4. If `resume_builder/support/skills_taxonomy.md` exists → read it. Otherwise derive from Career Workbook "Skills Demonstrated" + "Technology Landscape" sections.
5. If `resume_builder/support/pub_metadata.md` exists → read it. Otherwise skip (user is a PM, not academic).

**Deriving achievement IDs from the Career Workbook:**
- Each Workbook N = one employer position. Each Project within a Workbook = candidate achievement.
- Assign IDs as `WN-Pm` (e.g., W2-P1 = Workbook 2, Project 1). Use Key Metrics items as sub-bullets or additional candidates (e.g., W2-M1).
- Map each candidate against the JD requirements (Direct / Bridge / Gap) using the ATS keywords from Phase 0.

**Present one table per position:**

**[Position Name] (Budget: N-M bullets, ~X-Y rendered lines)**

| | ID | Achievement | Variant | Lines | JD Match |
|---|---|-------------|---------|-------|----------|
| * | W2-P1 | [short description] | 2L | 2 | Direct |
| * | W2-P2 | [short description] | 2L | 2 | Direct |
| o | W2-M1 | [short description] | 2L | 2 | Bridge |
| x | W2-P3 | [short description] | -- | -- | Weak |

**Legend:** `*` = recommended (HIGH priority + Direct JD match) | `o` = available (MEDIUM or Bridge) | `x` = not recommended (Low or Gap)

**After all positions, show:**
- Recommended set total vs budget (from Quick Budget Card in resume_reference.md)
- Remaining budget slots and what could fill them
- Forced exclusions per provenance flags
- Focus directive impact (what changed vs Priority Matrix defaults)
- CV: confirm first bullet of first experience is 2L (page 1 rule)

**Update session file** — write Bullet Plan tables. Status: `Phase 1: DONE (N bullets confirmed)`

Progress: "Reading experience files for bullet candidates..." / "Recommending N bullets per position"

### >>>>>> MANDATORY STOP — DO NOT PROCEED <<<<<<
Present bullet plan. Wait for user to confirm/modify selections.
**You MUST wait for the user's explicit text response before continuing.**
If you proceed without confirmation, you will generate bullets the user didn't approve.
**Update session file with confirmed plan before continuing.**

---

## Budget Gate (AFTER user confirms bullet plan, BEFORE Phase 2)

**Re-read session file Bullet Plan section** to verify confirmed counts.

- Check budget targets from `resume_builder/reference/resume_reference.md` Budget Card (if absent: use 5-7 bullets per position, 2-page resume).
- Show: `Budget: [N] bullets vs target [T]. PASS/FAIL`
- **FAIL = do not proceed. Reconcile with user first.**

---

## Phase 2: Generate

**Re-read to restore context after compaction:**
1. `output/<FolderName>/session_<name>.md` (framing + confirmed bullet plan)
2. `resume_builder/reference/critical_rules.md` — Character Limits, Bold Width Penalty, Orphan rules (skip if absent)
3. `resume_builder/support/ai_fingerprint_rules.md` — Banned words, structural rules (skip if absent)
4. If Career Workbook content was compacted out: re-read only the Workbook sections for the confirmed bullet IDs (do NOT re-read the full workbook)

**Read section specs:** `resume_builder/reference/resume_reference.md` Section-by-Section Specs (if absent: use standard PM resume conventions)

**Apply writer role philosophy** (from session file Writer Role) across every section — CERW: scope-first, executive register, strategic outcomes; CPRW: action verb + metric forward, execution depth, JD keyword density.

**Generate section by section:**
1. Summary → apply CERW or CPRW writing philosophy (see Writer Role Selection); check against session framing strategy
   - Update Status → `Phase 2: Summary DONE`
2. Technical Skills
   - Update Status → `Phase 2: Skills DONE`
3. Each position's bullets → apply writer role philosophy per bullet; **CHAR COUNT GATE after each position**
   - Position titles: theme + date must fit ONE line. If wrapping, shorten title.
   - After each position: Update Status → `Phase 2: [Position] DONE`
4. **PAGE FILL GATE after all experience**

Save markdown to `output/<FolderName>/resume_<name>.md`

**Update session file** — add Output Files.

Progress: "Writing Position 1 bullets (6 of 7)..." / "Bullet 4 is SHORT at 184 chars — padding"

### CHAR COUNT GATE (per position)
Count characters per bullet line. Target: 180–220 chars per line for 2L bullets. Last line >= 70% fill. **Fix before next position.**

### PAGE FILL GATE
Estimate rendered pages from line count. Resume: <= 3 lines white space on last page. **If FAIL: add/trim variable bullets.**

### MARKDOWN REVIEW STOP

**>>> MANDATORY STOP — DO NOT PROCEED <<<**

Present the resume markdown and bullet audit table to the user. Show:
- Bullet audit table (issues found + fixes applied)
- Recruiter kit summary

"Resume markdown complete. To run the parallel workflow:
1. Run `/make-cl output/<FolderName>/session_<name>.md` to generate the cover letter markdown
2. Then run `/critique-resume output/<FolderName>/session_<name>.md` to score both together
3. Then run `/edit-resume` to apply tier-1 fixes

**DOCX + PDF: use Claude Web (claude.ai).** Paste the approved markdown and ask Claude to generate the DOCX and PDF. Claude Web produces better-formatted output than CLI.

Or to critique the resume alone now: `/critique-resume output/<FolderName>/session_<name>.md`"

**DOCX generation is NOT done in CLI** — always hand off to Claude Web after edits are approved.

Update Status → `Phase 2: Markdown DONE`

---

## Bullet Audit (MANDATORY — run internally after Phase 2 generation, before Markdown Review Stop)

For every bullet in the resume, run this audit silently:

| Check | Rule | Action if FAIL |
|-------|------|---------------|
| Char count | ≤210 chars for 2-line bullets | Shorten; never truncate meaning |
| ATS keyword | ≥1 keyword from Phase 0 ATS Keyword Bank per bullet | Add the highest-priority missing keyword naturally |
| Metric source | Every %, $, or # traceable to Workbook (Verified Metrics table in parent SKILL.md) | Replace with directional language |
| Weak verbs | No "helped", "assisted", "worked on", "supported", "participated" | Replace with strong action verb |
| Keyword bolding | All ATS keywords and metrics bolded in markdown | Apply **bold** |
| Early Career reframing | Kiddie Commute and USD Recycling bullets use JD-relevant keyword, not generic text | Reframe per Reframing Map |

**Report audit as a table:** bullet ID → issue → fix applied. Present this table to the user. Do not ask for approval — just fix and report.

---

## Recruiter Kit (generate immediately after bullet audit, before Markdown Review Stop)

Generate the following four assets using the Lead Narrative and Framing Strategy from Phase 0. Do not use generic language. Source all claims from Workbook metrics.

**A. Recruiter Email (≤150 words)**
```
Subject: {Job Title} — Lokapati Bhogela, PMP | {Key Differentiator Phrase}

{3–4 sentences: lead with strongest Workbook metric for this JD, state role title,
cite top 2 requirements met, years of experience, clear call to action.}

Lokapati Patnaik Bhogela, PMP
blpatnaik31@gmail.com | linkedin.com/in/lbhogela
Note: H-1B transfer required (not new sponsorship)
```

**B. LinkedIn Connection Note (≤300 chars)**
One sentence establishing relevance + one sentence value prop. Must fit in LinkedIn's connection note field.

**C. Voicemail Script (≤80 words)**
Format: Name → role applying for → key differentiator → contact info → callback ask.

**D. Recruiter Talking Points (5 bullets)**
Map each talking point to a specific JD requirement. Order: strongest match first. These are the 5 points the candidate must hit if a recruiter calls cold.

Save Recruiter Kit to `output/<FolderName>/recruiter_kit_<Company>.md`

---

## End of /make-resume

Update session file Status:
- `Resume: Markdown DONE`
- `Cover Letter: PENDING`
- `Critique: PENDING`
- `Recruiter Kit: DONE`
- `Next: /make-cl output/<FolderName>/session_<name>.md`
- `Next Critique: /critique-resume output/<FolderName>/session_<name>.md`
- `DOCX/PDF: hand off to Claude Web (claude.ai) after edits approved`

Append one row to `session_learnings.md` Session Log:
`| [date] | [Job Title | Client | Vendor | Employer] | Resume Markdown DONE | [any token note] |`

### >>>>>> MANDATORY STOP <<<<<<
Present: resume markdown (for review), bullet audit table, recruiter kit summary.
**You MUST wait for the user's explicit text response before continuing.**

"Resume markdown done. Recruiter kit saved to output/<FolderName>/recruiter_kit_<Company>.md

Next steps (parallel workflow):
1. Run: /make-cl output/<FolderName>/session_<name>.md
   (Cover letter flows directly from Phase 0 framing — no re-read needed)
2. Then: /critique-resume output/<FolderName>/session_<name>.md
   (Scores resume + CL together)
3. Then: /edit-resume (applies tier-1 fixes to both resume + CL)
4. **DOCX + PDF: paste approved markdown into Claude Web (claude.ai) and ask Claude to generate the DOCX/PDF** — Claude Web produces better-formatted output than CLI"