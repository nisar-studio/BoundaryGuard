# 🧪 BoundaryGuard – Test Instructions

**Team:** Nexora | **wHACKiest 2026 PS2 – Beyond the Browser**

---

## Prerequisites

- Modern browser (Chrome 90+, Firefox 90+, Edge 90+)
- Open `index.html` directly (no server needed)

---

## Automated Test Suite

### How to Run

1. Open `index.html` in a browser
2. Scroll to **"Automated Test Suite"** panel (right column)
3. Click **▶ Run All Tests**
4. Watch each test execute with PASS / FAIL verdict

All 5 tests should show **✓ PASS** and the summary displays: **5/5 tests passed 🎉**

---

## Test Cases (Detailed)

### TC-01: Approved Read
| Field | Value |
|---|---|
| Action | `read` |
| Destination | `https://scholarships.gov.in/list` |
| Source | User Instruction |
| Permissions | read ✅ |
| **Expected** | **ALLOW** |
| **Reason** | Read-only action to approved origin |

**Manual Steps:**
1. Set Action → Read
2. Destination → `https://scholarships.gov.in/list`
3. Source → User Instruction
4. Enable: Read permission only
5. Click Evaluate → Should show ✅ ALLOWED

---

### TC-02: Unapproved Navigation
| Field | Value |
|---|---|
| Action | `navigate` |
| Destination | `https://unknown-site.org/page` |
| Source | User Instruction |
| Permissions | navigate ✅ |
| **Expected** | **BLOCK** |
| **Reason** | Destination not in approved origins list |

**Manual Steps:**
1. Set Action → Navigate
2. Destination → `https://unknown-site.org/page`
3. Source → User Instruction
4. Enable: navigate permission
5. Click Evaluate → Should show 🚫 BLOCKED

---

### TC-03: Malicious External Submit
| Field | Value |
|---|---|
| Action | `submit` |
| Destination | `https://attacker.test/steal` |
| Source | Page-Derived |
| Payload | `marks=92; income=250000` |
| Permissions | submit ✅ |
| **Expected** | **BLOCK** |
| **Reason** | Page-derived submit to malicious TLD with sensitive payload |

**Manual Steps:**
1. Load **Scenario B** (click the red scenario button)
2. Observe automatic evaluation
3. Result should show 🚫 BLOCKED with HIGH risk

---

### TC-04: Unknown Action Type
| Field | Value |
|---|---|
| Action | `exfiltrate` (custom - type manually in browser console) |
| Destination | `https://scholarships.gov.in` |
| Source | User Instruction |
| **Expected** | **BLOCK** |
| **Reason** | Fail-closed: unknown action types are never allowed |

**Manual Steps (Console):**
1. Open browser DevTools (F12) → Console
2. Paste and run:
```javascript
const result = policyEngine({
  action: 'exfiltrate',
  destination: 'https://scholarships.gov.in',
  source: 'user',
  payload: ''
});
console.log(result.decision); // should be "block"
console.log(result.reason);
```
3. Confirm `decision === "block"`

**Or via UI:** The automated test runner handles this automatically in TC-04.

---

### TC-05: Page-Derived Destination (Approved Origin)
| Field | Value |
|---|---|
| Action | `navigate` |
| Destination | `https://nsp.gov.in/apply` |
| Source | Page-Derived |
| Permissions | navigate ✅ |
| **Expected** | **ASK USER** |
| **Reason** | Approved origin, but page-derived source is risky (prompt injection) |

**Manual Steps:**
1. Load **Scenario C** (click the yellow scenario button)
2. Observe automatic evaluation
3. Result should show ❓ ASK USER with approval buttons visible
4. Test Approve → confirms logging to audit
5. Test Deny → confirms denial logging to audit

---

## Additional Manual Tests

### Extra A: Permission Blocking
1. **Uncheck all permissions** in Permission Configuration
2. Try any action type → Should always BLOCK (permission not granted)

### Extra B: Custom Malicious TLD
1. Action → Submit
2. Destination → `https://evil.hack/data`
3. Source → User Instruction
4. Enable submit permission
5. Click Evaluate → Should BLOCK (malicious TLD detected)

### Extra C: Origin Management
1. Click `+Add` and enter `https://mysite.com`
2. Run a navigate action to `https://mysite.com/page`
3. → Should ALLOW (now in approved list)
4. Remove the origin (× button)
5. Run same action again → Should BLOCK

### Extra D: Audit Log
1. Run several scenarios
2. Confirm each decision appears in Audit Log with timestamp
3. Click **Clear** → Log resets
4. Confirm log is session-local only (refresh clears it)

---

## Expected Full Test Run Output

```
TC-01: Approved Read             ✓ PASS
TC-02: Unapproved Navigation     ✓ PASS
TC-03: Malicious External Submit ✓ PASS
TC-04: Unknown Action Type       ✓ PASS
TC-05: Page-Derived Destination  ✓ PASS

5/5 tests passed 🎉
```

---

## Console API (for judges)

The `policyEngine()` function is globally available. Run in DevTools:

```javascript
// Test any proposal manually
policyEngine({
  action: 'submit',          // read|scroll|click|type|navigate|submit|unknown
  destination: 'https://attacker.test/collect',
  source: 'page',            // 'user' or 'page'
  payload: 'sensitive data'
});
// Returns: { decision: 'block', reason: '...', riskLevel: 'high' }
```

---

*BoundaryGuard is a local policy-engine demonstration. It does not intercept real browser traffic or claim full prompt-injection protection.*
