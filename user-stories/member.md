# Gym Member User Stories

## Previous version — historical baseline

Earliest available stakeholder section in the pre-refactor working file. This role had no story file in the initial documentation commit. The wording below is preserved as history.

**Status: historical.** Read the consolidated section below for current requirements.

## Added — 2026-10-29, 18:54 (Asia/Colombo, UTC+05:30)

Members are stakeholders even while staff operate the portal on their behalf.
The current scope includes registration through staff, membership services,
reminders, and biometric attendance terminals. Stories labeled Future portal
require a later member-facing application.

### MEM-01 — Join a gym through staff

As a prospective member, I want staff to register my details and explain my
registration status so that I know when I can start attending.

Acceptance criteria:

- Staff can confirm my member number, registered branch, and whether approval is
  pending or complete.
- Registering at another branch of the same business preserves my existing
  member profile and history.
- A shared family phone number does not automatically combine my record with
  another person's.

### MEM-02 — Understand my membership and balance

As a member, I want clear membership dates, branch coverage, and payment
information so that I know what I have purchased and what I still owe.

Acceptance criteria:

- Staff can provide my start date, final usable date, and eligible branches.
- My receipt identifies the amount paid and its reference, and any remaining
  balance is clear.
- Renewal preserves my previous membership and payment history.
- These details can be provided through staff without requiring a member portal
  login.

### MEM-03 — Receive relevant reminders

As a member, I want payment and renewal reminders through my agreed contact
channel so that I can act before missing a payment or expiry date.

Acceptance criteria:

- My reminder preference is recorded and can be updated through staff.
- Messages clearly identify the gym business and relevant due date or membership
  end date.
- I do not receive obsolete reminders after payment or renewal has been
  confirmed.
- A retry does not intentionally send the same milestone reminder repeatedly.

### MEM-04 — Enroll for biometric attendance

As a member, I want staff to enroll me on the gym's attendance terminal so that
the device recognizes me when I arrive.

Acceptance criteria:

- Enrollment uses fingerprint, face, or both according to the actual terminal
  capability and the gym's chosen policy.
- Staff verify that enrollment is associated with my correct branch registration
  before confirming success.
- I am told whether enrollment succeeded, failed, or still needs another step.
- The product explains the biometric handling and records any consent required
  by the agreed deployment policy before collection.
- Repzin's business database stores the device mapping and necessary enrollment
  status/reference; biometric images or templates are not copied into ordinary
  member records.

### MEM-05 — Have a recognized visit recorded accurately

As a member, I want a successful terminal check-in to record my visit so that my
attendance history is accurate.

Acceptance criteria:

- The event identifies the correct member, branch, terminal, and actual
  occurrence time.
- A genuine second visit can be recorded separately; retransmitting the same
  device event does not duplicate it.
- If recognition or connectivity fails, staff can explain the issue and use
  authorized manual attendance.
- An entry refusal or failed match is distinguishable from a successful visit.
- Policy update (2026-11-03): I can enter only with approved registration, valid
  branch coverage, and a fully paid subscription. Partial payment does not grant
  access, expiry stops access immediately, and manual attendance offers no
  grace-period bypass.
- Door opening is included only where connected access-control hardware supports
  it and its policy is agreed.

    ## Additional requirements — 2026-11-04 (Asia/Colombo)

### MEM-06 — Provide contact details for reminders

As a member, I want to provide my email address and verify my mobile number
during registration so that the gym can send me reminders using the channels my
gym business has enabled.

Acceptance criteria:

- Staff collect my email address during registration. I verify my mobile number
  with an OTP before registration can be completed.
- The same tenant reminder settings apply across its branches. Email reminders
  become available when email delivery is enabled.
- When the tenant selects both channels and both are active, each channel is
  delivered and retried independently.

### MEM-07 — Understand what my payment covers

As a member, I want my receipt to show which invoice I paid so that I know what
I still owe.

Acceptance criteria:

- The receipt shows the amount received and the invoice, including its
  registration fee and subscription lines.
- Each invoice is paid in one full payment; there are no installments.
- Staff explain that entry requires a paid subscription invoice, valid
  membership dates, and a paid registration fee if my gym charges one, with no
  grace period.

### MEM-F01 — Future portal: view my own membership information

As a member, I want to sign in and view my membership, attendance, and payments
so that I can check my own records without contacting staff.

Future acceptance criteria:

- My identity is verified before linking a login to an existing member record.
- I can view only my own authorized records, including membership dates, branch
  coverage, visits, receipts, and balance.
- An account shared across unrelated gym businesses does not expose their member
  data to each other.
