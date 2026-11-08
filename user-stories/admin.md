# Admin (Gym Owner) User Stories

## Previous version — historical baseline

Original committed stories from `c5d0c1f` (initial documentation commit). The wording below is preserved; it may describe superseded or future behavior.

**Status: historical.** Read the consolidated section below for current requirements.

1. As a gym owner, I want to register members and track their transactions.
   - Use a step-by-step wizard requiring only a name, phone number, and emergency phone number.
   - Allow biometric enrollment to be initiated during registration.
   - Assign each member a unique, human-readable member ID.

2. As a gym owner, I want to add, update, remove, publish, and unpublish membership packages.
   - Support packages with different prices and validity periods.
   - Support couple packages that apply to each member of the group.

3. As a gym owner, I want to create employee accounts with limited access and track employee activity.
   - Assign an initial password and require a password change at first login.

4. As a gym owner, I want to assign fine-grained permissions to individual employees to restrict access and hide sensitive information.

5. As a gym owner, I want to record membership payments so members can unlock the door using biometrics.
   - Show current membership details when recording a payment.
   - Display the start date and calculate the end date automatically.
   - Send an automatic confirmation with the payment date, end date, amount paid, transaction ID, and membership ID.
   - Transaction IDs do not need to be human-readable.

6. As a gym owner, I want members to complete registration themselves to reduce my workload.
   - Provide a registration URL.
   - Require gym owner approval to complete registration.

7. As a gym owner, I want to view attendance and arrival times.
   - Default to today and allow filtering by date.
   - Show all attendance records, including multiple visits on the same day.

8. As a gym owner, I want to send automatic reminders before membership packages expire.
   - Include the end date and membership ID.

9. As a gym owner, I want to send an automatic notification when a membership expires.
   - Include the same basic membership information as the expiry reminder.

10. As a gym owner, I want to send automatic messages to absent members to encourage attendance.
    - **To discuss:** Define when these messages should be sent.

11. As a gym owner, I want to view money collected in a selected month to support business decisions.

12. As a gym owner, I want to identify members who attend without paying their membership fees.

---

## Consolidated additions and changes — 2026-11-08 (Asia/Colombo)

All later additions are combined here by topic. This section supersedes conflicting statements above and in the archived dated updates. Acceptance criteria describe intended behavior, not completed implementation.

Read the [current shared rules](current-rules.md) with these stakeholder stories. They define the detailed eligibility, scope, retention, and synchronization rules referenced below.


### OWN-01 — Manage my tenant and branches

As an owner, I want to manage gyms under one business so that I can oversee several locations without mixing customer data.

Acceptance criteria:

- Create/update/deactivate branches with name, unique tenant-local code, address, contacts, and time zone; isolate other tenants completely.
- Choose a branch or authorized all-branches reporting view. Require an explicit branch on registrations, invoices, payments, and attendance; deduplicate unique member totals.

### OWN-03 — Register and approve members

As an owner, I want a clear registration and approval flow so that staff know who may join.

Acceptance criteria:

- Search existing profiles; collect contacts, verify phone by OTP, and assign a readable tenant-wide member number under R02.
- Show registration state, approver/time, and rejection/suspension reasons. Preserve member history without granting multi-branch access in phase one.

### OWN-04 — Configure and sell membership packages

As an owner, I want flexible prices and durations so that I can offer suitable memberships.

Acceptance criteria:

- Support fixed-day and calendar-month packages, including several months, priced independently; purchases preserve agreed terms.
- Publish/unpublish without deleting history; confirm approval, dates, branch, and price at sale. Renew with a new non-overlapping period. Phase-one coverage is one branch.

### OWN-06 — Invoice charges and collect full payment

As an owner, I want clear bills and receipts so that I know what is owed and collected.

Acceptance criteria:

- Use independent registration/subscription invoice lines. One full payment settles one invoice; no installments or split allocation. Several invoices can be paid separately.
- Record collector, method, branch, time, currency, and readable receipt; retries cannot collect twice. Distinguish unpaid/due/overdue from paid/voided invoices.

### OWN-07 — Correct collections and record refunds

As an owner, I want traceable corrections so that financial reports remain reliable.

Acceptance criteria:

- Require permission, reason, actor, and time; preserve issued/completed history and cap refunds at the refundable amount.
- Reflect completed refunds in balances/net collections; cancelling membership alone does not mean refunding money.

### OWN-08 — Review attendance and membership trends

As an owner, I want accurate daily presence and renewal reports so that I can understand member activity.

Acceptance criteria:

- Apply R07: first successful member access per branch-local day, denied/later attempts excluded, earliest occurrence retained under delayed delivery.
- Filter member/branch/date; show last attendance, distinct days, ending/lapsed/renewed memberships, continuous renewals, and returning members.
- Choose low-attendance window/count thresholds; distinguish new/never-attended members and show actual comparisons. Personal follow-up only; automatic absence messages are deferred.

### OWN-13 — Understand business performance

As an owner, I want traceable branch and combined reports so that I can make decisions from reliable figures.

Acceptance criteria:

- Separate invoiced sales, outstanding debt, and cash collected net of refunds. Label branch/date/currency and source records.
- Show unique-member and daily-presence totals, renewal denominator/cutoff, and upcoming/lapsed memberships; do not count repeated entry as extra visits.

### OWN-14 — Delegate branch access

As an owner, I want staff roles restricted by branch so that employees see only authorized records.

Acceptance criteria:

