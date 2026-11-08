# Branch Manager User Stories

## Previous version — historical baseline

Earliest available stakeholder section in the pre-refactor working file. This role had no story file in the initial documentation commit. The wording below is preserved as history.

**Status: historical.** Read the consolidated section below for current requirements.

## Added — 2026-10-29, 18:54 (Asia/Colombo, UTC+05:30)

A branch manager is an employee with delegated responsibility for one or more
gyms within a tenant. The shared employee stories in [employee.md](employee.md)
also apply. These stories add branch oversight; they do not automatically grant
owner privileges or introduce a new role beyond the planned manager role.
Hardware integration is part of the current scope.

### MGR-01 — Oversee my assigned branches

As a branch manager, I want a summary of my assigned gyms so that I can oversee
daily operations.

Acceptance criteria:

- I can review attendance, pending registrations, expiring memberships, and
  overdue balances for authorized branches.
- Financial information is visible only when my role permits it.
- Every result identifies its branch, date range, and local time context.
- I cannot view another tenant or a branch outside my assigned access.

### MGR-02 — Resolve registration and attendance exceptions

As a branch manager, I want to review registration and entry problems so that
members receive a clear resolution.

Acceptance criteria:

- I can approve, reject, or suspend a branch registration only when authorized,
  with the responsible person and time recorded.
- I can distinguish pending registration, expired membership, unpaid balance,
  missing branch entitlement, and unknown terminal mapping.
- Policy update (2026-11-03): attendance corrections require a reason and
  preserve history, but neither managers nor staff can use manual attendance to
  bypass unpaid or expired subscription restrictions. There is no grace period.
- A successful biometric match identifies the member; membership and entry
  eligibility are evaluated separately.

### MGR-03 — Follow up with members who stop attending

As a branch manager, I want to review low-attendance and lapsed-membership lists
so that I can contact members who may need help renewing or returning.

Acceptance criteria:

- Results include the last visit, attendance window, membership dates, and
  balance when my role permits access.
- New members and members who have never attended are distinguishable.
- I can inspect the records behind a result before deciding to contact the
  member.
- Automated low-attendance messages require a separate agreed rule; they are not
  sent merely because a member appears in a report.

### MGR-04 — Monitor terminal availability and enrollment problems

As a branch manager, I want to see the status of my branch's terminals so that
attendance problems can be resolved quickly.

Acceptance criteria:

- Each terminal shows its branch, gateway, connection status, last contact, and
  supported methods: fingerprint, face, or both.
- I can distinguish an enrollment failure, unknown member mapping, failed match,
  and event-delivery problem when the hardware exposes that information.
- An unresolved device problem can be referred to an authorized administrator
  without exposing biometric data.
- Staff can use permitted manual attendance while hardware is unavailable, and
  the manual source is recorded.

---

## Consolidated additions and changes — 2026-11-08 (Asia/Colombo)

All later additions are combined here by topic. This section supersedes conflicting statements above and in the archived dated updates. Acceptance criteria describe intended behavior, not completed implementation.

Read the [current shared rules](current-rules.md) with these stakeholder stories. They define the detailed eligibility, scope, retention, and synchronization rules referenced below.


### MGR-01 — Oversee assigned branches

As a branch manager, I want a branch summary so that I can oversee daily work without owner-wide privileges.

Acceptance criteria:

- View permitted attendance, pending approvals, ending memberships, unpaid invoices, and finances only where authorized. Label branch/date/time context; reject other tenants/branches.

### MGR-02 — Resolve registration and attendance exceptions

As a branch manager, I want clear entry problems so that I can guide staff to the correct resolution.

Acceptance criteria:

- Approve/reject/suspend only when authorized; record actor/time/reason. Separate biometric recognition from entry permission.
- Apply R03/R04 enrollment versus entry checks; no unpaid/expired membership grace or manual bypass. Attendance corrections require reasons/history and follow daily-success uniqueness.

### MGR-03 — Follow up on low attendance and lapses

As a branch manager, I want understandable member trends so that I can decide whom to contact.

Acceptance criteria:

- Show actual last successful attendance, review window/count, membership dates, and permitted balances; distinguish new and never-attended members.
- Inspect source records and distinguish continuous renewals from returns after gaps. Automatic absence messaging remains deferred.

### MGR-04 — Monitor branch terminals

As a branch manager, I want device status so that I can identify enrollment and entry problems.

Acceptance criteria:

- Show gateway where deployed, connectivity/last contact, methods, capacity, and confirmed sync. Distinguish missing mapping, credential, permission, and delivery failures.
- Escalate hardware problems without exposing biometric content; permitted manual attendance still enforces R03/R07.

### MGR-TERM01 — Resolve synchronization problems

As an authorized branch manager, I want per-terminal progress so that I can retry failures safely.

Acceptance criteria:

- Show face/fingerprint pending/success/failure separately; incompatibility/capacity and offline status cannot be marked successful without confirmation.
- View platform-configured daily local sync schedule, last/next run, and outcome; prompt activation/renewal/removal does not wait for it.

### MGR-PH1-01 — Manage capacity and biometric lifecycle

As an authorized branch manager, I want clear removal and restoration state so that capacity cleanup does not silently block returning members.

Acceptance criteria:

- Apply independent 80% thresholds, more-than-30-day member inactivity, staff exclusion, and branch-wide type-specific eviction under R08.
- Use R09 permitted buttons with scope confirmation/audit. Restore retained data only after eligibility/capacity checks; deleted data needs physical capture again.
- Explain separate 12-month profile and configurable default 3-month biometric retention after last membership end; offline removal remains pending.

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

### Open decisions and source history

The [shared open decisions](current-rules.md#open-decisions--do-not-treat-these-as-settled-acceptance-criteria) remain unresolved; no acceptance criterion here selects an answer. The complete [pre-consolidation file](history/2026-11-08-before-consolidation/branch-manager.md) preserves every dated addition and intermediate correction.