- Member sign-in never grants staff or owner permissions.

### MEM-F02 — Future portal: submit a registration request

As a prospective member, I want to submit my registration details online so that
staff can review my application before I visit.

Future acceptance criteria:

- The form identifies the intended business and branch.
- Submission creates a pending request and does not immediately grant gym or
  terminal access.
- Staff verify possible duplicates before linking the request to an existing
  member.
- I receive a clear outcome when the request is approved or rejected.

---

## Consolidated additions and changes — 2026-11-08 (Asia/Colombo)

All later additions are combined here by topic. This section supersedes conflicting statements above and in the archived dated updates. Acceptance criteria describe intended behavior, not completed implementation.

Read the [current shared rules](current-rules.md) with these stakeholder stories. They define the detailed eligibility, scope, retention, and synchronization rules referenced below.


### MEM-01 — Join through staff

As a prospective member, I want staff to explain registration so that I know what is needed before attending.

Acceptance criteria:

- Receive a readable member number and branch approval status. Shared family contact details do not merge my profile with another person.
- Provide name, email, mobile, and requested emergency contact details; verify phone with a valid OTP. Resends/attempts are limited; changing my number requires verification again.

### MEM-02 — Understand membership and payment

As a member, I want clear dates and charges so that I know what I bought and whether I can enter.

Acceptance criteria:

- Staff explain one-branch coverage, start/end, package terms, and any one-time registration fee. No portal login is required.
- Receipts show the paid invoice and charge lines. Each invoice needs one full payment; no installments. Renewals preserve history; no grace period for unpaid/expired coverage.

### MEM-03 — Receive relevant reminders

As a member, I want useful expiry reminders so that I can renew in time.

Acceptance criteria:

- Use the tenant's WhatsApp/email/both choice, not an individual preference; email only once activated. Default is one day before end, with owner-configured lead time.
- Identify the business and membership date. Cancel obsolete reminders after payment/renewal; independent channel retries avoid resending successful deliveries.

### MEM-04 — Enroll physically once

As a member, I want my biometrics captured correctly so that the branch terminals recognize me.

Acceptance criteria:

- Choose face/fingerprint/both at registration where supported. Capture at one branch terminal, then sync compatible terminals there; no arbitrary portal photo upload or cross-branch distribution.
- Approved registration plus paid registration fee permits capture when fee-enabled; without a fee, current paid branch membership is required. Capture alone never enables entry.
- Explain data handling/required consent and separate face/fingerprint progress. Entry requires current fully paid branch coverage, any fee paid, approval, and no suspension.

### MEM-05 — Have daily attendance recorded accurately

As a member, I want successful entry recorded on the right day so that attendance reflects my activity.

Acceptance criteria:

- Only the first successful access that branch-local day counts. Denied attempts do not count; repeated daily access creates no extra attendance. Delayed delivery retains earliest actual success.
- The terminal decides locally from stored credentials/validity. Staff can explain failed/offline updates and use only an eligible manual attendance fallback.

### MEM-PH1-01 — Return after inactivity

As a member, I want staff to explain missing credentials so that I know whether I need restoration or fresh capture.

Acceptance criteria:

- At capacity thresholds, more than 30 days of absence can cause branch-wide terminal removal for the affected biometric type; retained backend data may be restored with authorization.
- Biometrics default to deletion 3 configurable months after latest membership end; profile retention is separately 12 months. Deleted credentials require fresh physical capture under enrollment eligibility rules.
- Staff can use permitted restore/re-enroll/delete actions. Removal of credentials and suspension of entry are different; readiness needs terminal confirmation.

### MEM-F01 — Future portal: view my own information

As a member, I want a later portal so that I can review my membership without contacting staff.

Acceptance criteria:

- Future authenticated linking must expose only my records, not owner/staff functions. This portal is outside phase one.

### MEM-F02 — Future portal: submit registration

As a prospective member, I want to submit details online in a later release so that staff can review them.

Acceptance criteria:

- Future requests identify tenant/branch, check duplicates, and remain pending until approval. Submission does not grant terminal access. This self-registration is outside phase one.

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
| MEM-06 | MEM-01 and MEM-03 |
| MEM-07 | MEM-02 |
| MEM-08 | MEM-01 |
| MEM-TERM01 | MEM-04 |

### Open decisions and source history

The [shared open decisions](current-rules.md#open-decisions--do-not-treat-these-as-settled-acceptance-criteria) remain unresolved; no acceptance criterion here selects an answer. The complete [pre-consolidation file](history/2026-11-08-before-consolidation/member.md) preserves every dated addition and intermediate correction.
