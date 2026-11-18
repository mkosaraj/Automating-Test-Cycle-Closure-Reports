# Test Closure Report: Money Transfer (Mobile Banking App)

**Report date:** 2026-11-18 | **Prepared by:** QA Lead | **Source:** test_results.csv

## 1. Summary

15 test cases were executed: 9 passed, 5 failed and 1 was blocked, a **60.0% pass rate**. The run found 5 defects (1 Critical, 3 Major, 1 Minor). The main transfer flow works. Every failure is on an edge path: OTP delivery, network loss, peak load, invalid input or small screens. The most serious defect is a duplicate debit after retry (DEF-102). **The exit criteria are not met. Recommendation: No-Go.**

## 2. Scope and Objectives of the Test Cycle

**In scope (7 modules):** Transfer, Authentication, Validation, Reliability, Security, Performance and UI.

**Objectives:**
- Confirm that transfers work correctly: between own accounts, to saved and new payees, and when scheduled or cancelled.
- Confirm authentication and security controls: OTP, biometric approval and session timeout.
- Confirm that limits and invalid input are rejected (validation).
- Check behaviour when the network drops (reliability) and with 500 users (performance).
- Check how the UI works on small screens and that receipts can be downloaded.

## 3. Execution Metrics

| Metric | Value |
|---|---|
| Total test cases | 15 |
| Passed | 9 |
| Failed | 5 |
| Blocked | 1 |
| Pass rate (of all tests) | 60.0% (9/15) |
| Pass rate (of tests that ran, excluding blocked) | 64.3% (9/14) |
| High priority pass rate | 66.7% (6/9) |
| Medium priority pass rate | 40.0% (2/5) |
| Low priority pass rate | 100% (1/1) |
| Total execution time | 74.1 s |

| Module | Total | Passed | Failed | Blocked |
|---|---|---|---|---|
| Transfer | 5 | 4 | 0 | 1 |
| Validation | 3 | 2 | 1 | 0 |
| Authentication | 2 | 1 | 1 | 0 |
| UI | 2 | 1 | 1 | 0 |
| Reliability | 1 | 0 | 1 | 0 |
| Performance | 1 | 0 | 1 | 0 |
| Security | 1 | 1 | 0 | 0 |

## 4. Defect Summary

| Defect ID | Test ID | Severity | Description |
|---|---|---|---|
| DEF-101 | TC03 | Critical | OTP not received within 60 seconds when transferring to a new payee |
| DEF-102 | TC06 | Major (recommend raising to Critical) | Duplicate debit after automatic retry during network loss. Blocks TC13. |
| DEF-103 | TC09 | Minor (recommend raising to Major) | Confirm button hidden on 5-inch screens |
| DEF-104 | TC12 | Major | Generic error instead of a message on the field for an invalid beneficiary account number |
| DEF-105 | TC15 | Major | Response time above the 5-second target with 500 users |

**By severity:** Critical 1, Major 3, Minor 1. All 5 are open.

## 5. Key Insights

- **The main flow is stable.** The Transfer module had no failures (4 passed, 1 blocked). Security (TC11) and biometric approval (TC14) passed.
- **Failures cluster where the app depends on something outside it.** OTP delivery (DEF-101), the network (DEF-102) and capacity under load (DEF-105) account for 3 of the 5 failures, all on High priority tests.
- **Money is at risk.** DEF-102 debits the customer twice. It is rated Major but should be Critical.
- **UI problems stop users from finishing.** The hidden Confirm button (DEF-103) and generic error messages (DEF-104) block or confuse users. DEF-103's Minor rating understates this.
- **Validation of limits works.** The daily limit (TC04) and insufficient balance (TC05) responses are correct.

## 6. Recommendations

Ranked by risk to customers' money first, then security, then user experience.

| Rank | Recommendation | Addresses |
|---|---|---|
| P1 | Make every transfer safe to retry: give each transfer a unique reference, show a "pending" status instead of retrying automatically, and reconcile transfers | DEF-102 (TC06), unblocks TC13 |
| P2 | Make OTP delivery reliable: monitor delivery, add a backup SMS provider and a resend option with rate limits, and offer biometric or in-app approval as an alternative | DEF-101 (TC03) |
| P3 | Tune performance and scale for peak load, and disable the button after the first tap | DEF-105 (TC15) |
| P4 | Show errors on the field that's wrong, and display the beneficiary's name before the user confirms | DEF-104 (TC12) |
| P5 | Keep the Confirm button visible on small screens, and test on a wider range of devices | DEF-103 (TC09) |
| P6 | Add automated tests for retries, network loss, OTP delivery and load, and agree severity rules | All |

## 7. Open Risks and Blocked Tests

| Item | Type | Risk |
|---|---|---|
| TC13: Transfer history update | Blocked | Not tested until DEF-102 is fixed. Transfer history may be wrong if a debit is duplicated. |
| DEF-102 | Open risk | Customers lose money, which also creates compliance and reputational risk |
| DEF-101 | Open risk | Users cannot pay new payees |
| DEF-105 | Open risk | Slow responses at peak may make users tap again, which increases the DEF-102 risk |
| Severity ratings | Process risk | DEF-102 and DEF-103 are rated lower than their impact, so they may get less attention than they need |

## 8. Exit Criteria Assessment

| Criterion | Target | Actual | Status |
|---|---|---|---|
| No open Critical defects | 0 | 1 (DEF-101), or 2 if DEF-102 is raised to Critical | **Not met** |
| Pass rate | ≥ 95% | 60.0% (9/15) | **Not met** |

**Neither exit criterion is met.**

## 9. Release Recommendation

**No-Go.** The Money Transfer feature should not be released. Before release:

1. Fix DEF-102 and DEF-101, and confirm the fixes.
2. Fix DEF-105, DEF-104 and DEF-103.
3. Run TC13, retest all 5 failed cases and run regression on the transfer flows.
4. Check the exit criteria again: no open Critical defects and a pass rate of at least 95%.
