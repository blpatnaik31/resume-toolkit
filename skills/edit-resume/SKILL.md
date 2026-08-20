---
name: edit-resume
description: "Edit existing resume/CV or cover letter from critique feedback and user suggestions"
---

# /edit-resume

**User input:** `$ARGUMENTS`

Parse `$ARGUMENTS`: First argument is the .md resume file path (required). A critique .md path is optional. Text in quotes is inline instructions.
- `/edit-resume output/Acme/resume_acme.md`
- `/edit-resume output/Acme/resume_acme.md output/Acme/critique_acme.md`
- `/edit-resume output/Acme/resume_acme.md "shorten Position 1 header, fill last page"`

If only .md path and no instructions: ask the user what to fix.

---

## Safety Rules (ALWAYS ENFORCED)

**Accuracy > Relevance > Impact > ATS > Brevity**

Read `config.md` Provenance Flags before editing (skip if absent; use Career Workbook as authority). Verify every claim against `Career_Workbook_Master_Lokapati.md`.

- Use the email from `Career_Workbook_Master_Lokapati.md` Personal Info header in all outputs
- Source ALL bullet content from `Career_Workbook_Master_Lokapati.md` (primary) or `resume_builder/experience/` files if they exist. Never fabricate.
- Resume bullets: ALL variable bullets must be 2L (CV: 2L/3L mix OK)
- Run `python3 resume_builder/helpers/char_count.py` after edits — the tool is authoritative
- **No em dashes (—) anywhere in the output.** Use a period, comma, colon, or parentheses instead. Grep for `—` after every edit pass and fix every hit, even if the em dash was already present before this edit.
- **Keep edits natural, not listy.** Don't introduce dense per-bullet bolding or repeated verb-first openers while editing. See the toolkit's Writing Style Rules (top-level `SKILL.md`) for the full standard.

### FIXED Sections — Refuse if Asked to Edit
Check `config.md` FIXED Sections if it exists; otherwise treat only header/contact/education/certifications as fixed. Say no and explain: these are template-locked across all outputs.

VARIABLE sections only: Summary, Technical Skills, Research Experience bullets/headers.

---

## User Input During Execution

If the user provides feedback, corrections, or suggestions at any point:
1. Acknowledge the input immediately
2. If it affects an already-applied edit: go back, fix it, re-run char count gate
3. If it changes the edit plan: update session file, adjust remaining edits
4. If it's a question: answer it, then continue from current step
5. Never restart a phase — resume from current position

---

## Startup

Read `resume_builder/reference/shared_ops.md` — Fresh Session Startup + Session File Derivation.
Read `CLAUDE.md` — check Active Sessions and KB Corrections.
Read `config.md` — load Provenance Flags, email, FIXED Sections, document preferences.
Find and read the session file (use derivation protocol from shared_ops.md).

**Recovery check:**
- Read session file, check for existing Edit N Status
- If Edit N Status shows IN_PROGRESS: read .tex, identify which edits are done, resume
- If no edit in progress: proceed to Phase 1

---

## Phase 1: Load Context

