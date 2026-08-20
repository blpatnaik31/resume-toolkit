---
name: make-coverletter
description: "Generate a tailored cover letter from an existing session file and finished resume/CV"
---

**User input:** `$ARGUMENTS`

Parse `$ARGUMENTS`:
- Session file path (e.g., `output/Acme/session_acme_engineer.md`) → read that session file
- Session name (e.g., `acme_engineer`) → find session file via shared_ops.md derivation
- Empty → check `CLAUDE.md` Active Sessions for latest

---

## Safety Rules (ALWAYS ENFORCED)

**Accuracy > Relevance > Impact > ATS > Brevity**

Read `config.md` Provenance Flags before generating any content (skip if absent; use Career Workbook). Verify every claim against the Career Workbook.

- Use the email from Career Workbook Personal Info header in all outputs
- CL deepens what resume presents — never introduces new claims not traceable to resume bullets
- All metrics must appear in the Verified Workbook Metrics table in the parent `SKILL.md`
- Para 1 must never open with "I am writing to apply" or any generic opener
- The CL flows directly from Phase 0 Framing Strategy — no new research required when the session file is complete
- **No em dashes (—) anywhere in the letter.** Use a period, comma, colon, or parentheses instead. Grep the draft for `—` before presenting it and fix every hit.
- **Write in natural, first-person prose, not a keyword list.** Vary sentence length, avoid stacking the same sentence pattern paragraph after paragraph, and bold sparingly if at all. See the toolkit's Writing Style Rules (top-level `SKILL.md`) for the full standard.

---

## Writer Role Selection

Writer Role is set in the session file by `/make-resume`. Read it from there. If absent (CL-only run), determine it now using the same table.

| Seniority signals in JD title / level | Role |
|---|---|
| Director, Senior Director, VP, SVP, EVP, Managing Director, Head of, Principal (strategic scope), Partner, C-suite | **CERW** — Certified Executive Résumé Writer |
| Manager, Senior Manager, Lead, Staff, Senior IC, Specialist, Analyst, Individual Contributor | **CPRW** — Certified Professional Résumé Writer |

**CERW cover letter philosophy** — Strategic scope first, execution second:
- Open with organizational impact or business outcome, not a task-level hook
- Executive register throughout: "Orchestrated", "Spearheaded", "Architected", "Drove enterprise-wide…"
- Frame as change agent / thought leader positioned for Director+ step
- Body paragraphs: impact thesis → evidence from strategic scope → "why them" at leadership level
- No tool lists or task-level detail; elevate cross-functional influence and program scope

**CPRW cover letter philosophy** — Achievement-forward, metrics-anchored:
- Open with a quantified hook tied to a direct JD requirement
- Weave ATS keywords and technical tools naturally in first paragraph
- Body paragraphs: skills match → metric evidence → execution depth
- Close with specific "why them" angle grounded in company context
- JD keyword density matters; mirror JD language in topic sentences

---

## User Input During Execution

If the user provides feedback, corrections, or suggestions at any point:
1. Acknowledge the input immediately
2. If it affects already-written content: fix it, re-verify word count and anti-patterns
3. If it changes the framing: note the change in session file Framing Strategy
4. Never restart — resume from current position

---

## Startup

Skip `resume_builder/reference/shared_ops.md` if absent.

Then:
1. Read `CLAUDE.md` — check Active Sessions and KB Corrections
2. Read `config.md` — load Provenance Flags, email, role types (skip if absent)
3. Find and read the session file
4. **Recovery check:**
   - If CL Status is DONE → "CL already generated. Run `/critique-resume` next." Show next command. Stop.
   - If CL Status is IN_PROGRESS → check if CL .md exists, offer to resume or regenerate
   - If Resume Status shows `Markdown DONE` → resume markdown is ready; proceed to Phase 1 (DOCX not required)
   - If Resume Status is not DONE and no resume .md exists → "Resume not yet generated. Run `/make-resume` first." Stop.
   - If CL Status is PENDING → proceed to Phase 1

**Fast path:** If the session file has a complete Cover Letter Plan and Framing Strategy from Phase 0 of `/make-resume`, skip all web searches — generate directly from those sections. The CL is designed to flow from Phase 0 without re-researching. Only do fresh web searches if the session file's Company Context is missing or marked stale.

---

## Phase 1: Load Context

