# Super Admin User Stories

## Previous version — historical baseline

Original committed stories from `c5d0c1f` (initial documentation commit). The wording below is preserved; it may describe superseded or future behavior.

**Status: historical.** Read the consolidated section below for current requirements.

1. As a super admin, I want to register new gyms in the system.
   - Require a unique gym name.
   - Assign each gym a unique, human-readable gym ID.

2. As a super admin, I want to configure each gym independently.
   - Apply configuration changes immediately.

3. As a super admin, I want to configure all gyms in bulk so I can enable or disable features when issues occur.
   - Bulk changes override existing configurations.

4. As a super admin, I want to block gyms with unpaid fees and restore access when they pay.
   - Clearly show each gym's blocked or unblocked status.
   - Provide a clear notification when a gym is blocked or unblocked.

5. As a super admin, I want to register ESP32 devices for a gym so I can connect access control terminals.
   - Assign each ESP32 device a unique ID.
   - Allow the system to identify all ESP32 devices associated with a gym ID.

6. As a super admin, I want to register biometric terminals under the appropriate gym and ESP32 device.
   - Display registered devices.
   - **To discuss:** Define the required terminal information.

---

## Consolidated additions and changes — 2026-11-08 (Asia/Colombo)

All later additions are combined here by topic. This section supersedes conflicting statements above and in the archived dated updates. Acceptance criteria describe intended behavior, not completed implementation.

Read the [current shared rules](current-rules.md) with these stakeholder stories. They define the detailed eligibility, scope, retention, and synchronization rules referenced below.


### SUP-01 — Onboard a tenant and first branch

As a platform administrator, I want complete onboarding so that a business starts with an owner and usable branch.

Acceptance criteria:

- Create tenant identifier, name, currency, time zone, contacts, first branch, and active owner together; failure must not leave an ownerless partial setup.
- Branch codes are tenant-local unique; unrelated tenants may share names. Separate gym-member invoices from future platform billing.

### SUP-02 — Protect tenant boundaries

As a platform administrator, I want reliable isolation so that customer records cannot leak across tenants.

Acceptance criteria:

- Check authenticated context and branch/action permission on requests, background work, and device events. Foreign record IDs cannot reveal private data.
- Require explicit authorized support access with recorded reason. Keep global administration distinct from tenant roles.

### SUP-03 — Suspend and restore a business

As a platform administrator, I want traceable account status changes so that access can be controlled without deleting history.

Acceptance criteria:

- Record reason, actor, time, and status; show clear refusal messages and preserve records on restoration.
- Enforce an agreed suspension policy; device ingestion/offline terminal effects still need final definition. Automatic unpaid-platform-fee blocking is future billing work.

### SUP-04 — Configure settings at the right scope

As a platform administrator, I want scoped configuration so that changes affect only intended businesses and branches.

Acceptance criteria:

- Preview targets/values for bulk changes; report success/failure separately and audit actor. Explain inheritance only where supported.
- Enforce branch-scoped RBAC; evaluate a free identity provider without assuming selection. Platform login, door access, and device administration remain separate grants.

### SUP-HW01 — Provision devices and branch control groups

As a platform administrator, I want authenticated device onboarding so that events and commands reach the correct branch.

Acceptance criteria:

- Register stable identity/serial, tenant, branch, supported/enabled methods, capacity, status, and last contact. Gateways where used belong to one tenant/branch; ESP32 is not universally required.
- Verify identity/connectivity, allow authorized disabling/credential rotation, and audit. Initial sync sends required branch state with throttled per-person/type progress.
- Use generalized HWS operations; target Hikvision DS-K1T320MFWX-B without SDK first. Validate firmware transfer/capture and recognition during updates before claiming support.

### SUP-HW02 — Maintain person-to-terminal mappings

As a platform administrator, I want durable identity mappings so that terminal events never identify the wrong person.

Acceptance criteria:

- Bind local keys to the correct branch member registration or distinct staff identity. Reject cross-branch/tenant links and explicitly resolve unmatched manual enrollment.
- Preserve mapping/version history across re-enrollment/revocation; delayed events or reused identifiers cannot be reassigned to another person.

### SUP-HW03 — Monitor device event processing

As a platform administrator, I want operational failures visible so that I can support reliable attendance.

Acceptance criteria:

- Distinguish disconnected device, unknown identity, and processing failure. R07 stores only first successful daily member access with occurrence time; no retained denied/later raw-attempt history.
- Keep bounded retry/diagnostic data. Terminal authorization is local; receipt of an event does not prove a door opened or retroactively authorize entry.

### SUP-OPS01 — Clean up temporary records cheaply

As a platform administrator, I want bounded scheduled cleanup so that temporary data does not grow indefinitely.

Acceptance criteria:

- Delete OTP challenge on successful registration finalization and expired/abandoned verification records weekly; enforce expiry immediately on verification.
- Delete completed notification metadata older than 30 days monthly; preserve pending work with bounded retries/recovery and prevent old reminders being recreated.
- Use pg_cron if available or a VPS scheduled task, indexed batches, quiet hours, overlap protection, failure visibility, and pruned scheduler history. Preserve business records.

### SUP-TERM02 — Schedule branch reconciliation

As a platform administrator, I want a daily local branch sync setting so that repairs run at suitable times.

Acceptance criteria:

- Set time/enabled status, show last/next run/outcome/failures, prevent overlap, and respect newer prompt changes. Enrollment/activation/renewal/removal remain prompt.
- Apply safe command retries/revisions and reconnect recovery; never overwrite suspension, eviction, or deletion with stale enable work. Local expiry runs independently.

### SUP-PH1-01 — Govern capacity, retention, and enrollment

As a platform administrator, I want one enforceable policy so that member data and branch device capacity stay controlled.

Acceptance criteria:

- Apply R04 conditional enrollment, R08 independent face/fingerprint capacity eviction, and single-branch phase-one distribution. Staff follow separate authorization/lifecycle rules.
- Configure positive biometric retention months (default 3), initially platform-managed; keep profiles separately 12 months after latest membership end and protect renewals.
- Coordinate branch/HWS deletion confirmations, content-free tombstones, obsolete-transfer cancellation, and historical-record preservation. Keep unresolved never-subscribed retention/backup rules explicit.

### SUP-UI01 — Protect sensitive biometric actions

As a platform administrator, I want distinct action permissions so that users can manage credentials safely.

Acceptance criteria:

- Enforce separate register/re-enroll, terminal removal, restore, central deletion, access removal, and retry permissions under R09. Reject unauthorized/cross-branch requests.
- Confirm destructive scope, audit actor/time without biometric content, and show actual per-terminal/type progress; pending commands cannot masquerade as completed removal.

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
| SUP-TERM01 | SUP-HW01 |

### Open decisions and source history

The [shared open decisions](current-rules.md#open-decisions--do-not-treat-these-as-settled-acceptance-criteria) remain unresolved; no acceptance criterion here selects an answer. The complete [pre-consolidation file](history/2026-11-08-before-consolidation/super-admin.md) preserves every dated addition and intermediate correction.
