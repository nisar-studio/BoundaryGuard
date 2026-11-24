# 🛡️ BoundaryGuard
### Browser Action Security Simulator
**Team:** Nexora | **Hackathon:** wHACKiest 2026 – PS2: Beyond the Browser

---

## Overview

**BoundaryGuard** is a local, deterministic policy engine that simulates how a browser agent security layer evaluates and controls AI-driven browser actions before they execute. It demonstrates realistic attack scenarios — including data exfiltration, prompt injection via page content, and unauthorized navigation — and shows how a principled policy engine can block, allow, or escalate these decisions.

> ⚠️ **Scope:** This is a local demonstration simulator, NOT a Chrome extension. It does not intercept real browser traffic. All data is synthetic.

---

## ▶️ Quick Start

1. Download or clone the project folder.
2. Open `index.html` in any modern browser (Chrome, Firefox, Edge).
3. No build step. No dependencies. No API keys.

```
BoundaryGuard/
├── index.html      ← Full application (single file)
├── README.md       ← This file
└── TESTS.md        ← Detailed test instructions
```

---

## Features

| Feature | Description |
|---|---|
| **Dashboard** | Task context, approved origins, permission config, action proposal |
| **6 Action Types** | Read, Scroll, Click, Type, Navigate, Submit |
| **Policy Engine** | Deterministic JS rules — not hardcoded visual responses |
| **3 Demo Scenarios** | ALLOW / BLOCK / ASK USER with realistic student data |
| **Result Panel** | Verdict, risk level, reason, approval controls |
| **Audit Log** | Timestamped decision history (session-local) |
| **Test Suite** | 5 automated test cases with PASS/FAIL verdicts |

---

## Policy Engine Logic

The engine evaluates every proposed action through these checks **in order**:

```
1. Is the action type known?           → Unknown → BLOCK (fail closed)
2. Is the action permitted?            → Permission disabled → BLOCK
3. Is the action read-only?
   ├─ Yes → destination approved?      → ALLOW / BLOCK
   └─ No  → sensitive action...
4. Source: User Instruction vs Page-Derived?
5. Destination: Approved origin?
6. Malicious TLD detected?             → BLOCK (high risk)
7. Page-derived + approved origin      → ASK USER (prompt injection risk)
8. User + approved origin + submit     → ASK USER (data confirmation)
9. User + approved + navigate/click    → ALLOW
```

---

## Demo Scenarios

### Scenario A – ALLOW ✅
- **Task:** Read scholarship info from `scholarships.gov.in`
- **Action:** `read` | **Source:** User | **Destination:** `https://scholarships.gov.in/list`
- **Result:** ALLOW — read-only action to approved origin

### Scenario B – BLOCK 🚫
- **Task:** Page proposes to submit student marks and income
- **Action:** `submit` | **Source:** Page-Derived | **Destination:** `https://attacker.test/collect`
- **Payload:** `name=Aarav Kumar; marks=92; income=250000; DOB=2003-05-14`
- **Result:** BLOCK — page-derived submit to malicious origin, data exfiltration suspected

### Scenario C – ASK USER ❓
- **Task:** Navigate to application portal after reading eligibility
- **Action:** `navigate` | **Source:** Page-Derived | **Destination:** `https://nsp.gov.in/apply`
- **Result:** ASK USER — approved destination but page-derived source (prompt injection risk)

---

## Automated Tests

| ID | Test Case | Expected |
|---|---|---|
| TC-01 | Approved read from gov.in | ALLOW |
| TC-02 | Unapproved user navigation | BLOCK |
| TC-03 | Malicious external submit (attacker.test) | BLOCK |
| TC-04 | Unknown action type ("exfiltrate") | BLOCK |
| TC-05 | Page-derived navigate to approved origin | ASK |

Click **▶ Run All Tests** in the UI to execute all five deterministically.

---

## Security Principles Demonstrated

1. **Fail Closed** – Unknown action types are always blocked.
2. **Origin Allowlisting** – Only explicitly approved origins are trusted.
3. **Source Attribution** – Page-derived actions receive stricter scrutiny than user instructions.
4. **Minimal Permission** – Each action type must be explicitly enabled.
5. **Data Exfiltration Detection** – Suspicious TLDs and unapproved origins with payloads are flagged.
6. **Human-in-the-Loop** – Sensitive/ambiguous actions escalate to the user rather than auto-allowing.

---

## Technology

- Pure HTML5 + CSS3 + Vanilla JavaScript
- Zero external dependencies
- Zero API calls
- Runs entirely in browser (file:// protocol supported)

---

## Team Nexora · wHACKiest 2026