- Assign owner/manager/receptionist roles and permitted branches under R02. Disabling staff blocks further authorized operations; keep an active owner.
- Audit approvals, payments, refunds, corrections, and biometric actions. Individual custom permissions are future work; final role/action matrix remains open.

### OWN-16 — Choose reminders and review delivery

As an owner, I want one reminder policy for my tenant so that all branches communicate consistently.

Acceptance criteria:

- Select WhatsApp/email/both tenant-wide, with email unavailable until activated. Default expiry lead time is one day; owner can change it.
- Use tenant templates and recent metadata-only delivery history under R10. Recheck renewal/payment, cancel obsolete work, and retry channels independently.

### OWN-18 — Configure an optional registration fee

As an owner, I want one fee or no fee for my tenant so that registration charges are predictable.

Acceptance criteria:

- Snapshot one tenant-currency fee on approval and charge once per member/business. Later amount changes affect new registrations only.
- Invoice separately or with the first subscription. A required fee must be paid before capture/entry; enrollment and entry then follow their separate R04/R03 rules.

### OWN-TERM01 — Manage terminal enrollment as a branch group

As an owner, I want one branch control view so that enrollment is easy to manage across its terminals.

Acceptance criteria:

- See active/online devices, enabled methods, capacity, last contact, and per-terminal status. Capture physically once and distribute only within that branch.
- Select member method during registration. Enforce conditional fee/no-fee enrollment eligibility in R04; show separate face/fingerprint and incompatible-device outcomes.

### OWN-TERM02 — Activate, renew, and remove entry access

As an owner, I want access to reflect membership so that only eligible people enter.

Acceptance criteria:

- Send local validity promptly on activation/renewal; no backend check per entry and no grace period. Renewals need not retransfer unchanged biometrics.
- Provide Remove access; preserve explicit suspension during reconciliation. Offline changes remain pending until terminal confirmation.

### OWN-TERM03 — Enroll authorized staff

As an owner, I want staff credentials managed separately so that employees can use terminals without buying memberships.

Acceptance criteria:

- Capture staff face/fingerprint/both at a compatible branch terminal and sync within that branch using separate identity mapping.
- Authorize staff entry separately; enrollment alone does not grant entry or device administrator rights. Staff are excluded from member inactivity eviction.

### OWN-PH1-02 — Manage capacity and returning members

As an owner, I want understandable eviction and retention states so that storage limits do not silently break access.

Acceptance criteria:

- Apply independent 80% face/fingerprint thresholds and branch-wide eviction for members absent more than 30 days under R08.
- Show retained/evicted/deleted data, offline pending removals, and restoration failures. Profile retention is 12 months; biometrics default to configurable 3 months after last membership end.
- Offer Register/Re-enroll, Remove from branch terminals, Restore, Delete biometric data, Remove access, and Retry according to R09; confirm destructive scope.

### OWN-F01 — Future member self-service

As an owner, I want members to submit registration and view their information in a later release so that staff handle fewer routine requests.

Acceptance criteria:

- Future submissions require approval and verified profile linking; members see only their own data. Member portals, self-registration, and couple/family packages are outside phase one.

### Superseded requirements

This table explains the main replacements; it does not introduce alternative operating modes.

| Earlier wording | Current replacement |
| --- | --- |
| Multiple visits or first attempt, including unclear denied attempts | First successful access per member/branch/local day; denied and later attempts do not create attendance (R07). |
| Multi-branch packages or cross-branch biometric distribution in phase one | Tenant can manage branches; each membership and biometric distribution stay within one branch (R01). |
| Member-specific reminder preferences and broad reminder milestones | Tenant-wide channel setting; default one-day-before-end reminder with owner-configurable lead time (R10). |
| Partly-paid balances, installments, or split invoice settlement | One full payment for one invoice; multiple separate invoices remain possible (R03). |
| Enroll everyone during registration; always require paid membership; or allow free registration alone | Fee-enabled tenant: paid registration fee suffices. No-fee tenant: current fully paid branch membership required. Branch approval always required (R04). |
| 60-day central biometric retention with unclear anchor | Configurable default 3 calendar months after latest membership end; separate 12-month member-profile retention (R08). |
| Face-only or affected-terminal-only cleanup | Independent face/fingerprint 80% thresholds and branch-wide removal of the affected credential type (R08). |
| Entry/offline behavior left unspecified or inferred from connectivity | Terminal authorizes locally; new offline changes remain pending until acknowledged (R05). |
| Only mapping/status retained; biometrics never centrally retained | Securely retain captured biometric data separately from ordinary profile fields for same-branch distribution and its limited retention period (R04/R08). |

### Previous story IDs merged into this section

Use this mapping when following older references. The source wording is preserved in the history snapshot.

| Previous ID | Current story |
| --- | --- |
| OWN-02 | OWN-01 |
| OWN-05 | OWN-04 |
| OWN-09 | OWN-06 |
| OWN-10 | OWN-16 |
| OWN-11 | OWN-08 |
| OWN-12 | OWN-08 |
| OWN-15 | OWN-TERM01 |
| OWN-17 | OWN-06 |
| OWN-19 | OWN-16 |
| OWN-PH1-01 | OWN-04 and OWN-16 |
| OWN-PH1-03 | OWN-14 |

### Open decisions and source history

The [shared open decisions](current-rules.md#open-decisions--do-not-treat-these-as-settled-acceptance-criteria) remain unresolved; no acceptance criterion here selects an answer. The complete [pre-consolidation file](history/2026-11-08-before-consolidation/admin.md) preserves every dated addition and intermediate correction.
