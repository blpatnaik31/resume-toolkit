---
name: resume-toolkit
description: "Complete resume and CV builder toolkit for tailoring applications to job descriptions. Use this skill when the user wants to build, edit, or critique a resume or CV, generate a cover letter, optimize a LinkedIn profile, extract achievements from research papers, or set up a knowledge base from their work history. Triggers on: /make-resume, /edit-resume, /critique-resume, /make-cl, /new-session, /career-workbook, /optimize-linkedin, /setup-extract, /setup-build-kb, or any request to tailor a resume to a job posting, write a cover letter, critique a resume, optimize a LinkedIn profile, extract paper achievements, or build a resume knowledge base. This skill bundles nine sub-skills: career-workbook, new-session, make-resume, edit-resume, critique-resume, make-coverletter, optimize-linkedin, setup-extract, and setup-build-knowledgebase."
---

# Resume Toolkit

> Based on [ARPeeketi/claude-resume-kit](https://github.com/ARPeeketi/claude-resume-kit) (MIT) — the original anti-fabrication, knowledge-base-first resume system. This fork extends it as a Claude Code skill for PM/TPM/PO applications.

A complete resume and CV builder. This meta-skill bundles nine sub-skills — read the relevant one for your task:

| Command | Sub-skill file | What it does |
|---------|---------------|--------------|
| `/new-session` | `skills/new-session/SKILL.md` | Two-step entry point: confirm Career Workbook (Step 1), then display session prompt with 4 JD input options (Step 2) |
| `/career-workbook` | `skills/career-workbook/SKILL.md` | Display, review, and update the Career Workbook standalone |
| `/make-resume` | `skills/make-resume/SKILL.md` | Generate a tailored resume or CV from a job description |
| `/edit-resume` | `skills/edit-resume/SKILL.md` | Edit resume/CV or cover letter from critique feedback |
| `/critique-resume` | `skills/critique-resume/SKILL.md` | Score and critique a resume/CV against a JD |
| `/make-cl` | `skills/make-coverletter/SKILL.md` | Generate a tailored cover letter |
| `/optimize-linkedin` | `skills/optimize-linkedin/SKILL.md` | Suggest copy-paste LinkedIn profile changes (goal-first, then JD) |
| `/setup-extract` | `skills/setup-extract/SKILL.md` | Extract achievements from research papers into KB |
| `/setup-build-kb` | `skills/setup-build-knowledgebase/SKILL.md` | Synthesize extractions into resume-ready knowledge base |

## Standard Workflow (Job Application)

```
/new-session
  │
  ├── STEP 1: Career Workbook Review (skills/career-workbook/SKILL.md)
  │     Display timeline, certifications, verified metrics
  │     ├── "yes / looks good" → proceed to Step 2
  │     └── corrections provided → apply edits, loop back to confirm
  │
  └── STEP 2: Session Prompt (skills/new-session/SKILL.md)
        Display template + 4 JD input options:
          2a  Copy-paste JD inline
          2b  File path or folder (JDs/*.txt)
          2c  Upload .txt / .pdf / image
          2d  URL (Claude fetches + extracts JD)
        ↓ (user fills template + provides JD)

  /make-resume [JD]     ← Phase 0: JD analysis, comp, strategy, framing — STOP
                        ← Phase 1: Bullet plan — STOP
                        ← Phase 2: Generate resume markdown + bullet audit — STOP
       ↓
  ┌─────────────────────────────┬──────────────────────────────┐
  │  resume markdown done       │  /make-cl                    │
  │  (already complete)         │  (generate CL markdown now)  │
  └─────────────────────────────┴──────────────────────────────┘
       ↓  (both markdowns ready)
  /critique-resume              ← Score resume + CL together
       ↓
  ┌─────────────────────────────┬──────────────────────────────┐
  │  /edit-resume (resume)      │  /edit-resume (CL)           │  ← apply tier-1 fixes
  └─────────────────────────────┴──────────────────────────────┘
       ↓  (edits approved — CLI work ends here)
  ┌─────────────────────────────────────────────────────────────┐
  │  DOCX + PDF → use Claude Web (claude.ai)                    │
  │  Paste the final markdown → ask Claude to generate DOCX/PDF │
  │  Claude Web produces better-formatted output than CLI        │
  └─────────────────────────────────────────────────────────────┘
```

**Key principle:** CLI generates all markdown (resume + CL), critiques, and edits. DOCX and PDF are always produced in **Claude Web** (claude.ai) — paste the approved markdown and ask Claude to generate the DOCX/PDF. Claude Web renders significantly better output than CLI for document formatting.

## Writing Style Rules (ALWAYS ENFORCED)

Applies to every generated artifact: resume, CV, cover letter, and any outreach/recruiter email drafted as part of a session. Each sub-skill's own Safety Rules section restates this; treat it as non-negotiable regardless of which sub-skill produced the text.

- **No em dashes (—), anywhere.** Rewrite with a period, comma, colon, or parentheses instead. Before presenting any draft, grep the output for `—` and fix every hit — zero tolerance.
- **Write like a person, not a keyword-stuffed generator.** Vary sentence length and structure. Don't stack identical subject-verb openers bullet after bullet ("Led... Directed... Drove... Owned..." repeated down every line) — mix verbs, and let some bullets lead with the outcome or the object instead of the verb.
- **Bold sparingly.** One or two genuinely load-bearing terms per bullet (a metric, a proper noun) — not every noun phrase. Dense per-bullet bolding is the single biggest AI-output tell; when in doubt, bold less.
- **Prefer plain prose over parallel listicle structure** in summaries and cover letters. Write like the candidate describing their own work in conversation, not a marketing blurb assembled from bullet fragments.
- Do a final pass before presenting any draft: confirm zero em dashes, and skim for repetitive bullet openers — vary at least every third one.

## Effort Defaults (embedded — no /effort command required)

| Sub-skill | Effort | Reason |
|-----------|--------|--------|
| `/new-session` | low | Orchestration only; no generation |
| `/career-workbook` | low | Display + optional edit; no resume generation |
| `/make-resume` | medium | Structured phases; high effort adds no bullet quality |
| `/make-cl` | medium | Fixed structure; flows from Phase 0 framing |
| `/edit-resume` | medium | Targeted edits only |
| `/critique-resume` | high | Scoring depth benefits from thorough treatment |

## How to Invoke a Sub-skill

When the user invokes a command, read the corresponding sub-skill file from the `skills/` folder and follow its instructions exactly.

For example, if the user types `/make-resume JDs/Acme.txt`:
1. Read `skills/make-resume/SKILL.md`
2. Follow its startup, phase, and stop instructions

All sub-skills share the same project workspace conventions (`CLAUDE.md`, `session_learnings.md`, `Career_Workbook_Master_Lokapati.md`, `output/`, `JDs/`).

## Knowledge Base

- **Career Workbook:** `../career-progression/Career_Workbook_Master_Lokapati.md` — read ONCE at startup; never re-read mid-session
- **Session Learnings:** `session_learnings.md` — file existence map, token patterns, DOCX build workflow
- **Job Tracker:** `job_tracker.md` — application status; append a row after every new session

## Verified Workbook Metrics (canonical list — do not use metrics outside this list without explicit Workbook citation)

| Workbook | Verified Metrics |
|----------|-----------------|
| W1 (Alcon) | Metrics marked TBD — use directional language only ("improved", "reduced") |
| W2 (Baxter Sr 2022) | 30% product release delay reduction; 25% audit readiness improvement; 30% manual process reduction; 40% audit findings reduction; 500+ LoVs consolidated |
| W3 (GCHP) | 98% data accuracy; 15% audit trail improvement; 12% reconciliation error reduction; zero operational disruption |
| W4 (Baxter Jr 2019) | 30% faster product release; 100% supply chain traceability; 30% faster recall decision cycles; 75% manual reconciliation reduction; 30% onboarding time reduction; zero data loss |
| W5 (Kiddie Commute) | 20% CSAT improvement; 30% onboarding reduction; $5M Series A; 12% operational cost reduction |
| W6 (USD Recycling) | 25% drop-off time reduction; 75% laptop recovery rate; $50K revenue |
| W7 (Nike) | Supply chain KPI framework — 6 composite metrics; no quantified company outcomes |
| W8 (Infosys) | Merger context (TWC–Charter $78.7B) — no personal metrics confirmed |
| W9 (TCS) | 1M+ agent platform scale; specific % TBD |

**Fabrication rule:** If a metric is not in the table above, do NOT use it. Write directional language instead.
