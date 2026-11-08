# Current shared rules for user stories

Consolidated on 2026-11-08 (Asia/Colombo) from the dated additions in all five
stakeholder files. These are intended requirements, not a claim of
implementation. This document and each file's consolidated section replace
conflicting historical wording. No new product decisions are introduced here.
Unresolved choices remain explicitly open.

## R01 — Business, branch, and release scope

A tenant is one customer business. A gym is a branch within that tenant. Owners
can manage several branches and see authorized combined reports. In phase one,
each purchased membership and its terminal access cover one branch only.
Biometric distribution stays inside that branch. Managing several branches does
not give a member multi-branch access.

The first release provides owner/staff operations and hardware integration.
Member portals, member self-registration, couple/family packages, individual
custom employee permissions, multi-branch packages/access, automated
low-attendance messages, and automated platform-subscription billing are future
work. Email delivery is prepared for later activation; WhatsApp is the initial
reminder channel.

## R02 — Registration, contact verification, and authorization

- Staff register members and authorized owners/managers approve branch
  registration. Show pending, approved, rejected, suspended, or closed status.
  Record the approving person/time and reasons for rejection or suspension.
- Collect name, mobile number, and email address; verify the phone with an OTP
  before completing registration. Keep emergency contact fields from the
  original registration flow. Assign a readable member number unique within the
  tenant. Search before creating duplicates; a shared family phone does not
  prove that two records are the same person.
- Changing a phone number requires verification again. Limit OTP requests,
  resends, and wrong attempts. Expired, consumed, or attempt-exhausted codes are
  unusable immediately, regardless of the cleanup schedule.
- At registration finalization, save verified phone/time and delete the OTP
  challenge atomically. If verification precedes finalization, retain only an
  expiring consumed registration proof, not a reusable OTP. Delete expired
  challenges and abandoned proofs weekly. Never expose codes in activity history
  or logs.
- Platform login identifies a user; server-side authorization checks active
  account, tenant membership, role/action permission, and branch scope on every
  operation. Hidden UI controls alone are insufficient. Owner, manager, and
  receptionist policies are separate from global platform administration. Never
  leave a tenant without an active owner.
- Staff access to the portal, staff/member door access, and terminal
  administrator privileges are three different grants. A free identity provider
  may be evaluated; none is selected by these stories.

## R03 — Packages, invoices, payments, and entry eligibility

- Owners set package prices and positive durations in fixed days or calendar
  months, including several months. A purchase keeps its agreed price, currency,
  duration, coverage dates, and branch even if the package changes later.
  Unpublishing a plan preserves sold memberships and history.
- Approval is required before confirming membership. A renewal creates a new
  period; overlapping confirmed periods for the same member are rejected in
  phase one.
- A tenant may charge one registration fee in its currency or no fee. Snapshot
  the amount at registration approval. Charge once per member per tenant; a
  later branch association does not charge it again. Changes apply to new
  registrations only.
- Invoices contain independent charge lines, such as registration fee and
  subscription. The fee may be invoiced separately or with the first
  subscription. Approval can precede payment, but a required registration fee
  must be paid before member entry or biometric enrollment.
- One full payment settles one invoice. Its amount equals the invoice total. No
  installments, partial payments, or payment split across invoices. A member can
  have several invoices and pay each separately. Keep readable receipt
  references and collector, collection branch, method, timestamp, and currency.
  Retried submissions cannot duplicate money received. Preserve the original
  payment-confirmation requirement: show/send payment date, subscription end
  date, amount, transaction reference, and member/membership reference through
  available configured delivery channels; transaction IDs need not be
  human-readable.
- Show unpaid, not-yet-due, overdue, paid, and voided invoices clearly. Do not
  show a supported partly-paid workflow. Issued charges and completed
  collections cannot be silently rewritten. Corrections/refunds need
  authorization, reason, actor, and time. Refunds cannot exceed the original
  refundable amount; cancellation alone does not mean a refund occurred. Reports
  separate sales, outstanding debt, and money collected net of completed
  refunds.
- Member entry always requires approved branch registration, a paid registration
  fee when applicable, current fully paid subscription coverage at that branch,
  and no applicable person/tenant block or explicit access suspension. There is
  no grace period. Manual attendance cannot bypass these rules. A future
  subscription becomes usable only at its start; access ends at expiry.

