---
name: gpt-pro
description: "Package files + structured prompts into tar.gz for deep reasoning review by ChatGPT Pro (browser upload). Use when user says 'send to GPT Pro', 'GPT Pro review', 'ChatGPT Pro review', 'prepare for GPT', 'package for GPT Pro', 'get GPT to review', 'GPT Pro analysis', 'upload to ChatGPT', 'gpt-pro', '/gpt-pro', 'pack for GPT', 'bundle for ChatGPT', 'have ChatGPT check this', 'send this to GPT', 'GPT deep review', 'browser pack', 'browser-pack'. Creates tar.gz + PROMPT.md in ~/Documents/GPT Pro Analysis/ subfolder, auto-copies prompt to clipboard, for manual upload to chat.openai.com."
---

# GPT Pro Analysis — Package Anything for ChatGPT Pro Deep Reasoning

Prepare any problem + relevant files into a structured tar.gz bundle that's ready to upload to ChatGPT Pro (browser). Domain-agnostic — works for code debugging, architecture review, data analysis, writing feedback, reverse engineering, research synthesis, or anything that benefits from deep reasoning over multiple files.

## Why Structured Packages Beat Copy-Paste

ChatGPT Pro accepts file uploads and excels at deep, long-context reasoning — but quality in = quality out. A tar.gz with a structured PROMPT.md that frames the problem, sets the role, defines the output format, and points to the right files consistently produces 10x better results than dumping raw code into the chat. This skill encodes the prompt patterns that work.

## Core Workflow

```
1. UNDERSTAND  → What does the user want GPT Pro to analyze? What's the question?
2. SCOPE       → Which files are relevant? What context does GPT Pro need?
3. COLLECT     → Copy relevant files into a staging directory
4. PROMPT      → Generate PROMPT.md with role, context, questions, output format
5. SANITIZE    → Strip secrets, credentials, internal URLs
6. PACKAGE     → tar.gz to ~/Desktop with descriptive dated name
7. REPORT      → Tell user: package location, contents, upload instructions
```

## Step 1: Understand the Task

Ask or infer from conversation context:
- **What's the question?** — "Why is this broken?", "Review this architecture", "Analyze this data"
- **What expertise should GPT Pro adopt?** — Match to the domain (senior engineer, editor, data scientist, etc.)
- **What output format?** — Code fixes with file:line? A report? A redesigned component? A comparison matrix?

## Step 2: Scope the Files

Include ONLY what GPT Pro needs. Less is more — irrelevant files dilute attention.

**Always include:**
- Files directly related to the problem
- Config/env that affects behavior (sanitized)
- Sample data showing the issue (logs, errors, outputs, examples)

