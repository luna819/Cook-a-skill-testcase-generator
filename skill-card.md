# Skill Card: Test Case Generator

|              |                                    |
| ------------ | ---------------------------------- |
| **Category** | QC / Testing                       |
| **Version**  | v1.1                               |
| **Author**   | [Your Name]                        |
| **Platform** | Claude Project / Custom GPT / Dify |

---

## What It Does

Reads a feature spec (`.md` file) and automatically generates a complete, structured list of test cases — covering happy paths, edge cases, negative cases, and **security cases** — in under 2 minutes.

Includes:

- **Pre-Analysis Report** before generating (identifies flows, fields, BRs, security triggers, PII fields)
- **Testing technique labeling** (BVA, EP, State Transition) per test case
- **Spec Reference** on every TC — traceable back to the source spec
- **AC Coverage Matrix** — maps each TC to the Acceptance Criteria it covers
- **Anti-hallucination self-check** — removes or flags any TC not grounded in the spec

---

## ROI — Why It Matters

| Metric                   | Before                      | After                                          |
| ------------------------ | --------------------------- | ---------------------------------------------- |
| Time to write test cases | **4–8 hours** per module    | **15–20 minutes** (review + adjust AI output)  |
| Security coverage        | Often skipped or incomplete | Auto-generated from 9 security trigger signals |
| Edge case coverage       | Depends on QC experience    | Systematic — BVA/EP applied automatically      |
| Format consistency       | Varies per person           | Standardized every time                        |
| Traceability             | Manual — easy to miss       | `Spec Reference` on every TC                   |

> **Estimated time saved per module: 3.5–7.5 hours**

---

## Input

| Field          | Required | Description                                          |
| -------------- | -------- | ---------------------------------------------------- |
| `spec.md`      | Yes      | Markdown file describing the feature to be tested    |
| Testing type   | No       | Functional / UI / API                                |
| Priority focus | No       | Which module needs the most thorough coverage        |
| Output mode    | No       | Senior (default — full detail) / Junior (simplified) |

**Requirements for the spec:** written in Markdown; clearly describes feature behavior, user flows, input fields, validation rules, and business rules. The clearer the spec, the better the output.

---

## Output

### Per Test Case — 8 fields

| Field               | Description                                                                 |
| ------------------- | --------------------------------------------------------------------------- |
| **ID**              | TC-001, TC-002, ...                                                         |
| **Type**            | Happy Path / Edge Case / Negative Case / Security Case                      |
| **Priority**        | Critical / Major / Minor                                                    |
| **Technique**       | BVA / EP / State Transition / N/A                                           |
| **Spec Reference**  | Section, field, or rule the TC is derived from (mandatory)                  |
| **Precondition**    | What must be true before the test begins                                    |
| **Steps**           | Step-by-step actions to execute                                             |
| **Expected Result** | Concrete, observable outcome (includes HTTP status code for security cases) |
| **Test Data**       | Suggested synthetic values to use                                           |

### Output structure

```
## Pre-Analysis Report       ← feature summary, fields, BRs, security triggers, PII, estimated TC count
## Test Case Summary         ← count by type, security triggers detected, PII notice, coverage notes
## AC Coverage Matrix        ← maps each AC/BR to the TCs that cover it
## Happy Path Cases          ← TC-001...
## Edge Cases                ← TC-00n...
## Negative Cases            ← TC-00n...
## Security Cases            ← TC-00n...
```

### TC count — scales by complexity

| Spec complexity | Signals                                  | Minimum TCs |
| --------------- | ---------------------------------------- | ----------- |
| Simple          | 1–2 flows, <5 fields, no BRs             | 10          |
| Medium          | 3–5 flows, 5–10 fields, 1–4 BRs          | 15          |
| Complex         | 5+ flows, 10+ fields, 5+ BRs, multi-role | 20+         |

---

## Security Coverage

Security cases are auto-generated when the spec contains any of these signals, mapped to OWASP Top 10:

| Trigger in spec          | Security case        | OWASP |
| ------------------------ | -------------------- | ----- |
| Free-text input field    | XSS                  | A03   |
| DB-queried field         | SQL Injection        | A03   |
| Password + storage rule  | Credential Exposure  | A02   |
| Token-based flow         | Token Integrity      | A07   |
| Form submission          | Rate Limiting        | A04   |
| State-changing request   | CSRF                 | A01   |
| Resource IDs             | IDOR                 | A01   |
| Role-based access        | Privilege Escalation | A01   |
| File upload / path input | Path Traversal       | A03   |

