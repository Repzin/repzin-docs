# Employee User Stories

## Previous version — historical baseline

Original committed stories from `c5d0c1f` (initial documentation commit). The
wording below is preserved; it may describe superseded or future behavior.

**Status: historical.** Read the consolidated section below for current
requirements.

1. As an employee, I want to perform tasks assigned by the gym owner so I can do
   my job.
    - Show only sections I am permitted to access.

2. As an employee, I want to view the changes I have made to the system for
   transparency.
    - Allow filtering by date.
    - Clearly display each activity type.

---

## Consolidated additions and changes — 2026-11-08 (Asia/Colombo)

All later additions are combined here by topic. This section supersedes
conflicting statements above and in the archived dated updates. Acceptance
criteria describe intended behavior, not completed implementation.

Read the [current shared rules](current-rules.md) with these stakeholder
stories. They define the detailed eligibility, scope, retention, and
synchronization rules referenced below.

### EMP-01 — Work only in authorized branches

As an employee, I want a clear branch and permission scope so that I perform
only assigned work.

Acceptance criteria:

- Show selected branch and permitted actions; backend rejects unauthorized or
  disabled-account operations even through direct requests.

### EMP-02 — Register a member accurately

As an employee, I want to search and verify details so that I avoid duplicate
member records.

Acceptance criteria:

- Collect contacts, verify phone with a limited-use OTP, and assign the readable
  member number under R02. A shared phone does not merge people.
- Show pending approval; approve only with permission. Phone changes need fresh
  verification; never display OTP values in history.

### EMP-03 — Sell or renew a membership

As an employee, I want to review dates, price, and branch so that I sell the
right coverage.

Acceptance criteria:

- Require branch approval, snapshot the package terms, reject overlapping
  confirmed periods, and preserve old periods. Phase-one coverage is one branch.

### EMP-04 — Collect one full invoice payment

As an employee, I want to review an invoice and issue its receipt so that
collections stay accurate.

Acceptance criteria:

- Check payer, tenant, branch permission, currency, and total; collect the full
  invoice only. No installments or splitting across invoices.
- Show independent charge lines and receipt reference; record
  collector/branch/time/method and reject duplicate submission effects.

### EMP-05 — Record or correct daily attendance

As an employee, I want an authorized manual fallback so that eligible members
have accurate attendance when devices fail.

Acceptance criteria:

- Apply the same paid/current/approved entry checks as terminals; no payment or
  expiry override. One successful presence per branch-local day, not several
  visits.
- Record source/operator and actual time. Corrections need permission/reason and
  traceability without inflating totals; follow R07.

### EMP-06 — Resolve exceptions safely

As an employee, I want clear failure reasons so that I know what to fix or refer
to a manager.

Acceptance criteria:

- Distinguish approval, fee/payment, expired coverage, suspension, missing
  credentials, unknown mapping, and offline sync.
- Never guess unknown device identity or bypass financial/access restrictions.
  Refer unauthorized refunds/corrections to authorized staff.

### EMP-07 — Review my actions

As an employee, I want a dated activity history so that I can explain my work.

Acceptance criteria:

- Filter permitted activity by date, action, and branch; show affected record
  and correction reasons where allowed. I cannot rewrite/delete history.

### EMP-08 — Capture and synchronize biometrics

As an authorized employee, I want to guide physical terminal enrollment so that
credentials belong to the correct person.

Acceptance criteria:

- Select person, branch terminal, and the member's registered
  face/fingerprint/both method. Apply R04 fee-enabled/no-fee eligibility before
  capture and queued work.
- Request remote capture if supported or use manual device enrollment; no portal
  image upload. Resolve unmatched local identities explicitly.
- Capture once, sync compatible same-branch terminals, preserve mapping history,
  and show face/fingerprint progress separately. Staff follow their separate
  authorization flow.

### EMP-10 — Explain reminder delivery

As an authorized employee, I want recent delivery status so that I can answer
member questions.

Acceptance criteria:

- Show tenant-selected channels, unavailable email integration, and per-channel
  supported outcomes. Members have no individual channel override.
- Use R10 metadata-only monthly retention; do not display full rendered messages
  or OTPs. Payment/renewal cancels obsolete reminders.

### EMP-PH1-01 — Restore or re-enroll a returning person

As an authorized employee, I want clear biometric actions so that I can help
someone return safely.

Acceptance criteria:

- Use R09 buttons only where my role permits; retained credentials can be
  restored after eligibility/capacity checks. Deleted biometrics need fresh
  capture.
- Explain terminal-only removal versus central deletion versus access
  suspension. Show per-type/per-terminal confirmation, retry failures, and never
  claim an offline terminal is ready.

### Superseded requirements

This table explains the main replacements; it does not introduce alternative
operating modes.

| Earlier wording                                                                                       | Current replacement                                                                                                                                      |
| ----------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Multiple visits or first attempt, including unclear denied attempts                                   | First successful access per member/branch/local day; denied and later attempts do not create attendance (R07).                                           |
| Multi-branch packages or cross-branch biometric distribution in phase one                             | Tenant can manage branches; each membership and biometric distribution stay within one branch (R01).                                                     |
| Member-specific reminder preferences and broad reminder milestones                                    | Tenant-wide channel setting; default one-day-before-end reminder with owner-configurable lead time (R10).                                                |
| Partly-paid balances, installments, or split invoice settlement                                       | One full payment for one invoice; multiple separate invoices remain possible (R03).                                                                      |
| Enroll everyone during registration; always require paid membership; or allow free registration alone | Fee-enabled tenant: paid registration fee suffices. No-fee tenant: current fully paid branch membership required. Branch approval always required (R04). |
| 60-day central biometric retention with unclear anchor                                                | Configurable default 3 calendar months after latest membership end; separate 12-month member-profile retention (R08).                                    |
| Face-only or affected-terminal-only cleanup                                                           | Independent face/fingerprint 80% thresholds and branch-wide removal of the affected credential type (R08).                                               |
| Entry/offline behavior left unspecified or inferred from connectivity                                 | Terminal authorizes locally; new offline changes remain pending until acknowledged (R05).                                                                |
| Only mapping/status retained; biometrics never centrally retained                                     | Securely retain captured biometric data separately from ordinary profile fields for same-branch distribution and its limited retention period (R04/R08). |

### Previous story IDs merged into this section

Use this mapping when following older references. The source wording is
preserved in the history snapshot.

| Previous ID | Current story |
| ----------- | ------------- |
| EMP-09      | EMP-04        |
| EMP-11      | EMP-02        |
| EMP-TERM01  | EMP-08        |

### Open decisions and source history

The
[shared open decisions](current-rules.md#open-decisions--do-not-treat-these-as-settled-acceptance-criteria)
remain unresolved; no acceptance criterion here selects an answer. The complete
[pre-consolidation file](history/2026-11-08-before-consolidation/employee.md)
preserves every dated addition and intermediate correction.