**Include when helpful:**
- Reference/comparison material (competitor code, prior version, working example)
- Prior findings or decision logs (so GPT doesn't re-investigate solved problems)

**Never include:**
- `node_modules/`, `target/`, `__pycache__/`, build artifacts
- Files >5000 lines (excerpt the relevant sections instead)
- Unrelated modules or services

## Step 3: Build the Package Directory

```
<descriptive-name>/
├── PROMPT.md           # THE key file — structured task + questions
├── source/             # Relevant source files (preserve original names)
│   ├── module_a.py
│   ├── module_b.rs
│   └── Component.tsx
├── data/               # Supporting evidence — logs, outputs, examples
│   ├── error_log.txt
│   ├── sample_output.json
│   └── expected_vs_actual.md
├── reference/          # Optional — comparison material, prior analysis
│   └── prior_findings.md
└── config/             # Optional — sanitized config
    └── env_sanitized.txt
```

Adapt the subdirectory names to the domain. For a book review it might be `chapters/` and `feedback/`. For data analysis it might be `datasets/` and `schemas/`. Use whatever makes sense.

## Step 4: Write PROMPT.md

This is the most important file. It's what gets pasted into ChatGPT Pro first, before uploading the tar.gz. Structure it with these sections:

### PROMPT.md Template

```markdown
# <Title — What You Want Analyzed>

## Your Role

You are a senior <domain expert> conducting <type of analysis>. You have deep expertise in <relevant technologies/fields>.

**Rules:**
1. Base every conclusion on evidence from the files provided. Flag speculation as `[HYPOTHESIS]`.
2. Don't tell me what's working. Tell me what's wrong/missing/improvable and how to fix it.
3. For each finding: root cause, evidence (file:line or specific quote), fix, expected impact.

## Context

<2-4 paragraphs explaining the system/project/document — what it does, how it works, what the current state is. Include enough for GPT Pro to understand the architecture without needing to infer it from code alone.>

## Current Situation

<Quantified state — metrics, error rates, performance numbers, word counts, whatever is measurable. Concrete data, not vibes.>

## Questions

### Q1: <Specific, answerable question>
**Data:** <Point to specific files in the package>
**Hypotheses:** <2-3 possible explanations to investigate>

### Q2: <Next question>
**Data:** <File pointers>

### Q3-N: <As many as needed, usually 3-7>

## Files Included

<List each file with a one-line description of what it contains and why it's relevant.>

## Output Format

For each question:
1. **Finding** — what specifically is the issue?
2. **Evidence** — cite file:line, specific data, or quote
3. **Fix/Recommendation** — concrete, actionable
4. **Impact** — what changes if this is fixed?

## Constraints

<Budget, timeline, technology stack limitations, things that can't change, scope boundaries.>
```

### Adapting the Template

The template above is a skeleton. Adapt the sections to fit the domain:

- **Code debugging**: Add "System Architecture" section, include error messages, stack traces
- **Architecture review**: Add "Current Architecture" diagram/description, "Proposed Changes", "Trade-offs to Evaluate"
- **Writing/book review**: Replace "Questions" with "Review Criteria" (structure, pacing, clarity, argument strength)
- **Data analysis**: Add "Dataset Description" (schema, size, collection method), "Analysis Goals"
- **Design review**: Add "Design Requirements", "User Personas", "Existing Patterns"
- **Reverse engineering**: Add "What We Know", "What We Don't Know", "Reference Implementations"

The pattern is always: **Role + Context + Specific Questions + Evidence Pointers + Output Format**.

## Step 5: Sanitize

Before packaging, strip ALL secrets. This is non-negotiable — the package is going to a third-party service.

```bash
# Create sanitized env copy
grep -v -E '(PRIVATE_KEY|MNEMONIC|API_KEY|PASSWORD|SECRET|AUTH_TOKEN|CREDENTIAL|ACCESS_KEY)' .env > config/env_sanitized.txt
```

**Strip from all files:**
- Private keys, mnemonics, seed phrases
- API keys, auth tokens, session cookies
- Passwords, database connection strings
- Internal IP addresses, hostnames (replace with `<REDACTED>`)
- Cloud credentials (AWS, GCP, Azure)

**Quick check before packaging:**
```bash
grep -rni "password\|secret\|api_key\|private_key\|mnemonic\|token.*=" <pack-dir>/ | head -20
```

## Step 6: Package

All packages go under `~/Documents/GPT Pro Analysis/`, with each run in its own subfolder:

```bash
BASEDIR=~/Documents/GPT\ Pro\ Analysis
OUTDIR="$BASEDIR/<project>-<type>-$(date +%Y-%m-%d)"
mkdir -p "$OUTDIR"
cp /tmp/<pack-dir>/PROMPT.md "$OUTDIR/PROMPT.md"
tar czf "$OUTDIR/<project>-<type>-$(date +%Y-%m-%d).tar.gz" -C /tmp <pack-dir>/
```

Then copy the prompt to the clipboard so the user can paste it straight into ChatGPT Pro:

```bash
cat "$OUTDIR/PROMPT.md" | pbcopy
```

Example structure:
```
~/Documents/GPT Pro Analysis/
├── myapp-debug-2026-03-21/
│   ├── PROMPT.md
│   └── myapp-debug-2026-03-21.tar.gz
├── novel-structure-review-2026-03-28/
│   ├── PROMPT.md
│   └── novel-structure-review-2026-03-28.tar.gz
└── ...
```

## Step 7: Report to User

Always end with:

1. **Package location** — the path under `~/Documents/GPT Pro Analysis/` containing both `PROMPT.md` and the `.tar.gz`
2. **What's inside** — file count, key files listed
3. **Clipboard** — confirm the prompt has been copied to clipboard
4. **How to use it:**
   - Open ChatGPT Pro in browser
   - Paste (Cmd+V) — the prompt is already in your clipboard
   - Upload the `.tar.gz` from the Desktop subfolder
   - Send

## PASTE_THIS.txt Alternative

For smaller packages where upload is overkill, create a single `PASTE_THIS.txt` that concatenates everything:

```
# <TITLE>
# Paste this into ChatGPT Pro
# Date: <DATE>

## Task
<prompt content>

================================================================
FILE: <filename>
================================================================
<file contents>

================================================================
FILE: <next filename>
================================================================
<file contents>
```

This works well when the total content is under ~50K characters.

## Multi-Round Workflow

When GPT Pro returns results and you need a follow-up:

1. Save outputs in the project with clear attribution:
   - `<dir>/gpt-pro-analysis-<date>.md` with header: "Source: GPT-5.4 Pro, <date>"
2. For Round 2+, reference prior findings in the new PROMPT.md:
   ```markdown
   ## Prior Analysis (Round 1)
   <Summary of what GPT Pro found last time>

   ## What Needs Follow-Up
   <Specific gaps, validations needed, deeper dives>
   ```
3. Include Round 1 outputs in the new package under `reference/`