Read in this order:
1. **Session file** (`output/<FolderName>/session_<name>.md`) — note: Framing Strategy, Company Context, Bullet Plan, Edit History
2. `resume_builder/reference/resume_reference.md` — char limits, budgets, fixed sections
3. The .md resume file being edited
4. Critique file (if provided in `$ARGUMENTS`)
5. JD file (path from session file's JD Info section)
6. Estimate baseline page count from markdown density (sections × avg lines)
7. Run char count if helper exists: `python3 resume_builder/helpers/char_count.py -f [resume|cv] [file.md]`
   If absent: manually audit bullet line lengths.

**Record baseline in session file** under `## Edit [N] Baseline` (scan existing Edit History sections; next N = max existing + 1, or 1 if none):

```
## Edit [N] Baseline
- Pages: [N]
- Char violations: [list or "none"]
- Orphan violations: [list or "none"]
- White space last page: [N lines]
- Variable bullets: [N]
- Rendered lines: [N]
```

Progress: "Reading session file — [company], [role type] bundle..." / "Baseline: 2 pages, 0 char violations, 1 orphan..."

---

## Phase 2: Diagnose & Plan Edits

Gather change requests from THREE sources:
1. **User instructions** from `$ARGUMENTS` (highest priority)
2. **Critique file** (Tier 1 fixes first, then Tier 2)
3. **Auto-detected issues** from Phase 1 (char violations, orphans, page fill)

Cross-check against **session file framing strategy** — edits must stay consistent with decisions from `/make-resume`.

**For each change, classify:**
- **MODIFY:** Change text of existing bullet/summary/skills. Budget unchanged.
- **SWAP:** Replace one bullet with another. Budget unchanged if same variant.
- **ADD:** Insert new bullet. Budget increases by rendered lines.
- **REMOVE:** Drop a bullet. Budget decreases.
- **VARIANT CHANGE:** e.g., 2L → 3L. Budget changes by rendered line delta.
- **FIXED:** Blocked — show in plan with `[FIXED — cannot edit]` and explain why.

**Budget revalidation (if any change is ADD, REMOVE, SWAP-with-different-variant, or VARIANT CHANGE):**
Recalculate total variable bullets and rendered lines. Compare against budget from resume_reference.md.
If OVER budget: present overflow and ask user which bullet to drop or shorten.
Show: `Budget: [N] bullets ([M] rendered lines) vs target [T]. PASS/FAIL`

### Parallel Edit Mode (when critique covers both resume + CL)

If the critique file contains Tier 1 fixes for BOTH the resume and the CL:
1. Build two numbered edit plans: **Resume Edit Plan** and **CL Edit Plan**
2. Present both plans in a single STOP — user approves or modifies both at once
3. Execute resume edits first (all resume gates must pass), then CL edits (all CL gates must pass)
4. After both pass all gates → trigger Phase 5 DOCX finalization for both

If edit targets **cover letter only** (not resume/CV): note this — Phase 4 will use CL-specific gates. Load CL .md path from session file Output Files section.

### >>>>>> MANDATORY STOP — DO NOT PROCEED <<<<<<
Present numbered edit plan(s). Each item shows: what, why, source, classification (MODIFY/ADD/SWAP/FIXED).
If parallel mode: show Resume Edit Plan and CL Edit Plan as two separate numbered lists.
**You MUST wait for the user's explicit text response before continuing.**
Proceeding without confirmation may make unwanted edits that break package consistency.

---

## Phase 3: Load Reference Files (only confirmed edits)

Load ONLY what the confirmed edits need:

- **All edits:** `resume_builder/support/ai_fingerprint_rules.md` — scan for banned words/patterns before and after edits
- **Bullet expand/rewrite/add:** `resume_builder/experience/` files + matching bundle + `resume_builder/support/achievement_reframing_guide.md`
- **Summary rewrite:** Bundle (S2 summary guide) + `resume_builder/support/skills_taxonomy.md`
- **Cover letter edits:** `resume_builder/support/significance_*.md` + `resume_builder/reference/cl_reference.md`
- **Simple fixes** (orphans, headers, spacing): No extra files needed

---

## Phase 4: Execute Edits

Apply edits one section at a time. After each edited section:

1. Run char count gate (if helper exists):
   ```bash
   python3 resume_builder/helpers/char_count.py -f [resume|cv] output/<FolderName>/[file].md
   ```
2. Fix any OVER violations or orphans before next section
3. If a bullet expansion doesn't render as expected (1L when targeting 2L, or 3L), adjust immediately

Update session file Edit N Status after each individual edit:
- Edit 1 (orphan fix): DONE
- Edit 2 (Summary rewrite): IN_PROGRESS

### Resume/CV Verification Gates
| Gate | Check | If FAIL |
|------|-------|---------|
| Char count | No OVER violations (180–220 per bullet line) | Fix bullet before proceeding |
| Page fill | Resume: <= 3 lines white space estimated | Expand/trim variable bullets |
| Page count | Match Document Preferences | Trim/expand variable content |
| Orphan | 2L bullet last line >= 70% | Pad or trim |
| Title width | Position title + date fits 1 line | Shorten title |

### Cover Letter Verification Gates (if CL was edited)
| Gate | Check | If FAIL |
|------|-------|---------|
| Word count | Industry 250-300, Lab/Academic 350-450 | Trim/expand |
| Paragraph count | Industry 3, Lab/Academic 4 | Restructure |
| Anti-patterns | No generic opener, no defensive framing, no credential dump | Rewrite |
| Package cohesion | CL claims traceable to resume bullets, no contradictions | Fix |

After all edits, if DOCX rebuild needed, ask user for email confirmation first (same gate as in /make-resume).

Progress: "Editing Position 1 bullet 6 — was 184 chars, now 197..."

---

## Phase 5: Update Session File & Present

1. **Append Edit History** (use the N from Phase 1 baseline):
   ```
   ### Edit [N] ([date]): [short description]
   - Changes: [what changed]
   - Source: critique item # / user request / auto-detected
   - Verification: gates passed
   ```

2. **Compare against baseline:**

   | Metric | Before | After | Delta |
   |--------|--------|-------|-------|
   | Page count | [N] | [N] | [+/-] |
   | Char violations | [N] | [N] | [+/-] |
   | Orphans | [N] | [N] | [+/-] |
   | White space | [N] | [N] | [+/-] |

   Flag any metric that worsened.

3. **Update Status** — mark critique as STALE if edits made after last critique. Update Next.

4. **Update memory pointer** if status changed.

5. **Present:** Changes summary + delta table + compiled PDF.

### >>>>>> MANDATORY STOP <<<<<<
Show results. Wait for user approval or further edits.
**You MUST wait for the user's explicit text response before continuing.**

### When user approves / says "looks good" / finalizes:

**Check session file DOCX status:**
- If `Phase 3: DOCX DONE` already → skip to file organization
- If DOCX has not been generated yet → proceed to DOCX + PDF Finalization below

---

## DOCX + PDF Finalization (runs once, after all edits approved)

Generate artifacts for both resume and CL in sequence.

**EMAIL CONFIRMATION GATE**

Show the user:
- Email extracted from Career Workbook Personal Info header
- Confirm: "Finalizing DOCX + PDF for [Company]. Email to include: [email]. Confirm or provide a different email."

**Wait for user's explicit confirmation.** Once confirmed:

**Resume DOCX + PDF:**
1. Generate `output/<FolderName>/build_resume_docx.py` using the confirmed-working python-docx pattern from `/make-resume` Phase 3 (margins, fonts, job header table, bullet indent — all settings must match exactly)
2. Run:
   ```bash
   python3 output/<FolderName>/build_resume_docx.py
   ```
3. Convert to PDF:
   ```bash
   libreoffice --headless --convert-to pdf --outdir output/<FolderName>/ output/<FolderName>/Lokapati_Bhogela_Resume_<Company>.docx
   ```

**CL DOCX + PDF:**
4. Generate `output/<FolderName>/build_cl_docx.py` using the matching CL style
5. Run:
   ```bash
   python3 output/<FolderName>/build_cl_docx.py
   ```
6. Convert to PDF:
   ```bash
   libreoffice --headless --convert-to pdf --outdir output/<FolderName>/ output/<FolderName>/Lokapati_Bhogela_CoverLetter_<Company>.docx
   ```

**Verify all 4 artifacts exist:**
- `Lokapati_Bhogela_Resume_<Company>.docx` ✓
- `Lokapati_Bhogela_Resume_<Company>.pdf` ✓
- `Lokapati_Bhogela_CoverLetter_<Company>.docx` ✓
- `Lokapati_Bhogela_CoverLetter_<Company>.pdf` ✓

Update session file:
- Status: `Resume: DONE (DOCX + PDF)`
- Status: `Cover Letter: DONE (DOCX + PDF)`

Run file organization from `resume_builder/reference/shared_ops.md` — Finalization check.

"Package complete in output/<FolderName>/ — [list all files]"