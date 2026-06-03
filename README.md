# Resume Toolkit — Claude Code Skill

> **Based on [ARPeeketi/claude-resume-kit](https://github.com/ARPeeketi/claude-resume-kit)** by [@ARPeeketi](https://github.com/ARPeeketi) — the original knowledge-base-first, anti-fabrication resume system for researchers and engineers. This fork adapts it as a Claude Code skill for PM/TPM/PO job applications, extending the workflow with career workbook management, comp analysis, recruiter kits, and cover letter generation.

A complete resume and CV builder as a Claude Code skill. Bundles nine sub-skills that guide you from raw job description to polished, ATS-optimised resume and cover letter — with mandatory review gates at each phase.

---

## What's Included

| Command | Sub-skill | What it does |
|---------|-----------|--------------|
| `/new-session` | `skills/new-session/` | Two-step entry point: confirm Career Workbook (Step 1), then display session prompt with 4 JD input options (Step 2) |
| `/career-workbook` | `skills/career-workbook/` | Display, review, and update the Career Workbook standalone |
| `/make-resume` | `skills/make-resume/` | Generate a tailored resume/CV from a JD in three gated phases |
| `/edit-resume` | `skills/edit-resume/` | Apply critique feedback — tier-1 fixes first |
| `/critique-resume` | `skills/critique-resume/` | Score resume (and optional CL) against a JD across 8 dimensions |
| `/make-cl` | `skills/make-coverletter/` | Generate a tailored cover letter from an existing session |
| `/optimize-linkedin` | `skills/optimize-linkedin/` | Suggest copy-paste LinkedIn profile changes aligned to a goal or JD |
| `/setup-extract` | `skills/setup-extract/` | Extract achievements from research papers into a knowledge base |
| `/setup-build-kb` | `skills/setup-build-knowledgebase/` | Synthesise extractions into a resume-ready KB |

---

## Standard Workflow

```
/new-session
  │
  ├── STEP 1: Career Workbook Review  (/career-workbook)
  │     Displays timeline, certs, verified metrics as a compact table
  │     ├── "yes / looks good" → proceed to Step 2
  │     └── corrections provided → apply edits, re-confirm, loop back
  │
  └── STEP 2: Session Prompt
        4 JD input options:
          2a  Copy-paste JD inline
          2b  File path or folder  (e.g. JDs/Acme.txt)
          2c  Upload .txt / .pdf / image  (Claude extracts text)
          2d  URL  (Claude fetches + extracts JD, shows preview)
        ↓ (fill template + provide JD)

/make-resume [JD path]
    Phase 0: JD analysis, gap mapping, framing strategy, comp table — STOP
    Phase 1: Bullet plan per position — STOP
    Phase 2: Generate resume markdown + bullet audit — STOP
        ↓
/make-cl                   ← cover letter from Phase 0 framing (no re-research)
        ↓
/critique-resume           ← score resume + CL together (8-dimension, 100-pt scale)
        ↓
/edit-resume               ← apply tier-1 fixes (resume) + /edit-resume (CL)
        ↓
  ┌──────────────────────────────────────────────────┐
  │  DOCX + PDF → Claude Web (claude.ai)             │
  │  Paste approved markdown → ask for DOCX/PDF      │
  └──────────────────────────────────────────────────┘
```

**Key principle:** All markdown (resume, CL, critique, edits) is generated in CLI. DOCX and PDF are always produced in **Claude Web** (`claude.ai`) — paste the approved markdown there. Claude Web renders significantly better output than CLI for document formatting.

---

## Key Features

### 1. Three-Phase Resume Generation
- **Phase 0** — JD analysis: requirements table (Direct / Bridge / Gap), ATS keyword bank, gap assessment, company research, framing strategy with reframing map, compensation table, cover letter plan
- **Phase 1** — Bullet plan: achievement candidates from Career Workbook mapped to JD requirements, priority matrix, budget check
- **Phase 2** — Generate: summary, skills, experience bullets with char count gate and page fill gate; produces bullet audit table and recruiter kit

### 2. Mandatory Gates
- **Sponsorship / Viability Gate** — flags GC/Citizen-only roles and C2C mismatches before wasting any tokens
- **Metric Verification Gate** — every %, $, or headcount must trace back to the Career Workbook; unverified metrics are replaced with directional language automatically
- **Char Count Gate** — bullets checked to 180–220 chars per line; orphan lines flagged
- **Budget Gate** — bullet counts confirmed against page-budget targets before Phase 2
- **MANDATORY STOP** after each phase — work halts until the user explicitly approves before continuing

### 3. Writer Role Selection
Automatically detects seniority level from the JD and applies the right writing philosophy:
- **CERW** (Director+ / Executive) — scope-first, strategic outcomes, executive register
- **CPRW** (Manager / IC) — action verb + metric forward, execution depth, JD keyword density

### 4. 8-Dimension Critique Scoring
| Dimension | Weight |
|-----------|--------|
| Bullet quality | 25% |
| Narrative cohesion | 15% |
| ATS compatibility | 15% |
| Summary | 10% |
| Skills | 10% |
| Publications | 10% |
| Credibility | 10% |
| Visual layout | 5% |

Produces: five-perspective read-through (ATS / Recruiter / HR / HM / Technical), interview likelihood per reader, tiered improvements (T1 ≥1pt, T2 0.3–0.9pt, T3 <0.3pt), CL critique, post-generation checklist.

### 5. Recruiter Kit
Generated automatically at the end of Phase 2:
- Recruiter email (≤150 words)
- LinkedIn connection note (≤300 chars)
- Voicemail script (≤80 words)
- 5 recruiter talking points mapped to JD requirements

---

## Installation

1. Clone this repository into your Claude Code skills folder:

   ```bash
   git clone https://github.com/blpatnaik31/resume-toolkit ~/.claude/skills/resume-toolkit
   ```

2. Reload Claude Code (restart the CLI or IDE extension).

3. Verify by typing `/resume-toolkit` — you should see the skill listed.

---

## Usage

### Start a new session
```
/new-session
```
Step 1 displays your Career Workbook summary for confirmation (or updates). Step 2 displays the session template with four ways to provide the JD: paste inline, file path, upload, or URL.

### Review or update the Career Workbook standalone
```
/career-workbook
```
Displays the Career Timeline, certifications, KB corrections, and verified metrics. Accepts corrections (new role, metric update, cert change) before proceeding.

### Generate a tailored resume
```
/make-resume JDs/Acme_SeniorPM.txt
/make-resume JDs/JPMC_TPM.txt Focus: Risk Management, ML platforms
/make-resume Quick: JDs/GlobalLogic_TPM.txt   ← skips Phase 0/1 stops
```

### Critique a finished resume
```
/critique-resume output/Acme/session_Acme_SeniorPM.md
```

### Generate a cover letter
```
/make-cl output/Acme/session_Acme_SeniorPM.md
```

### Apply edits
```
/edit-resume output/Acme/session_Acme_SeniorPM.md
```

---

## File Structure

```
resume-toolkit/
├── README.md
├── SKILL.md                        ← Meta-skill router + verified metrics table
└── skills/
    ├── career-workbook/SKILL.md    ← Step 1: workbook review + update gate
    ├── new-session/SKILL.md        ← Step 1+2 orchestrator + 4 JD input options
    ├── make-resume/SKILL.md        ← Phase 0 / 1 / 2 + gates + recruiter kit
    ├── edit-resume/SKILL.md
    ├── critique-resume/SKILL.md    ← 8-dimension scoring + 5-perspective read
    ├── make-coverletter/SKILL.md
    ├── optimize-linkedin/SKILL.md
    ├── setup-extract/SKILL.md
    └── setup-build-knowledgebase/SKILL.md
```

---

## Effort Defaults

| Sub-skill | Effort | Reason |
|-----------|--------|--------|
| `/new-session` | low | Orchestration only; no generation |
| `/career-workbook` | low | Display + optional edit; no resume generation |
| `/make-resume` | medium | Gated phases; more iterations don't improve bullet quality |
| `/make-cl` | medium | Fixed structure; flows from Phase 0 framing |
| `/edit-resume` | medium | Targeted edits only |
| `/critique-resume` | high | Scoring depth benefits from thorough treatment |

---

## Conventions

### Session Naming
`Job Title | Client | Vendor | Employer`
- **Client** — end-client company (blank if direct hire)
- **Vendor** — staffing vendor placing the candidate (blank if direct)
- **Employer** — direct employer of record

Examples:
- `Senior PM | Santander | OkayaInfocom | OkayaInfocom`
- `TPM | [direct] | Exaways | Altimetrik`
- `Technical Project Manager | iRhythm | | GlobalLogic`

### Output Folder Layout
```
output/
└── <CompanyName>_<RoleTag>/
    ├── session_<name>.md       ← session state machine (phases, status, bullet plan)
    ├── resume_<name>.md        ← generated resume markdown
    ├── cl_<name>.md            ← generated cover letter markdown
    ├── critique_<name>.md      ← critique report + score
    └── recruiter_kit_<name>.md ← email, LinkedIn note, voicemail, talking points
```

### Git Commit Messages
```
feat(resume): <Company> <Role> — resume markdown DONE
feat(resume): <Company> <Role> — CL markdown DONE
chore(jobs):  <Company> <Role> — Phase 0 DONE
docs(session): <Company> <Role> — Phase N DONE
```

---

## Credits

This project is a fork of **[ARPeeketi/claude-resume-kit](https://github.com/ARPeeketi/claude-resume-kit)** by [@ARPeeketi](https://github.com/ARPeeketi), licensed under the MIT License.

The original system introduced the core ideas this toolkit is built on:
- Knowledge-base-first approach (extract once, apply to many JDs)
- Anti-fabrication controls with provenance flags per achievement
- Verb discipline rules to prevent overclaiming
- AI fingerprint avoidance (banned-word lists, structural anti-patterns, post-generation scan)
- Multi-perspective critique framework (ATS / Recruiter / HR / HM / Technical)

This fork extends the original for PM/TPM/PO job applications and packages it as a Claude Code skill:
- Career Workbook management with verified metrics enforcement
- Three-phase gated resume generation (Viability → Bullet Plan → Generate)
- Writer Role selection (CERW for Director+ / CPRW for Manager/IC)
- Compensation analysis with 3-layer and 4-layer C2C models
- Recruiter Kit generation (email, LinkedIn note, voicemail, talking points)
- Cover letter generation flowing from Phase 0 framing
- Two-step `/new-session` with workbook review and four JD input methods

---

## License

MIT

---

## Support

If this toolkit saved you hours of resume work, consider buying me a coffee or sending a PayPal tip.

[![Buy Me A Coffee](https://img.shields.io/badge/Buy%20Me%20A%20Coffee-donate-yellow?logo=buy-me-a-coffee&logoColor=white)](https://buymeacoffee.com/blpatnaik31)
&nbsp;&nbsp;
[![PayPal](https://img.shields.io/badge/Donate-PayPal-blue?logo=paypal&logoColor=white)](https://paypal.me/LokapatiPatnaik)

**PayPal QR Code** — scan to donate directly:

<img src="assets/paypalqrcode.png" alt="PayPal QR Code" width="180"/>

Voluntary contributions only — the toolkit is and always will be free and open source (MIT).