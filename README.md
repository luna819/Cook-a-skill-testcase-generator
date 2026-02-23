# 🧪 Test Case Generator — AI Skill

> **Cook A Skill** | QC Team | Internal Competition

---

## Overview

**Test Case Generator** is an AI Skill built to automate the test case writing process for QC/Testers. Instead of spending a full day writing manually, you simply feed a `spec.md` file into the skill — and AI will generate a complete, well-formatted list of test cases in just minutes.

---

## Problem Being Solved

| Before (Manual) | After (AI Skill) |
|---|---|
| Read spec and think through each test case manually | AI reads spec → generates test cases automatically |
| Easy to miss edge cases and negative cases | AI covers more broadly, nothing gets skipped |
| Each QC writes in a different format | Standardized, consistent output format |
| Takes a full day for one complex module | Generated in minutes, QC only reviews and supplements |
| Depends on individual experience | Knowledge is encoded into the skill, usable by anyone |

---

## Input / Output

- **Input:** A `spec.md` file describing the feature or module to be tested
- **Output:** A list of test cases including:
  - Happy path cases
  - Edge cases
  - Negative cases
  - Steps to reproduce
  - Expected result
  - Preconditions
  - Priority (Critical / Major / Minor)

---

## How to Use

1. Open your AI tool (Claude Project / Custom GPT / Dify...)
2. Feed the `spec.md` file into the skill
3. The skill automatically analyzes and generates test cases
4. QC reviews the output and supplements as needed
5. Export and use for real testing

---

## Repository Structure

```
repo-name/
  ├── README.md          ← this file
  ├── SKILL.md           ← main AI instruction file
  ├── spec.md            ← spec approved by Supervisor
  ├── skill-card.md      ← Skill Card summary
  └── ai-showcase/       ← screenshots of best prompts
```

---

## Evaluation Criteria (Judges)

| Axis | Description |
|---|---|
| Coverage | How many cases? Does it cover happy path, edge cases, and negative cases fully? |
| Quality | Are test cases clear? Are steps, expected results, and preconditions complete? |
| Intelligence | Does it detect hidden edge cases? Does it assign priority? |
| Output Format | Is it structured, easy to use, and exportable? |
| Speed | How fast does it go from spec input to output? |

---

