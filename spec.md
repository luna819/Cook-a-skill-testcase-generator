# Spec: Test Case Generator AI Skill

> Version: v1.0  
> Status: Pending Supervisor Approval  
> Supervisor: Hân (Hano) — QA Manager

---

## 1. Overview

**Skill Name:** Test Case Generator  
**Type:** AI Skill (Instruction-based)  
**Target Users:** QC / Tester  
**Platform:** Claude Project / Custom GPT / Dify or any AI tool that supports custom instructions

---

## 2. Problem Statement

Current QC test case writing process:
- Manually read spec → think through each case → write into a sheet
- Takes **4–8 hours** for an average feature module
- Prone to missing edge cases and negative cases due to reliance on individual experience
- Inconsistent format across different QC members

**Goal:** Reduce test case writing time to **15–30 minutes** while improving coverage and consistency.

---

## 3. Scope

### In Scope (MVP)
- Accept a `spec.md` file describing a feature or module as input
- Analyze the spec and automatically generate test cases
- Categorize cases: happy path / edge case / negative case
- Each test case includes: ID, name, precondition, steps, expected result, priority
- Output in Markdown table or structured list format

### Out of Scope (Not in MVP)
- Direct integration with Jira / TestRail / Google Sheets
- Automated test execution
- Support for PDF or Word spec files (`.md` only)
- Processing multiple modules at once

---

## 4. Input

| Field | Description | Required |
|---|---|---|
| `spec.md` | Markdown file describing the feature to be tested | ✅ |
| Testing type | Functional / UI / API (if categorization is needed) | ❌ Optional |
| Priority focus | Which module needs the most thorough testing | ❌ Optional |

**Requirements for spec file:**
- Written in Markdown (`.md`)
- Clearly describes: what the feature does, user flow, input fields, validation rules, business rules
- The clearer the spec, the better the output quality

---

## 5. Output

Each generated test case follows this structure:

```
### TC-[ID]: [Test case name]

- **Type:** Happy Path / Edge Case / Negative Case
- **Priority:** Critical / Major / Minor
- **Precondition:** [Conditions before execution]
- **Steps:**
  1. [Step 1]
  2. [Step 2]
  ...
- **Expected Result:** [What should happen]
- **Test Data:** [Suggested test data if applicable]
```

**Example output for a Login feature:**

```
### TC-001: Successful login with valid credentials

- **Type:** Happy Path
- **Priority:** Critical
- **Precondition:** User has an active account in the system
- **Steps:**
  1. Open the Login page
  2. Enter a valid email
  3. Enter the correct password
  4. Click the "Login" button
- **Expected Result:** User is redirected to the Dashboard; username is displayed in the header
- **Test Data:** email: test@example.com / password: Test@1234

---

### TC-002: Login fails with incorrect password

- **Type:** Negative Case
- **Priority:** Critical
- **Precondition:** User has an active account
- **Steps:**
  1. Open the Login page
  2. Enter a valid email
  3. Enter an incorrect password
  4. Click the "Login" button
- **Expected Result:** Error message "Email or password is incorrect" is shown; no redirect occurs
- **Test Data:** email: test@example.com / password: WrongPass123
```

---

## 6. Processing Workflow

```
[Input: spec.md]
       ↓
[Step 1] Analyze the spec
  - Identify the main feature
  - List all user flows
  - Identify input fields & validation rules
  - Identify business rules
       ↓
[Step 2] Generate test cases by category
  - Happy path: main success flows
  - Edge case: boundary values, limit scenarios
  - Negative case: wrong, missing, or invalid input
       ↓
[Step 3] Assign priority
  - Critical: main flows, severe impact if failed
  - Major: important features with available workarounds
  - Minor: minor UI issues, UX improvements
       ↓
[Step 4] Format output
  - Structured Markdown
  - Has ID, all fields complete, easy to copy into a sheet
       ↓
[Output: List of test cases]
```

---

## 7. Skill Edge Cases

| Scenario | How it's handled |
|---|---|
| Spec is too short or missing information | Skill asks: "The spec is missing info about [X], can you clarify?" |
| Spec describes multiple features at once | Skill separates each feature and generates cases individually |
| Complex validation rules | Additional boundary test cases are generated |
| Ambiguous or unclear expected behavior | Test case is flagged with "⚠️ Needs clarification from PO" |

---

## 8. Definition of Done

- [ ] Feed any spec.md → output at least 10 fully structured test cases
- [ ] Covers all 3 types: happy path, edge case, negative case
- [ ] Each test case has: ID, type, priority, precondition, steps, expected result
- [ ] Output is in Markdown and can be copied directly into a sheet
- [ ] No hallucinated information not present in the spec
- [ ] Speed: from spec input to output in under 2 minutes

---

## 9. Current Limitations

- Only processes `.md` spec files; PDF/Word/Confluence not supported
- No integration with test management tools (Jira, TestRail...)
- Output quality depends on input spec quality
- Does not auto-generate complex test data (suggestions only)

---

## 10. Roadmap

| Phase | Feature |
|---|---|
| v1.1 | Add export template to Google Sheets |
| v1.2 | More detailed auto-generated test data |
| v2.0 | Combine with report template: QC fills pass/fail → AI generates test summary report |
| v2.1 | Support input from Confluence / Notion pages |

---

*This spec was created using Claude as required by the Cook A Skill Guidebook.*  
*Pending approval from Supervisor (Hân/Hano) before starting the build phase.*
