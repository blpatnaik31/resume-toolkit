---
name: new-session
description: "Two-step entry point for a new job application. Step 1: display and confirm the Career Workbook (with optional updates). Step 2: display the session prompt template with four JD input options. Triggers when user starts a new application, pastes a JD, or says /new-session."
---

# /new-session

**Purpose:** Gate every new job application through two steps:
1. Review and confirm the Career Workbook is current (with optional edits)
2. Display the session prompt template with four ways to provide the JD

**Effort:** low — no resume generation in this skill.

---

## On Invocation

Check `CLAUDE.md` Active Sessions — if a session for this company already exists, show its current status and ask:
> "A session for [Company] already exists (Status: [X]). Continue it with `/make-resume` or start fresh?"

If continuing: hand off to `/make-resume`. If fresh: proceed to Step 1.

---

## STEP 1 — Career Workbook Review

Read `skills/career-workbook/SKILL.md` and execute it fully (Step 1 of that skill). Do not display the session prompt until the user confirms the workbook.

**Summary:** The career-workbook skill will:
- Display the Career Timeline, certifications, KB corrections, and verified metrics as a compact table
- Ask the user to confirm or provide changes
- If changes: apply them, loop back for re-confirmation (Step 1b)
- If confirmed: hand back here to execute Step 2

---

## STEP 2 — Session Prompt

Once the Career Workbook is confirmed, display this header:

> **Step 2 of 2 — Session Setup**
>
> Fill in the fields below. For the JD, choose one of the four input methods:
>
> | Option | How to provide the JD |
> |--------|----------------------|
> | **2a** | **Copy-paste** — paste the JD text directly into the `JD:` field below |
> | **2b** | **File path** — provide a local path, e.g. `JDs/Acme_SeniorPM.txt` or a folder `JDs/` |
> | **2c** | **Upload** — attach a `.txt`, `.pdf`, or image file; Claude will extract the text |
> | **2d** | **URL** — paste the job posting URL; Claude will fetch and extract the JD |

Then display the Master Prompt Template verbatim in a code block:

````
# JOB APPLICATION SESSION

## Inputs
JD: {paste JD text here — or use options 2b/2c/2d above}
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

## JD Input Handling (Option Resolution)

After the user submits, resolve the JD source before handing off to `/make-resume`:

**Option 2a — Inline text**
JD text is pasted directly. Save to `JDs/temp_{COMPANY}.txt` if no JDs/ file was named, then proceed.

**Option 2b — File path or folder**
- Single file (e.g., `JDs/Acme.txt`): read the file directly.
- Folder (e.g., `JDs/`): list files, ask user to confirm which one.
- Relative paths are resolved from the job-applications project root.

**Option 2c — File upload (txt / pdf / image)**
- `.txt`: read directly.
- `.pdf`: use the file-to-markdown converter (`scripts/file-to-markdown-office.py`) if available, otherwise read via the Read tool with PDF support.
- Image (`.png`, `.jpg`, `.jpeg`): use the Read tool (multimodal) to extract text, then display the extracted JD for confirmation before proceeding.
- After extraction, save to `JDs/temp_{COMPANY}.txt` and confirm with user.

**Option 2d — URL**
- Fetch the URL using WebSearch or WebFetch.
- Extract the job title, company, requirements, and responsibilities from the page.
- Display extracted JD text to user for confirmation (truncated to first 500 chars + "...").
- Save confirmed text to `JDs/temp_{COMPANY}.txt`.

**All options:** once JD text is resolved and confirmed, proceed to session file creation below.

---

## Quick-Reference Tables

**Session type:**
| TYPE | Comp model | CL required? |
|------|-----------|-------------|
| FTE (direct) | Salary; no vendor layers | Yes |
| C2C (1 vendor) | 3-layer model | Optional |
| C2C (2 vendors) | 4-layer model; ⚠️ duplicate sub check | Optional |
| LTC | Hourly; check LTC-to-hire option | Optional |
| Inbound | Start with recruiter reply; JD pending | No (wait for JD) |

**Sponsorship:**
- `yes` = H-1B transfer is acceptable to this employer
- `no` = employer cannot sponsor → ⛔ gate fires; requires explicit override
- `unknown` = proceed; flag in session file

---

## Session File Stub

After the user submits the filled template and the JD is resolved, create the session file:

```bash
mkdir -p output/<DerivedFolderName>/
```

Write `output/<DerivedFolderName>/session_<DerivedName>.md`:

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

Then hand off to `/make-resume` — phases live in `skills/make-resume/SKILL.md`.