## R04 — Biometric enrollment is separate from permission to enter

Select the member's method during registration: face, fingerprint, or both.
Select supported/enabled device methods when onboarding the terminal. Validate
compatibility before capture and distribution.

| Tenant registration fee | Additional enrollment requirement after branch approval                                 |
| ----------------------- | --------------------------------------------------------------------------------------- |
| Fee enabled             | Registration fee paid in full. A subscription is not needed for capture.                |
| No fee                  | Current, fully paid membership at that branch. Free registration alone is insufficient. |

Account/suspension checks and branch permissions apply in both cases. Apply this
eligibility to remote capture, accepting manual enrollment, distribution,
restoration, and fresh enrollment after deletion. Recheck queued work before
dispatch. For no-fee tenants, absent, unpaid, expired, or future membership
cannot satisfy the current-membership condition.

Enrollment never grants entry by itself. A fee-paid member may have synchronized
credentials but disabled access until R03 entry conditions pass. Staff enroll
through a separate authorized employment/access flow and do not buy a member
subscription.

- Capture physically at one compatible branch terminal. No arbitrary portal
  photo upload. Staff can press Register to request capture when the firmware
  supports it, or guide manual device enrollment.
- HWS returns captured face data and/or fingerprint templates, source/version,
  and local user mapping. Link to the correct platform member registration or
  staff identity. Resolve unmatched manual records explicitly; never guess an
  identity or activate an unmanaged record.
- Sync captured credentials to other compatible terminals in the same branch. Do
  not require capture at every terminal. Store biometric content separately from
  ordinary member profile fields and logs; keep references/status on the
  profile.
- Track face and fingerprint outcomes separately, including partial success and
  incompatible devices. Selecting both does not settle whether entry requires
  either or both; that matching policy still needs agreement and device
  validation.
- Explain biometric handling and record consent where the agreed deployment
  policy requires it. Use available device operator controls to restrict
  unauthorized local enrollment; independent device administrators can still
  change local devices outside the platform workflow.

## R05 — Terminals, gateways, and local entry

- The backend owns identity, branch assignments, eligibility, and desired
  access. HWS (Hardware Integration Service) manages connections, vendor
  protocols, data transfer, commands, acknowledgements, and normalized events
  through generalized interfaces.
- First-release target: Hikvision DS-K1T320MFWX-B without an SDK. SDK-free
  capture initiation, biometric export/import, cross-device fingerprint
  compatibility, and recognition during updates remain unverified until tested
  on supported firmware.
- Each terminal belongs to one tenant/branch. Show serial/stable identity,
  supported/enabled methods, capacities, configured active status, online
  status, and last contact. Active and online are different states.
- A branch's terminals form one logical control group. One branch action fans
  out, with confirmation tracked per terminal. Where a gateway is deployed, it
  belongs to one tenant/branch and can connect several terminals. The original
  ESP32 reference does not make ESP32 mandatory for every device connection.
- Authenticate integration/device identity; untrusted identifiers or headers
  alone do not establish tenant/branch authority. Support authorized
  disabling/credential rotation and audit.
- Entry is decided entirely on the terminal using its local users, credentials,
  validity, and permissions. It does not call the backend for each recognition.
  HWS forwards the result afterward; recording an event is not retroactive
  authorization.
- Activation sends start and expiry times. Local expiry applies even offline.
  Renewals normally change validity only; resend biometrics only if missing or
  changed. Explicit Remove access remains effective until authorized restoration
  of access, regardless of later reconciliation.
- Offline devices cannot apply new activation, renewal, deletion, or revocation
  until reconnection. Show pending rather than claiming success; already-applied
  validity still governs entry. Do not promise instant offline revocation.
- Keep tenant/branch/terminal/local-person mappings and their history. Separate
  staff and member identity namespaces. Reused local identifiers and delayed
  events must not attribute a previous person's activity to a new person.

## R06 — Prompt work and daily reconciliation

Dispatch capture, new enrollment distribution, payment activation, renewal, and
access removal promptly through durable background work. Portal requests can
return pending; there is no requirement to hold an HTTP request open until
terminals finish. Do not defer these changes to a daily batch.

Use bounded concurrency, per-terminal serialization, retries with backoff,
command IDs/revisions, acknowledgements, and reconnect recovery. Prioritize
access removal. Older enable commands cannot override newer suspension/deletion.
Initial provisioning may be large: throttle it and show
per-person/per-credential progress.

