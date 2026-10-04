# /optimize-linkedin — LinkedIn Profile Optimizer

## Purpose

Suggest targeted, copy-paste-ready changes to a LinkedIn profile that align it with:
1. **Main Goal** (PRIMARY — takes precedence in ALL conflicts)
2. Job Description keywords (SECONDARY — enhances but never overrides goal)

Never remove or compromise existing experience. Every suggestion is additive or a reframe.

---

## Invocation

```
/optimize-linkedin [profile_path] [jd_path_or_inline] "Main Goal: [statement]"
```

**Examples:**
```
/optimize-linkedin LinkedIn_Profile.pdf JDs/CustomerSupportManager.txt "Main Goal: Position as a Customer Support Program Manager with 10+ years ITSM/SLA delivery experience"
/optimize-linkedin LinkedIn_Profile.pdf "JD inline text here" "Main Goal: Senior TPM in healthcare IT / digital health"
```

**Arguments:**
| Argument | Required | Notes |
|---|---|---|
| `profile_path` | YES | Path to LinkedIn PDF export or plain text profile |
| `jd_path_or_inline` | YES | Path to JD file, or quoted inline JD text |
| `Main Goal` | YES | Must be a quoted string starting with "Main Goal:" |

If any argument is missing, ask before proceeding.

---

## Phase 0: Parse Inputs

1. Read the LinkedIn profile (PDF or text).
2. Read the JD.
3. Extract and state the Main Goal clearly.
4. **Precedence rule:** Where Main Goal and JD keywords conflict — e.g., JD wants "operations analyst" framing but Main Goal is "Senior PM" — **Main Goal wins every time**.

---

## Phase 1: Profile Audit

Build an internal audit table before writing any suggestions:

| Section | Current State | Main Goal Gap | JD Gap |
|---|---|---|---|
| Headline | [current text] | [what's missing for goal] | [what's missing for JD] |
| Summary | [first 3 lines] | [goal framing gap] | [JD keyword gap] |
| Top Skills | [listed skills] | [skills missing for goal] | [skills missing for JD] |
| [Company] | [current summary] | [goal signal missing] | [JD signal missing] |

**Main Goal gaps are mandatory to fix. JD gaps are opportunistic — add only if they don't dilute goal signal.**

---

## Phase 2: Generate Suggestions

Produce suggestions in this exact order and format. Each block is labeled and copy-paste ready.

### 2.1 Headline

- Max 220 characters (LinkedIn hard limit).
- Must lead with the Main Goal role title or positioning.
- Include top 2–3 credential signals (e.g., PMP, years of experience, industry).
- JD keywords added only if they fit naturally after goal signal.

**Output format:**
```
--- HEADLINE ---
[Exact text — copy this into LinkedIn]
Character count: [N]/220
Why: [one sentence rationale — goal alignment + top credential signals]
```

### 2.2 Summary (About Section)

- Opens with Main Goal positioning sentence.
- Paragraph 2: top 3 quantified outcomes most relevant to goal.
- Paragraph 3: industries, methodologies, tools (JD keyword layer — goal-safe only).
- Closes with availability / location signal.
- Target: 300–500 words (LinkedIn shows ~300 before "see more").
- No buzzwords without backing ("results-driven" etc. are banned unless quantified).

**Output format:**
```
--- SUMMARY ---
[Full summary text — copy this into the About field]
Why: [2 sentences — what goal framing this establishes + what JD signals it adds]
```

### 2.3 Top Skills

- LinkedIn shows top 5 skills pinned; total cap is 50.
- Reorder or replace to surface goal-aligned skills in the top 5.
- Suggest up to 10 additional skills to add to the full list.
- Never suggest removing an existing skill — only additions and reordering.

**Output format:**
```
--- TOP SKILLS (reorder to pin these 5) ---
1. [Skill]
2. [Skill]
3. [Skill]
4. [Skill]
5. [Skill]

--- ADD TO SKILLS LIST (up to 10 additions) ---
[Skill], [Skill], [Skill] ...

Why: [one sentence — what goal signal this top-5 order sends]
```

### 2.4 Per-Position Additions

LinkedIn hard limit: **2,000 characters per position description** (including spaces).

For EACH position where goal signal or JD signal is absent or weak:
1. Estimate the character count of the existing description.
2. Compute the **character budget**: `2000 - existing_char_count`.
3. If budget ≥ 150: write an addition that fits within the budget.
4. If budget < 150 but the existing description has redundancy: suggest a targeted trim + addition, staying within 2,000 total.
5. If budget < 0 (existing is already over 2,000): flag it — do not add, note what to trim first.

- Do NOT rewrite the existing description unless it is corrupted/garbled (see FIX block).
- Add a SHORT paragraph (2–4 sentences) at the end of the existing entry, within budget.
- Frame additions as elaborations, not replacements.
- If existing description has errors (garbled text, formatting corruption), note the fix separately.

**Output format per position:**
```
--- [COMPANY] ([DATE RANGE]) ADDITION ---
Estimated existing length: ~[N] chars | Budget remaining: ~[N] chars
[Exact text to append — fits within budget]
Why: [one sentence — what goal/JD signal this adds]

--- [COMPANY] FIX (if applicable) ---
[Description of what's corrupted / wrong + clean replacement text]
[Total replacement char count: N/2000]
```

Process positions in reverse chronological order (most recent first). Skip positions that already have strong goal alignment.

### 2.5 Summary of Changes

End with a compact table:

| Section | Change Type | Goal Signal Added | JD Signal Added |
|---|---|---|---|
| Headline | Rewrite | [Y/N — what] | [Y/N — what] |
| Summary | Rewrite | [Y/N — what] | [Y/N — what] |
| Top Skills | Reorder + Add | [Y/N — what] | [Y/N — what] |
| [Company] | Addition | [Y/N — what] | [Y/N — what] |
| ... | ... | ... | ... |

---

## Safety Rules

1. **Accuracy > Relevance.** Never add a claim that isn't in the profile, Career Workbook, or stated by the user.
2. **No fabrication.** If the profile doesn't mention a tool or metric, do not invent it.
3. **Non-destructive.** Every suggestion is additive. Existing content is preserved verbatim unless it contains errors.
4. **Goal precedence is absolute.** If the JD asks for framing that conflicts with the Main Goal (e.g., "staff augmentation" vs. "program leadership"), drop the JD framing.
5. **LinkedIn character limits.** Headline: 220. Position title: 100. Position description: 2,000 per role. Summary: 2,600.
6. **No generic filler.** "Passionate about," "results-driven," "proven track record" — banned unless followed immediately by a quantified outcome.

---

## Career Workbook Integration

If the user has a Career Workbook at `Career & Certifications/Career_Workbook_Master_Lokapati.md`:
- Cross-reference any metrics or claims against the Workbook before including.
- If a claim appears in the LinkedIn profile but NOT in the Workbook, flag it (do not remove — flag for user review).
- If a strong outcome IS in the Workbook but NOT in the LinkedIn profile, recommend adding it under the relevant position.

---

## Output Delivery

- All suggestions are delivered as plain text, copy-paste ready.
- No markdown tables inside the copy-paste blocks (LinkedIn renders plain text).
- Separate each labeled block with `---` dividers for easy scanning.
- Do not summarize what you did after delivering — the labeled blocks speak for themselves.

---

## Linked Skills

This skill complements:
- `/make-resume` — resume version of the same positioning
- `/critique-resume` — scoring framework that informs what signals are missing
- `/make-cl` — cover letter using the same goal framing