---

## Export Formats

The skill outputs **Markdown by default**. For integration with test management tools, use the mapping below to manually transfer or build a conversion script:

### → Jira (Zephyr / Xray)

| Skill output field | Jira field                                                  |
| ------------------ | ----------------------------------------------------------- |
| TC ID + Name       | Issue Summary                                               |
| Type               | Label (`happy-path`, `edge-case`, `negative`, `security`)   |
| Priority           | Priority (Critical → Highest, Major → High, Minor → Medium) |
| Precondition       | Precondition (Zephyr) / Environment (Xray)                  |
| Steps              | Test Steps                                                  |
| Expected Result    | Expected Result (per step)                                  |
| Spec Reference     | Linked Requirement / Story                                  |
| Test Data          | Description or Attachment                                   |

### → TestRail

| Skill output field | TestRail field                            |
| ------------------ | ----------------------------------------- |
| TC ID + Name       | Title                                     |
| Type               | Type (Functional / Security / Regression) |
| Priority           | Priority                                  |
| Precondition       | Preconditions                             |
| Steps              | Steps (Step Description)                  |
| Expected Result    | Steps (Expected Result)                   |
| Spec Reference     | References                                |
| Test Data          | Custom field or included in Steps         |

> **Note:** Direct export to Jira/TestRail is not yet automated — planned for v1.2. Currently, copy-paste from Markdown output is required.

---

## How to Use

| Step | Action                                                                                |
| ---- | ------------------------------------------------------------------------------------- |
| 1    | Open your AI tool (Claude Project / Custom GPT / Dify)                                |
| 2    | Load the skill instruction from `SKILL.md`                                            |
| 3    | Paste or attach your `spec.md` content                                                |
| 4    | Send: **"Generate test cases for this spec"**                                         |
| 5    | Review Pre-Analysis Report — confirm the skill understood the spec correctly          |
| 6    | Review test cases — adjust, remove, or add cases as needed                            |
| 7    | Copy output into your test sheet or map to Jira/TestRail using the export guide above |

---

## Sample Prompt

```
Here is my spec.md for the User Registration feature. Please generate test cases.

[paste spec content here]
```

For simplified output (Junior mode):

```
Generate basic test cases for this spec. Use simplified mode.

[paste spec content here]
```

---

## Definition of Done

| Criteria               | Target                                                                                  |
| ---------------------- | --------------------------------------------------------------------------------------- |
| TC count               | Scales by complexity: Simple ≥10 / Medium ≥15 / Complex ≥20                             |
| Type coverage          | Happy Path + Edge Case + Negative Case + **Security Case**                              |
| Security triggers      | All 9 OWASP-mapped signals checked automatically                                        |
| Required fields per TC | ID, Type, Priority, Technique, **Spec Reference**, Precondition, Steps, Expected Result |
| AC Coverage Matrix     | Every Acceptance Criterion / BR mapped to at least 1 TC                                 |
| Hallucination          | None — every TC cites a Spec Reference; unspecified items flagged ⚠️                    |
| Test data              | Synthetic only — no real PII                                                            |
| Output format          | Markdown — ready to copy into any sheet or map to Jira/TestRail                         |
| Speed                  | Input to output in under 2 minutes                                                      |

---

## Limitations

**This skill generates test case documents — it does not execute tests.** Human QC review is required before running any case. Output quality is proportional to spec quality: vague or one-paragraph specs will produce fewer and shallower test cases. Direct export to Jira, TestRail, or Google Sheets is not yet automated; manual mapping is required using the export guide above.

| Limitation     | Detail                                                                       |
| -------------- | ---------------------------------------------------------------------------- |
| File format    | Only `.md` supported — no PDF, Word, or Confluence                           |
| Output quality | Proportional to spec quality — vague spec = shallow output                   |
| Export         | No automated integration with Jira / TestRail / Google Sheets (v1.2 roadmap) |
| Test data      | Synthetic suggestions only — complex data generation not supported           |
| Multi-module   | One spec at a time — batch processing not supported in MVP                   |

---

## Roadmap

| Version | Feature                                                                    |
| ------- | -------------------------------------------------------------------------- |
| v1.1    | Testing technique labels (BVA/EP/State Transition) + AC Coverage Matrix ✅ |
| v1.2    | Export template for Google Sheets; TestRail CSV export                     |
| v1.3    | Automated Jira ticket creation via API                                     |
| v2.0    | QC fills pass/fail → AI generates test summary report                      |
| v2.1    | Support input from Confluence / Notion                                     |
