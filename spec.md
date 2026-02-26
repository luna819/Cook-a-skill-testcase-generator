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

## 2. User Personas

| Persona       | Profile                                                                                | How they use this skill                                                                                                                                                                                                              |
| ------------- | -------------------------------------------------------------------------------------- | ------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ |
| **QC Junior** | 0–2 years experience; tends to miss edge cases and security cases; inconsistent format | Uses **Junior mode** — paste spec, get Happy Path + Negative Cases in simplified format; review output as a learning reference; gradually add edge cases manually                                                                    |
| **QC Senior** | 3+ years experience; understands coverage depth; reviews AI output critically          | Uses **Senior mode (default)** — full output with BVA/EP labels, Spec Reference, AC Coverage Matrix, and Security Cases; adjusts or extends output as needed; uses Pre-Analysis Report to verify skill understood the spec correctly |

> **Output mode is selectable per run.** Junior mode reduces output depth; Senior mode (default) gives full coverage. Both follow the same TC format.

---

## 3. Problem Statement

Current QC test case writing process:

- Manually read spec → think through each case → write into a sheet
- Takes **4–8 hours** for an average feature module
- Prone to missing edge cases and negative cases due to reliance on individual experience
- Inconsistent format across different QC members

**Goal:** Reduce test case writing time to **15–30 minutes** while improving coverage and consistency.

---

## 4. Scope

### In Scope (MVP)

- Accept a `spec.md` file describing a feature or module as input
- Analyze the spec and automatically generate test cases
- Categorize cases: happy path / edge case / negative case / security case
- Each test case includes: ID, name, type, priority, **Technique**, **Spec Reference**, precondition, steps, expected result
- Output in Markdown table or structured list format
- **Security coverage:** When the spec contains user input fields, authentication, or data storage rules, the skill MUST generate security-related test cases including:
  - Injection attacks (XSS, SQL Injection) for any free-text input field
  - Credential/token exposure (password in URL, network log, response body)
  - Token integrity (verification links, session tokens — guessability, reuse, expiry)
  - Rate limiting / brute force protection on form submission
  - CSRF protection on state-changing requests

### Out of Scope (Not in MVP)

- Direct integration with Jira / TestRail / Google Sheets
- Automated test execution
- Support for PDF or Word spec files (`.md` only)
- Processing multiple modules at once

---

## 5. Acceptance Criteria

Each requirement below has explicit acceptance criteria that define when it is considered complete:

| ID    | Requirement          | Acceptance Criteria                                                                                                                                                                                         |
| ----- | -------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| AC-01 | Test case generation | Given a valid spec.md → skill generates ≥10 TCs (Simple), ≥15 (Medium), ≥20+ (Complex); all 4 types present: Happy Path, Edge Case, Negative Case, Security Case                                            |
| AC-02 | Security coverage    | Given a spec with any of 9 security trigger signals → skill auto-generates the corresponding Security Case mapped to OWASP Top 10; no manual prompt required                                                |
| AC-03 | Anti-hallucination   | Every TC has a Spec Reference pointing to an actual section/field/rule in the input spec; no invented rules or error messages; ambiguous items flagged with ⚠️ — not guessed                                |
| AC-04 | Output format        | Each TC contains all 9 required fields: ID, Type, Priority, Technique, Spec Reference, Precondition, Steps, Expected Result, Test Data; Pre-Analysis Report appears before TCs; AC Coverage Matrix included |
| AC-05 | Synthetic test data  | All test data uses synthetic values only — no real names, emails, phone numbers, or ID numbers                                                                                                              |
| AC-06 | Speed                | From spec input to complete output in under 2 minutes                                                                                                                                                       |

---

## 6. Input

| Field          | Description                                         | Required    |
| -------------- | --------------------------------------------------- | ----------- |
| `spec.md`      | Markdown file describing the feature to be tested   | ✅          |
| Testing type   | Functional / UI / API (if categorization is needed) | ❌ Optional |
| Priority focus | Which module needs the most thorough testing        | ❌ Optional |

**Requirements for spec file:**

- Written in Markdown (`.md`)
- Clearly describes: what the feature does, user flow, input fields, validation rules, business rules
- The clearer the spec, the better the output quality

---

## 7. Output

Each generated test case follows this structure:

```
### TC-[ID]: [Test case name]

- **Type:** Happy Path / Edge Case / Negative Case / Security Case
- **Priority:** Critical / Major / Minor
- **Technique:** BVA / EP / State Transition / N/A
- **Spec Reference:** [Section, field, or rule in the spec this TC is based on]
- **Precondition:** [Conditions before execution]
- **Steps:**
  1. [Step 1]
  2. [Step 2]
  ...
- **Expected Result:** [What should happen — specific and observable]
- **Test Data:** [Suggested test data if applicable]
```

> `Spec Reference` is mandatory. If a test case cannot be traced back to a specific section, field, or rule in the spec, it must not be generated.

**Example output for a Login feature:**

