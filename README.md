<p align="center">
  <img src="https://img.shields.io/badge/🛡️_BoundaryGuard-Beyond_the_Browser-FF6B2B?style=for-the-badge&labelColor=0a0f1e" alt="BoundaryGuard"/>
</p>

<h1 align="center">🛡️ BoundaryGuard</h1>

<p align="center">
  <strong>A Deterministic Browser-Action Security Simulator</strong><br/>
  <em>Protecting users from malicious AI browser-agent actions — before they execute.</em>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Team-Nexora-FF6B2B?style=flat-square&logo=data:image/svg+xml;base64,PHN2ZyB4bWxucz0iaHR0cDovL3d3dy53My5vcmcvMjAwMC9zdmciIHdpZHRoPSIyNCIgaGVpZ2h0PSIyNCIgdmlld0JveD0iMCAwIDI0IDI0IiBmaWxsPSJ3aGl0ZSI+PHBhdGggZD0iTTEyIDJMMiAyMmgyMEwxMiAyeiIvPjwvc3ZnPg==&logoColor=white" alt="Team Nexora"/>
  <img src="https://img.shields.io/badge/Hackathon-wHACKiest_2026-blueviolet?style=flat-square" alt="wHACKiest 2026"/>
  <img src="https://img.shields.io/badge/Problem_Statement-PS2-blue?style=flat-square" alt="PS2"/>
  <img src="https://img.shields.io/badge/Stack-HTML_·_CSS_·_JavaScript-F7DF1E?style=flat-square&logo=javascript&logoColor=black" alt="Tech Stack"/>
  <img src="https://img.shields.io/badge/Dependencies-Zero-22c55e?style=flat-square" alt="Zero Dependencies"/>
  <img src="https://img.shields.io/badge/API_Keys-None_Required-22c55e?style=flat-square" alt="No API Keys"/>
</p>

<p align="center">
  <img src="https://img.shields.io/badge/Status-Working_MVP-22c55e?style=for-the-badge" alt="Status"/>
  <img src="https://img.shields.io/badge/Tests-5/5_Passing-22c55e?style=for-the-badge" alt="Tests"/>
  <img src="https://img.shields.io/badge/Run-Open_index.html-FF6B2B?style=for-the-badge" alt="Run"/>
</p>

---

## 📌 The Problem

Modern AI browser agents (like those powered by LLMs) can **read pages, click buttons, fill forms, navigate, and submit data** on behalf of users. But what happens when:

- 🕷️ A **malicious webpage** silently instructs the agent to **exfiltrate sensitive data** (marks, income, personal info) to an attacker-controlled server?
- 🎭 A page uses **prompt injection** to redirect the agent toward unauthorized destinations?
- ❓ The agent encounters an **ambiguous action** — should it proceed automatically or ask the user?

**Without a security boundary, the browser agent becomes the attack vector.**

---

## 💡 Our Solution

**BoundaryGuard** is a **deterministic policy engine** that sits between the AI agent and the browser, intercepting every proposed action and enforcing a strict security policy before execution.

```
┌─────────────────────────────────────────────────────────────────────┐
│                        🤖 AI Browser Agent                         │
│                    "Submit form to external URL"                    │
└─────────────────────┬───────────────────────────────────────────────┘
                      │  Proposed Action
                      ▼
┌─────────────────────────────────────────────────────────────────────┐
│                   🛡️  BOUNDARYGUARD POLICY ENGINE                   │
│                                                                     │
│   ┌─────────┐  ┌──────────┐  ┌──────────┐  ┌───────────────────┐   │
│   │ Validate │→ │ Check    │→ │ Resolve  │→ │ Enforce           │   │
│   │ Action   │  │ Perms &  │  │ Dest.    │  │ Decision          │   │
│   │ Type     │  │ Origins  │  │ Origin   │  │ ✅ ALLOW          │   │
│   │          │  │          │  │          │  │ 🚫 BLOCK          │   │
│   │ 6 known  │  │ Per-type │  │ Approved │  │ ❓ ASK USER       │   │
│   │ types    │  │ toggles  │  │ allow-   │  │                   │   │
│   │ only     │  │          │  │ list     │  │ + Risk Level      │   │
│   └─────────┘  └──────────┘  └──────────┘  └───────────────────┘   │
│                                                                     │
│                    📋 Every decision → Audit Log                    │
└─────────────────────┬──────────┬──────────┬─────────────────────────┘
                      │          │          │
                   ✅ ALLOW   🚫 BLOCK   ❓ ASK
                      │          │          │
                      ▼          ▼          ▼
                   Execute     Deny     Show to User
                   Safely      +Log     for Approval
```