Platform administrators set a daily sync time and enabled state per branch in
its local time zone. The batch compares expected and reported
credentials/permissions, repairs differences, and retries unresolved work. Show
last/next run, outcome, and per-terminal failures. Prevent overlapping runs and
preserve newer prompt changes. Daily reconciliation must respect explicit
suspension, capacity eviction, and deletion state. Local expiry does not depend
on any batch.

## R07 — Attendance and insights

- Store only the first successful member access per member, branch, and
  branch-local calendar day. Denied attempts never count or reset inactivity.
  They cannot prevent a later successful access that day from being recorded.
- Ignore later same-day attempts without extra attendance or persisted raw-event
  rows. Temporary delivery/retry state must be bounded; do not retain ignored
  payloads in routine logs or indefinite queues.
- Use the event's occurrence time, not receipt time, to derive its branch-local
  day. Transactional daily uniqueness handles concurrent terminals/retries. If
  an earlier successful event arrives late, keep the earliest successful
  occurrence.
- Events include authenticated tenant/branch-bound terminal identity, local
  person key, stable event ID, occurrence time, and outcome/method where
  available. Report unknown mappings, disconnected devices, and processing
  failures without treating every diagnostic as a stored attendance attempt.
- Permitted manual check-in follows the same eligibility and daily uniqueness.
  Record its source/operator. Corrections require permission/reason and
  traceability; excluded/corrected records cannot inflate totals.
- Attendance defaults to today and supports member, branch, and date filters.
  Show distinct attendance days, last visit, never-attended members, low
  attendance, and changes over a stated comparison window. Do not label repeated
  daily access as extra visits. Unique-member all-branch summaries deduplicate
  people.
- Low-attendance reports distinguish new members, never-attended members, and
  members who stopped attending; show counts/window rather than claiming
  predicted cancellation. Owners/managers follow up personally in phase one.
- Show ending, renewed, lapsed, continuously renewed, and returning-after-gap
  memberships. Lapse begins immediately at coverage end without a next period;
  an unpaid renewal still grants no entry. Reports are traceable to permitted
  source records.

## R08 — Capacity eviction and separate retention periods

- Evaluate face and fingerprint occupancy independently at each terminal. At
  least 80% capacity triggers cleanup candidates for that credential type: 400
  of 500 faces qualifies; 399 does not. Count fingerprint templates, potentially
  several per person, rather than just people. Staff consume capacity but are
  excluded from member inactivity eviction.
- Candidates are members absent for more than 30 days based on successful branch
  attendance. Exactly 30 days does not qualify. Recheck recent attendance and
  newer changes before removal. Do not evict active attendees merely to meet a
  target; show unresolved capacity when no eligible candidate exists.
- Eviction is branch-wide for the triggered credential type. Remove it from
  every branch terminal, retain central data until its separate retention
  deadline, and track confirmation/offline pending status. Maintain
  residency/eviction state separately from membership permission so normal sync
  does not refill removed slots.
- Authorized restoration checks retained data, eligibility, compatible methods,
  and capacity. Local recognition becomes possible only after synchronization
  completes. A removed credential cannot be recognized through a backend lookup.
- Retain member profile/registration data for 12 calendar months after the
  latest membership end date. A renewal updates that reference; current/future
  memberships protect the member from inactive deletion.
- Retain access face images, face templates, and fingerprint templates for
  configurable calendar months after that same latest end date: default
  `biometric_retention_months = 3`, positive values only. Platform-managed scope
  is the initial interpretation; tenant overrides are not agreed. This replaces
  60-day retention.
- At biometric expiry, delete centrally and request removal of terminal and
  managed HWS staging/cache copies. Track each device; offline removal remains
  pending. Keep content-free deletion generations/tombstones, cancel old
  transfers, and expire temporary access so stale callbacks/retries cannot
  recreate deleted credentials. Recheck renewal/configuration/fresh enrollment
  before deletion.
- A returning member uses retained credentials only through authorized
  restoration. After central deletion, fresh physical capture is required; reuse
  the member profile while its 12-month retention permits. Keep
  financial/audit/attendance history via an agreed anonymization/reference
  strategy rather than cascading deletion.