```
### TC-001: Successful login with valid credentials

- **Type:** Happy Path
- **Priority:** Critical
- **Spec Reference:** Section 2 — User Flow (step 4: valid credentials → redirect to Dashboard)
- **Precondition:** User has an active account in the system
- **Steps:**
  1. Open the Login page
  2. Enter a valid email
  3. Enter the correct password
  4. Click the "Login" button
- **Expected Result:** User is redirected to the Dashboard; username is displayed in the header
- **Test Data:** email: user@example-test.com / password: Test@1234

---

### TC-002: Login fails with incorrect password

- **Type:** Negative Case
- **Priority:** Critical
- **Spec Reference:** Section 3 — Validation Rules (password mismatch → show error)
- **Precondition:** User has an active account
- **Steps:**
  1. Open the Login page
  2. Enter a valid email
  3. Enter an incorrect password
  4. Click the "Login" button
- **Expected Result:** An appropriate error message is displayed; user is not redirected
  ⚠️ Exact error message text not specified in spec — needs confirmation from PO
- **Test Data:** email: user@example-test.com / password: WrongPass123

---

### TC-003: XSS attempt in email field

- **Type:** Security Case
- **Priority:** Critical
- **Spec Reference:** Section 3 — Email field (free-text input queried against DB → XSS + SQLi triggers)
- **Precondition:** Login page is open
- **Steps:**
  1. Enter `<script>alert('XSS')</script>` in the email field
  2. Enter any password
  3. Click "Login"
- **Expected Result:** Input is rejected or sanitized; no script executes; an appropriate validation error is shown
- **Test Data:** email: `<script>alert('XSS')</script>` / password: AnyPass123
```

---

## 8. Processing Workflow

```
[Input: spec.md]
       ↓
[Step 1] Analyze the spec
  - Identify the main feature
  - List all user flows
  - Identify input fields & validation rules
  - Identify business rules
  - Detect security triggers (free-text fields, passwords, tokens, form submissions)
       ↓
[Step 2] Generate test cases by category
  - Happy path: main success flows
  - Edge case: boundary values, limit scenarios
  - Negative case: wrong, missing, or invalid input
  - Security case: XSS, SQLi, credential exposure, token integrity, rate limiting, CSRF
    (auto-triggered based on security signals found in Step 1)
       ↓
[Step 3] Assign priority
  - Critical: main flows, severe impact if failed
  - Major: important features with available workarounds
  - Minor: minor UI issues, UX improvements
       ↓
[Step 4] Build AC Coverage Matrix
  - Map each Acceptance Criterion / Business Rule to the TCs that cover it
  - If no explicit ACs in spec, derive from user flows and business rules
       ↓
[Step 5] Format output
  - Structured Markdown
  - Has ID, all fields complete, Spec Reference and Technique fields mandatory
  - Easy to copy into a sheet
       ↓
[Step 6] Anti-hallucination self-check
  - Every TC must cite a Spec Reference
  - Expected results must be grounded in the spec
  - Test data must respect constraints from the spec
  - No invented rules, no invented error messages
  - Flag or remove any TC that cannot be traced to the spec
       ↓
[Output: List of test cases]
```

---

## 9. Skill Edge Cases

| Scenario                                 | How it's handled                                                   |
| ---------------------------------------- | ------------------------------------------------------------------ |
| Spec is too short or missing information | Skill asks: "The spec is missing info about [X], can you clarify?" |
| Spec describes multiple features at once | Skill separates each feature and generates cases individually      |
| Complex validation rules                 | Additional boundary test cases are generated                       |
| Ambiguous or unclear expected behavior   | Test case is flagged with "⚠️ Needs clarification from PO"         |

---

## 10. Definition of Done

- [ ] Feed any spec.md → output at least 10 fully structured test cases
- [ ] Covers all 4 types: happy path, edge case, negative case, security case
- [ ] Security cases auto-generated whenever spec contains: free-text input fields, passwords, tokens, form submissions, or DB-queried fields
- [ ] Each test case has: ID, type, priority, **Technique**, **Spec Reference**, precondition, steps, expected result
- [ ] Every Spec Reference points to an actual section/rule/field in the input spec
- [ ] No invented validation rules, error messages, or behaviors not stated in the spec
- [ ] Ambiguous or unspecified items flagged with ⚠️ — not guessed
- [ ] Output is in Markdown and can be copied directly into a sheet
- [ ] Speed: from spec input to output in under 2 minutes

---

## 11. Current Limitations

- Only processes `.md` spec files; PDF/Word/Confluence not supported
- No integration with test management tools (Jira, TestRail...)
- Output quality depends on input spec quality
- Does not auto-generate complex test data (suggestions only)

---

## 12. Roadmap

| Phase | Feature                                                                             |
| ----- | ----------------------------------------------------------------------------------- |
| v1.1  | Testing technique labels (BVA/EP/State Transition) + AC Coverage Matrix ✅          |
| v1.2  | Export template for Google Sheets; TestRail CSV export                              |
| v2.0  | Combine with report template: QC fills pass/fail → AI generates test summary report |
| v2.1  | Support input from Confluence / Notion pages                                        |

---

_This spec was created using Claude as required by the Cook A Skill Guidebook._  
_Pending approval from Supervisor (Hân/Hano) before starting the build phase._
