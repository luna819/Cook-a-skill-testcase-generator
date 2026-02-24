# Skill Card: Test Case Generator

| | |
|---|---|
| **Category** | QC / Testing |
| **Version** | v1.0 |
| **Author** | [Your Name] |
| **Platform** | Claude Project / Custom GPT / Dify |

---

## What It Does

Reads a feature spec (`.md` file) and automatically generates a complete, structured list of test cases — covering happy paths, edge cases, and negative cases — in under 2 minutes.

---

## The Problem It Solves

| Before | After |
|---|---|
| Writing test cases manually: 4–8 hours | Generated in under 2 minutes |
| Easy to miss edge cases and negative cases | Full coverage across all 3 categories |
| Inconsistent format across QC members | Standardized output every time |
| Depends on individual QC experience | Knowledge encoded into the skill |

---

## Input

| Field | Required | Description |
|---|---|---|
| `spec.md` | Yes | Markdown file describing the feature to be tested |
| Testing type | No | Functional / UI / API |
| Priority focus | No | Which module needs the most thorough coverage |

**Requirements for the spec:** written in Markdown; clearly describes feature behavior, user flows, input fields, validation rules, and business rules.

---

## Output

Each generated test case follows this structure:

| Field | Description |
|---|---|
| **ID** | TC-001, TC-002, ... |
| **Type** | Happy Path / Edge Case / Negative Case |
| **Priority** | Critical / Major / Minor |
| **Precondition** | What must be true before the test begins |
| **Steps** | Step-by-step actions to execute |
| **Expected Result** | Concrete, observable outcome |
| **Test Data** | Suggested values to use |

Test cases are grouped into 3 sections: `## Happy Path Cases` / `## Edge Cases` / `## Negative Cases`, preceded by a `## Test Case Summary` block.

---

## How to Use

| Step | Action |
|---|---|
| 1 | Open your AI tool (Claude Project / Custom GPT / Dify) |
| 2 | Load the skill instruction from `SKILL.md` |
| 3 | Paste or attach your `spec.md` content |
| 4 | Send: **"Generate test cases for this spec"** |
| 5 | Review output, supplement as needed, copy into your test sheet |

---

## Sample Prompt

```
Here is my spec.md for the Login feature. Please generate test cases.

[paste spec content here]
```

---

## Definition of Done

| Criteria | Target |
|---|---|
| Minimum test cases per spec | ≥ 10 |
| Type coverage | Happy Path + Edge Case + Negative Case |
| Required fields per case | ID, Type, Priority, Precondition, Steps, Expected Result |
| Output format | Markdown — ready to copy into any sheet |
| Hallucination | None — only information from the spec |
| Speed | Input to output in under 2 minutes |

---

## Limitations

| Limitation | Detail |
|---|---|
| File format | Only `.md` supported — no PDF, Word, or Confluence |
| Output quality | Depends on spec quality — vague spec = fewer cases |
| Integrations | No direct export to Jira / TestRail / Google Sheets |
| Test data | Suggestions only — no complex auto-generated data |

---

## Roadmap

| Version | Feature |
|---|---|
| v1.1 | Export template for Google Sheets |
| v1.2 | More detailed auto-generated test data |
| v2.0 | QC fills pass/fail → AI generates test summary report |
| v2.1 | Support input from Confluence / Notion |