- Staff biometrics follow a separate employment/access lifecycle, not member
  subscription retention.

## R09 — Biometric action buttons

Authorized owners/admins and delegated staff see actions on the person's branch
enrollment view. Disable unavailable actions with a reason; check permission
again on the backend.

| Action                       | Effect                                                                                                       |
| ---------------------------- | ------------------------------------------------------------------------------------------------------------ |
| Register / Re-enroll         | Physical capture at a selected compatible terminal; R04 eligibility applies.                                 |
| Remove from branch terminals | Remove selected credential types across the branch; keep retained backend data and prevent automatic refill. |
| Restore to branch terminals  | Restore retained credentials subject to eligibility/capacity; do not implicitly enable entry.                |
| Delete biometric data        | Delete selected types centrally and from branch/HWS copies; explain that fresh capture is needed afterward.  |
| Remove access                | Suspend entry permission separately from deleting biometrics.                                                |
| Retry failed sync            | Retry authorized failed work without duplicate or stale conflicting operations.                              |

Show face/fingerprint availability and per-terminal pending/success/failure with
reasons. Confirm destructive actions with person, branch, selected types, and
deletion scope. Audit actor/time without biometric content. A submitted command
is not proof of completion.

## R10 — Tenant reminders and bounded delivery history

- The tenant owner selects WhatsApp, email, or both for the whole tenant. No
  member/branch channel override in phase one. Use the OTP-verified phone and
  collected email. Clearly identify email as unavailable until its integration
  is activated; do not promise email delivery beforehand.
- Default membership-ending reminder: one day before the purchased subscription
  end. Owner can change lead time; tenant-wide timing is the initial
  interpretation. Earlier broad before/on/after milestones and automatic absence
  messages are not current delivery requirements. The original separate
  automatic expiry-day notification needs an agreed trigger before activation;
  it is not silently added to the current expiry-reminder schedule.
- Recheck renewal/payment before dispatch; cancel obsolete queued reminders.
  Prevent duplicate milestone sends. Track each enabled channel independently so
  retrying email cannot resend successful WhatsApp.
- Reuse versioned tenant templates by purpose/channel and fill dynamic data at
  send time. Store necessary recipient/channel, template reference/version,
  status, timestamps, provider identifiers, and bounded retry/failure metadata.
  Do not store full rendered messages.
- Show queued, provider-accepted, delivered, failed, or cancelled states only
  when supported by provider evidence; acceptance is not proof of delivery.
- Once monthly, delete completed notification metadata older than 30 days by
  completion time. Actual retained history can be roughly 30–61 days.
  Pending/retrying work is not purged as completed history; bounded
  retries/stale-work recovery prevent indefinite accumulation. Purging delivery
  history must not recreate obsolete reminders.
- Schedule weekly OTP and monthly notification cleanup with `pg_cron` when
  supported, or a VPS scheduled task running equivalent cleanup. Use indexed
  bounded batches, quiet hours, overlap prevention, failure monitoring, and
  bounded scheduler history. Preserve billing and other business records.

## Open decisions — do not treat these as settled acceptance criteria

- Exact role/action matrix, identity provider, staff door-access validity and
  staff attendance/retention policy.
- Supported firmware's remote capture/export/import and recognition while
  syncing; fingerprint compatibility and whether both means either or both at
  entry.
- Inactivity baseline for never-attended members; capacity cleanup target below
  80%; restore prioritization.
- Profile/biometric retention anchor for members who pay registration but never
  buy a subscription; no membership end date exists for them.
- Final retention-setting scope, calendar-month/time-zone boundaries,
  changed-setting effects, backup handling, profile anonymization fields, and
  effective purge schedule.
- Fixed-day/calendar-month expiry boundaries and month-end handling; reminder
  send time/time zone and any per-package timing overrides.
- Missed daily sync scheduling, tenant suspension/device-ingestion policy and
  offline effects, and any future wider-than-branch access-removal scope. No
  cross-branch member operation is introduced in phase one.

## Source and revision notes

Source requirement discussions remain in
[phase-one requirements](../requirements/phase-one-membership-access-and-retention.md)
and
[terminal requirements](../requirements/terminal-enrollment-access-and-sync.md);
those files contain chronological, sometimes superseded wording. Use this
consolidated version for current story acceptance and consult their latest
corrections for context.
