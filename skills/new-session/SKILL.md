---
name: new-session
description: "Display the master prompt template for a new job application session under the job-applications project. Triggers when user starts a new application, pastes a JD, or says /new-session."
---

# /new-session

**Purpose:** Display the master prompt template so the user can fill in the 9 placeholders and paste their JD. This is the mandatory entry point for any new job application. Do not run `/make-resume` without first completing this template.

**Effort:** low — display only; no Career Workbook read required.

---

## On Invocation

1. Check `CLAUDE.md` Active Sessions — if a session for this company already exists, show its current status and ask if the user wants to continue it (→ `/make-resume`) or start a fresh one.
2. Display the Master Prompt Template below verbatim in a code block.
3. Remind the user: "Fill in all 9 fields, paste your full JD into the JD field, then submit. The session will run Phases 0–9 automatically."
4. Do NOT read the Career Workbook yet — that happens in Phase 1 of `/make-resume`.

---

## Master Prompt Template

Display exactly this block (do not paraphrase or summarize it):

````
# JOB APPLICATION SESSION

## Inputs
JD: {paste full job description here}
COMPANY: {company name}
TITLE: {exact job title from JD}
LOCATION: {city, state — remote / hybrid / onsite}
TYPE: {FTE | C2C | W2-contract | LTC}
COMP: {posted range — or "not posted"}
RECRUITER: {recruiter name, firm, message — or "none"}
REFERRAL: {yes | no}
SPONSORSHIP: {yes = H-1B transfer OK | no = cannot sponsor | unknown}

## Notes (optional)
FOCUS: {e.g., "emphasize AWS", "downplay startup roles" — or leave blank}
DEADLINE: {application deadline date — or "none"}
VENDOR_LAYERS: {3-layer | 4-layer | direct — for comp model}

## Execution
Read Career_Workbook_Master_Lokapati.md once. Do not re-read it during later phases.
All facts, metrics, employers, and project names must come from the Workbook.
Run Phases 0–9 in order. Present each phase output before the next.
Ask clarifying questions ONLY if a critical input is missing.
State assumptions explicitly when proceeding without complete information.

PHASE 0  — Viability Gate: sponsorship ⛔/✅, location fit, C2C/FTE mismatch check
PHASE 1  — JD Analysis: Requirements Table + ATS Keyword Bank + Gap Assessment
PHASE 2  — Comp Analysis: bill rate / 3-layer / 4-layer C2C / W2 equivalent / ask rate
PHASE 3  — Resume Strategy: Lead Narrative + Reframing Map + Bullet Plan
PHASE 4  — Resume Generation (Markdown, per resume rules)
PHASE 5  — Bullet Audit (char count, ATS keywords, metric sources — fix silently, report changes)
PHASE 6  — Cover Letter (280–320 words; specific company hook; Workbook-verified metrics only)
PHASE 7  — Recruiter Kit: 150-word email + 300-char LinkedIn note + 80-word voicemail + 5 talking points
PHASE 8  — Job Tracker Row (Markdown table row + JSON object)
PHASE 9  — Quality Review Checklist (fix all failures before presenting artifacts)

CLI produces markdown only. DOCX + PDF: paste approved markdown into Claude Web (claude.ai).
Effort: medium for Phases 1–8. High effort for any standalone /critique-resume.
````

---

## After Display

Say:

> **Fill in all fields above and paste your full JD into the `JD:` field.**
>
> **Session type quick-reference:**
> | TYPE | Comp model | CL required? |
> |------|-----------|-------------|
> | FTE (direct) | Salary; no vendor layers | Yes |
> | C2C (1 vendor) | 3-layer model | Optional |
> | C2C (2 vendors) | 4-layer model; ⚠️ duplicate sub check | Optional |
> | LTC | Hourly; check LTC-to-hire option | Optional |
> | Inbound | Start with recruiter reply; JD pending | No (wait for JD) |
>
> **Sponsorship quick-reference:**
> - `yes` = H-1B transfer is acceptable to this employer
> - `no` = employer cannot sponsor → ⛔ gate fires; requires your explicit override
> - `unknown` = proceed; flag in session file

---

## Session File Stub

After the user submits the filled template, immediately create the session file before Phase 0 runs:

```bash
mkdir -p output/<DerivedFolderName>/
```

Write `output/<DerivedFolderName>/session_<DerivedName>.md` with this header:

```markdown
# Session: {TITLE} | {CLIENT if any} | {VENDOR if any} | {COMPANY}

**Created:** {today's date}
**Status:** Phase 0: PENDING

## Inputs
- Company: {COMPANY}
- Title: {TITLE}
- Location: {LOCATION}
- Type: {TYPE}
- Comp: {COMP}
- Recruiter: {RECRUITER}
- Referral: {REFERRAL}
- Sponsorship: {SPONSORSHIP}
- Focus: {FOCUS or "none"}
- Deadline: {DEADLINE or "none"}
- Vendor Layers: {VENDOR_LAYERS}

## JD Source
{first 200 chars of JD...}
```

Then hand off to `/make-resume` — do not repeat Phase 0 instructions here; they live in `skills/make-resume/SKILL.md`.
