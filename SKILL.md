---
name: resume-toolkit
description: "Complete resume and CV builder toolkit for tailoring applications to job descriptions. Use this skill when the user wants to build, edit, or critique a resume or CV, generate a cover letter, optimize a LinkedIn profile, extract achievements from research papers, or set up a knowledge base from their work history. Triggers on: /make-resume, /edit-resume, /critique-resume, /make-cl, /new-session, /optimize-linkedin, /setup-extract, /setup-build-kb, or any request to tailor a resume to a job posting, write a cover letter, critique a resume, optimize a LinkedIn profile, extract paper achievements, or build a resume knowledge base. This skill bundles eight sub-skills: new-session, make-resume, edit-resume, critique-resume, make-coverletter, optimize-linkedin, setup-extract, and setup-build-knowledgebase."
---

# Resume Toolkit

> Based on [ARPeeketi/claude-resume-kit](https://github.com/ARPeeketi/claude-resume-kit) (MIT) — the original anti-fabrication, knowledge-base-first resume system. This fork extends it as a Claude Code skill for PM/TPM/PO applications.

A complete resume and CV builder. This meta-skill bundles eight sub-skills — read the relevant one for your task:

| Command | Sub-skill file | What it does |
|---------|---------------|--------------|
| `/new-session` | `skills/new-session/SKILL.md` | Display master prompt template for a new job application session |
| `/make-resume` | `skills/make-resume/SKILL.md` | Generate a tailored resume or CV from a job description |
| `/edit-resume` | `skills/edit-resume/SKILL.md` | Edit resume/CV or cover letter from critique feedback |
| `/critique-resume` | `skills/critique-resume/SKILL.md` | Score and critique a resume/CV against a JD |
| `/make-cl` | `skills/make-coverletter/SKILL.md` | Generate a tailored cover letter |
| `/optimize-linkedin` | `skills/optimize-linkedin/SKILL.md` | Suggest copy-paste LinkedIn profile changes (goal-first, then JD) |
| `/setup-extract` | `skills/setup-extract/SKILL.md` | Extract achievements from research papers into KB |
| `/setup-build-kb` | `skills/setup-build-knowledgebase/SKILL.md` | Synthesize extractions into resume-ready knowledge base |

## Standard Workflow (Job Application)

```
/new-session            ← Display master prompt template; fill placeholders; paste JD
       ↓
  /make-resume [JD]     ← Phase 0: JD analysis, comp, strategy, framing — STOP for confirmation
                        ← Phase 1: Bullet plan — STOP for confirmation
                        ← Phase 2: Generate resume markdown + bullet audit — STOP for review
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

## Effort Defaults (embedded — no /effort command required)

| Sub-skill | Effort | Reason |
|-----------|--------|--------|
| `/make-resume` | medium | Structured phases; high effort adds no bullet quality |
| `/make-cl` | medium | Fixed structure; flows from Phase 0 framing |
| `/edit-resume` | medium | Targeted edits only |
| `/critique-resume` | high | Scoring depth benefits from thorough treatment |
| `/new-session` | low | Display only |

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