Read in this order:
1. **Session file** — specifically: **Writer Role** (set by `/make-resume`; if absent, determine using Writer Role Selection above and note it), Company Context, Cover Letter Plan, Framing Strategy (including Reframing Map), ATS Keywords, Compensation Analysis
2. **Resume .md** — path from session file Output Files. Read to confirm which metrics and bullets the CL must complement. Markdown is sufficient — DOCX is not required.
3. `resume_builder/reference/cl_reference.md` — CL format rules, paragraph templates, anti-patterns (skip if absent; use defaults below)
4. `resume_builder/support/ai_fingerprint_rules.md` — Banned words, structural rules (skip if absent)
5. Optional: `resume_builder/bundles/bundle_[role_type].md` Section 5 if it exists

**CL format defaults (use when cl_reference.md absent):**
- Industry: 3 paragraphs + close, 280–320 words
- Para 1: specific company hook — JD/company language quoted; no generic opener
- Para 2: 2–3 Workbook-verified metrics tied to JD requirements by name
- Para 3: Why this role/company/moment + location fit + call to action
- Close: Name + credentials + contact

Update session file Status: `Cover Letter: IN_PROGRESS`

Progress: "Loading CL context from session file — [company], [institution type], [word target]..."

---

## Phase 2: Generate Cover Letter

**Detect institution type** from session file Cover Letter Plan:
- Industry → 3 paragraphs, 250-300 words
- National Lab → 4 paragraphs, 350-450 words
- Academic → 4 paragraphs, 350-450 words (postdoc) or 450-650 words (faculty)

**Apply writer role philosophy** (from session file Writer Role) throughout the CL — CERW: scope-first strategic register, executive framing, business outcomes before task detail; CPRW: achievement-forward, metrics-anchored, JD keyword density in topic sentences.

**Generate CL following cl_reference.md paragraph structure:**
- Use significance files for field-context depth (NOT resume bullet text)
- Use session file CL hooks and "why them" angle
- Ensure every major claim is traceable to a resume/CV bullet
- Open with a specific reference to their work — no generic openers
- Weave credentials into body paragraphs, not closing

Save to `output/<FolderName>/Lokapati_Bhogela_CoverLetter_<Company>.md`

Progress: "Writing [institution type] cover letter — [N] paragraphs, targeting [N] words..."

### CL Hook Verification Gate (MANDATORY before presenting to user)

Web-search every hook used in the CL (industry: product, technology, or company news referenced).
Present evidence as: **Claim** → **Evidence** → **Source URL**. Flag any unverified item.
Do NOT present draft until all hooks are verified or flagged.

---

## Phase 3: Verify & Markdown Review Stop

**Content gates:**

| Gate | Check | If FAIL |
|------|-------|---------|
| Word count | Industry 250-300, Lab/Academic 350-450 | Trim/expand |
| Anti-patterns | No generic opener, no defensive framing, no credential dump | Rewrite |
| Package cohesion | CL claims traceable to resume bullets, no contradictions | Fix |

Update session file:
- Add CL markdown path to Output Files
- Status: `Cover Letter: Markdown DONE (DOCX pending — deferred to after critique + edit)`
- Add Next Critique command

Progress: "CL done — [N] words. Package cohesion verified."

### >>>>>> MANDATORY STOP — DO NOT PROCEED <<<<<<
Present: CL markdown + summary (word count, page count, key hooks used, hook verification results).
**You MUST wait for the user's explicit text response before continuing.**

If user requests changes: apply them, re-run gates, re-verify. Update session file.
If user approves: present next command.

**DOCX generation is deferred** — it runs after `/edit-resume` applies critique fixes.

"Cover letter markdown done. Next steps:
1. Run: /critique-resume output/<FolderName>/session_<name>.md
   (Scores resume + CL together — DOCX not required)
2. Then: /edit-resume (applies tier-1 fixes to both resume + CL)
3. DOCX + PDF for both generated last, after edits are approved"

---

## Phase 4: DOCX + PDF (runs after /edit-resume completes)

**Trigger:** User says "generate DOCX", "make PDF", "finalize", or `/edit-resume` triggers finalization for both resume and CL.

**EMAIL CONFIRMATION GATE**

Show the user:
- Email extracted from Career Workbook Personal Info header
- Confirm: "Generating CL DOCX for [Company]. Email to include: [email]. Confirm or provide a different email."

**Wait for user's explicit confirmation.** Once confirmed:

1. Generate `output/<FolderName>/build_cl_docx.py` — python-docx script. Use the **v3 confirmed-working pattern** exactly:

   **Imports & design tokens:**
   ```python
   import os, subprocess
   from docx import Document
   from docx.shared import Pt, Inches, RGBColor
   from docx.enum.text import WD_ALIGN_PARAGRAPH
   from docx.oxml.ns import qn
   from docx.oxml import OxmlElement

   FONT = "Calibri"
   NAVY = RGBColor(0x1F, 0x49, 0x7D)   # #1F497D
   MID  = RGBColor(0x55, 0x55, 0x55)   # #555555
   DARK = RGBColor(0x21, 0x21, 0x21)   # #212121
   ```

   **Margins:** `top = bottom = left = right = Inches(0.875)` (US Letter, 8.5"×11").
   Also set `sec.page_width = Pt(612)` and `sec.page_height = Pt(792)` explicitly.
   Reset Normal style: `space_before = space_after = Pt(0)`.

   **`sp(para, before, after, line_pts=13)` helper** — sets `space_before`, `space_after`, `line_spacing` in Pt.

   **`fnt(run, size=10, bold=False, color=None)` helper** — sets font name, size, bold, and `color.rgb` (defaults to DARK).

   **`add_bottom_border(para, color_hex, size=8)` helper** — writes `w:pBdr/w:bottom val="single"` with given hex color onto the paragraph.

   **`add_rich_para(doc, segments, justify=False, before=0, after=10, line_pts=13)` helper:**
   - `segments` is a list of `(text, is_bold)` tuples
   - Creates one paragraph; calls `sp()` and optionally sets `JUSTIFY` alignment
   - Loops segments, calls `fnt(run, size=10, bold=is_bold)` per run
   - Returns the paragraph

   **Name header paragraph:**
   - `p.alignment = CENTER`; `sp(before=0, after=3, line_pts=18)`
   - `add_bottom_border(p, "1F497D", size=8)` on the name paragraph itself
   - Single run: `"Lokapati Patnaik Bhogela  |  PMP  |  CSPO  |  MBA"`, `fnt(size=15, bold=True, color=NAVY)`

   **Contact line paragraph:**
   - `p.alignment = CENTER`; `sp(before=3, after=14, line_pts=12)`
   - Single run: location | email | LinkedIn, `fnt(size=9, color=MID)`

   **Date paragraph:** `sp(before=0, after=3, line_pts=13)`; `fnt(size=10)`, text = `"May YYYY"`.

   **Salutation paragraph:** `sp(before=3, after=10, line_pts=13)`; `fnt(size=10)`; text = `"Dear Hiring Team,"`.

   **Body paragraphs** — each via `add_rich_para(doc, segments, justify=True, before=0, after=10)`:
   - Para 1: Hook + positioning (role title bolded)
   - Para 2: Proof points (company names and key achievements bolded inline)
   - Para 3: Fit + call to action (key phrases bolded)

   **Closing block:**
   - Thanks line: `sp(before=0, after=14, line_pts=13)`, `fnt(size=10)`
   - "Sincerely,": `sp(before=0, after=28, line_pts=13)`, `fnt(size=10)`
   - Signature name: `fnt(size=10.5, bold=True, color=NAVY)`; `sp(before=0, after=2, line_pts=13)`
   - Credentials: `"PMP | CSPO | MBA"`, `fnt(size=9, color=MID)`; `sp(before=0, after=2, line_pts=12)`
   - Email/LinkedIn: `fnt(size=9, color=MID)`; `sp(before=0, after=0, line_pts=12)`

   **PDF conversion** — inline via `subprocess.run` at the end of the script (no separate step needed):
   ```python
   subprocess.run(["libreoffice", "--headless", "--convert-to", "pdf",
                   "--outdir", OUT_DIR, DOCX_OUT])
   ```
   Print `✓ DOCX saved:` and `✓ PDF saved:` on success; print stderr and fix hint on failure.

   **Word count:** Target 250–320 words for industry CL.

2. Run:
   ```bash
   python3 output/<FolderName>/build_cl_docx.py
   ```
3. Convert to PDF:
   ```bash
   libreoffice --headless --convert-to pdf --outdir output/<FolderName>/ output/<FolderName>/Lokapati_Bhogela_CoverLetter_<Company>.docx
   ```
4. Verify .docx and .pdf exist in output folder.

Update session file:
- Status: `Cover Letter: DONE (DOCX + PDF)`

**Do NOT trigger file organization** — that happens after `/critique` approval.