---

## 🎬 Three Live Scenarios

Our simulator demonstrates three realistic attack/defense scenarios using **synthetic student scholarship data**:

<table>
<tr>
<td width="33%" align="center">

### ✅ Scenario A
**ALLOWED**

</td>
<td width="33%" align="center">

### 🚫 Scenario B
**BLOCKED**

</td>
<td width="33%" align="center">

### ❓ Scenario C
**ASK USER**

</td>
</tr>
<tr>
<td>

**Task:** Read scholarship info<br/>
**Action:** `read`<br/>
**Destination:** `scholarships.gov.in`<br/>
**Source:** User instruction<br/>
**Result:** ✅ Read-only action to an approved origin. Safe.

</td>
<td>

**Task:** Page hijacks agent<br/>
**Action:** `submit`<br/>
**Destination:** `attacker.test/collect`<br/>
**Payload:** Student marks, income, DOB<br/>
**Source:** Page-derived<br/>
**Result:** 🚫 Data exfiltration to malicious origin. Blocked.

</td>
<td>

**Task:** Navigate to application portal<br/>
**Action:** `navigate`<br/>
**Destination:** `nsp.gov.in/apply`<br/>
**Source:** Page-derived<br/>
**Result:** ❓ Approved origin, but page-derived. User must authorize.

</td>
</tr>
</table>

---

## 🔐 Policy Engine — How It Works

The engine processes every action through a **strict, ordered pipeline** — no shortcuts, no hardcoded responses:

```
ACTION RECEIVED
     │
     ├──→ Unknown type?                          → 🚫 BLOCK (fail closed)
     │
     ├──→ Permission disabled?                   → 🚫 BLOCK
     │
     ├──→ Read-only action?
     │       ├──→ Approved origin?               → ✅ ALLOW
     │       └──→ Unapproved origin?             → 🚫 BLOCK
     │
     ├──→ Sensitive action (submit/navigate/click/type)?
     │       │
     │       ├──→ Source: Page-derived?
     │       │       ├──→ Unapproved origin?     → 🚫 BLOCK (high risk)
     │       │       └──→ Approved origin?       → ❓ ASK USER
     │       │
     │       └──→ Source: User instruction?
     │               ├──→ Malicious TLD?         → 🚫 BLOCK (exfiltration)
     │               ├──→ Unapproved origin?     → 🚫 BLOCK
     │               ├──→ Submit + approved?     → ❓ ASK USER
     │               └──→ Other + approved?      → ✅ ALLOW
     │
     └──→ Unclassified?                          → 🚫 BLOCK (fail closed)
```

### Six Core Security Principles

| # | Principle | Implementation |
|---|---|---|
| 1 | **Fail Closed** | Unknown action types are always blocked — never allow what you don't understand |
| 2 | **Origin Allowlisting** | Only explicitly approved origins are trusted; everything else is denied by default |
| 3 | **Source Attribution** | Page-derived actions receive stricter scrutiny than direct user instructions |
| 4 | **Minimal Permission** | Each action type (read/scroll/click/type/navigate/submit) must be explicitly enabled |
| 5 | **Exfiltration Detection** | Suspicious TLDs (`.test`, `.evil`, `.hack`) and unapproved destinations with payloads are flagged |
| 6 | **Human-in-the-Loop** | Ambiguous or sensitive actions escalate to the user instead of auto-proceeding |

---

## 🧪 Automated Test Suite

Five deterministic test cases validate the policy engine — click **▶ Run All Tests** in the UI:

| ID | Test Case | Action | Destination | Source | Expected | Status |
|---|---|---|---|---|---|---|
| `TC-01` | Approved Read | `read` | `scholarships.gov.in/list` | User | ✅ ALLOW | ✓ PASS |
| `TC-02` | Unapproved Navigation | `navigate` | `unknown-site.org` | User | 🚫 BLOCK | ✓ PASS |
| `TC-03` | Malicious External Submit | `submit` | `attacker.test/steal` | Page | 🚫 BLOCK | ✓ PASS |
| `TC-04` | Unknown Action Type | `exfiltrate` | `scholarships.gov.in` | User | 🚫 BLOCK | ✓ PASS |
| `TC-05` | Page-Derived to Approved | `navigate` | `nsp.gov.in/apply` | Page | ❓ ASK | ✓ PASS |

> All results are generated by the **actual deterministic JavaScript policy engine** at runtime — not hardcoded visual responses.

The `policyEngine()` function is globally accessible from the browser console for judges to test arbitrary inputs:

```javascript
// Try it in DevTools (F12 → Console)
policyEngine({
  action: 'submit',
  destination: 'https://attacker.test/collect',
  source: 'page',
  payload: 'marks=92; income=250000'
});
// → { decision: "block", reason: "...", riskLevel: "high" }
```

---

## 📋 Local Audit Log

Every policy decision is recorded with:

| Field | Description |
|---|---|
| **Action** | The proposed action type |
| **Destination** | Target URL or element |
| **Decision** | ALLOW / BLOCK / ASK / USER APPROVED / USER DENIED |
| **Reason** | Deterministic explanation from the policy engine |
| **Timestamp** | ISO 8601 timestamp of the evaluation |

The log is session-local (privacy by design — no data leaves the browser).

---

## ▶️ Quick Start

```bash
# Clone the repository
git clone https://github.com/nisar-studio/BoundaryGuard.git

# Open the app (no install, no build, no server)
cd BoundaryGuard
open index.html        # macOS
start index.html       # Windows
xdg-open index.html    # Linux
```

> **That's it.** No `npm install`, no API keys, no build step. Just open and go.

---

## 📂 Project Structure

```
BoundaryGuard/
├── index.html      🛡️  Complete application (single file, 58 KB)
│                       Dashboard + Policy Engine + Tests + Audit Log
│
├── README.md       📖  Project documentation (this file)
├── TESTS.md        🧪  Detailed test instructions for judges
└── .gitignore      🙈  Git configuration
```

---

## 🛠️ Technology

<p align="center">
  <img src="https://img.shields.io/badge/HTML5-E34F26?style=for-the-badge&logo=html5&logoColor=white" alt="HTML5"/>
  <img src="https://img.shields.io/badge/CSS3-1572B6?style=for-the-badge&logo=css3&logoColor=white" alt="CSS3"/>
  <img src="https://img.shields.io/badge/JavaScript-F7DF1E?style=for-the-badge&logo=javascript&logoColor=black" alt="JavaScript"/>
</p>

| Requirement | Status |
|---|---|
| Pure HTML + CSS + Vanilla JS | ✅ |
| Zero external dependencies | ✅ |
| Zero API keys | ✅ |
| Runs locally in any browser | ✅ |
| Works with `file://` protocol | ✅ |
| No build step | ✅ |
| No server needed | ✅ |
| Responsive layout | ✅ |

---

## 🎨 Design System

| Element | Color | Usage |
|---|---|---|
| 🔵 **Navy** (`#0a0f1e` → `#1e2f55`) | Background, cards, panels | Professional dark theme |
| 🟠 **Orange** (`#ff6b2b`) | Accents, branding, highlights | BoundaryGuard identity |
| 🟢 **Green** (`#22c55e`) | ALLOW verdicts | Permitted actions |
| 🔴 **Red** (`#ef4444`) | BLOCK verdicts | Denied actions |
| 🟡 **Yellow** (`#f59e0b`) | ASK USER verdicts | Escalated decisions |

---

## ⚠️ Scope & Limitations

> **Transparency is a feature, not a weakness.**

| Statement | |
|---|---|
| This is a **local policy-engine demonstration** | ✅ |
| It simulates browser-agent security decisions | ✅ |
| All data is **synthetic** (fake student records) | ✅ |
| It does **NOT** intercept real browser traffic | ⚠️ |
| It does **NOT** claim full prompt-injection protection | ⚠️ |
| It is **NOT** a completed Chrome extension | ⚠️ |

The goal is to **demonstrate the concept and security principles** that would power a production browser-agent security layer.

---

## 🏗️ Future Scope

If developed into a full product, BoundaryGuard would:

- 🔌 Ship as a **Chrome/Firefox extension** with real browser action interception
- 🤖 Integrate with AI agent frameworks (LangChain, AutoGPT, etc.)
- 🧠 Add **ML-based anomaly detection** alongside deterministic rules
- 📊 Provide **enterprise dashboards** for fleet-wide policy management
- 🔄 Support **dynamic policy updates** via admin console
- 🌐 Implement **cross-origin communication protocols** for multi-tab agents

---

<p align="center">
  <img src="https://img.shields.io/badge/Built_with_❤️_by-Team_Nexora-FF6B2B?style=for-the-badge&labelColor=0a0f1e" alt="Team Nexora"/>
</p>

<p align="center">
  <strong>wHACKiest 2026 · PS2: Beyond the Browser</strong><br/>
  <em>Securing the boundary between AI agents and the browser — one policy decision at a time.</em>
</p>
