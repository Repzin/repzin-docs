# Phase-one membership, access, and retention requirements

Recorded 2026-11-07, Asia/Colombo. This is the current scope clarification and takes precedence over older conflicting stories and design examples. See also [terminal requirements](terminal-enrollment-access-and-sync.md).

## Branch scope

- P01. A tenant may manage multiple branches, but a phase-one subscription/access entitlement covers one branch.
- P02. Synchronize enrollment across compatible terminals within that branch only. Cross-branch enrollment distribution and multi-branch membership use are future scope.
- P03. Preserve a design extension path for multi-branch packages, but do not offer or activate them in phase one.

## Terminal face capacity and backend retention

- P04. Evaluate face and fingerprint usage independently against each terminal's supported count. When either reaches at least 80%, identify inactive members for branch-wide eviction of that credential type. For a 500-face terminal the trigger is 400 occupied slots, including staff and other users.
- P05. Eligible members have not attended for more than 30 days. Determine inactivity from attendance across the member's branch, rather than visits at one particular terminal.
- P06. Remove the eligible credential type for selected inactive members from every terminal in that branch and retain central biometric data for the agreed 60-day period. Staff faces are excluded from this member inactivity policy. Do not delete member, payment, subscription, or attendance history.
- P07. Record an evicted enrollment state so normal reconciliation and new-terminal synchronization do not immediately reload the face. Read desired membership eligibility separately from whether a credential should currently occupy a slot.
- P08. Before removing a face, recheck eligibility and recent attendance; preserve newer enrollment/access changes. Show capacity failures if no eligible faces can be removed. Do not evict active attendees just to meet a capacity target.
- P09. While data remains centrally retained, authorized restoration can re-enroll a returning eligible member. Local recognition requires restoration to finish before entry; a terminal cannot recognize a removed face through a backend lookup.
- P10. Confirm the details below before implementing irreversible central deletion.

### Retention details still requiring confirmation

- Does 60 days start at the last attendance date, or when the face is removed from the terminal? The requested duration is recorded without choosing an anchor.
- For a member who has never attended, which date starts inactivity?
- Eviction scope is resolved as branch-wide. A face-capacity trigger removes eligible faces across the branch; a fingerprint-capacity trigger removes eligible fingerprint templates across the branch.
- Apply the agreed central 60-day retention duration to evicted face and fingerprint data; its date anchor remains unresolved. Capacity triggers are evaluated independently by credential type.
- After central retention ends, how should remaining terminal copies be handled? Central deletion alone cannot guarantee biometric deletion across devices.
- Confirm whether eviction continues below 80% or to another target, and how restoration is initiated/prioritized.

## Branch-scoped platform authorization

- P11. Provide RBAC for owner/admin and staff platform accounts, with tenant membership and branch-scoped permissions. Global platform administrators remain distinct from tenant administrators.
- P12. Server-side authorization checks role, active account, tenant membership, and access to the target branch. Hiding UI controls alone is insufficient.
- P13. Identity-provider authentication identifies the user. Repzin maintains and enforces tenant/branch business authorization unless an explicitly verified equivalent is selected.
- P14. A free authentication provider may be evaluated for platform accounts. No provider is selected by this requirement; evaluate costs/limits and compatibility before changing the current auth implementation.
- P15. Platform RBAC, terminal entry permission, and device administrator privileges are separate grants. Final role/action matrix and provider remain decisions to close.

## Daily attendance persistence

- P16. Retain only the first successful access per member, branch, and local calendar day. Denied attempts never create attendance and do not prevent a later successful access that day from being recorded. Later same-day attempts, including retries across terminals in that branch, are ignored without persisted raw-event or attendance rows.
- P17. Preserve the original occurrence timestamp; derive the attendance day using the branch time zone, not arrival time at the backend.
- P18. Enforce daily uniqueness transactionally. If a delayed earlier event arrives, retain the earliest occurrence rather than whichever event was received first; later events remain ignored.
- P19. Use successful access events for attendance and inactivity calculations. Denied attempts do not update last attendance or reset the 30-day inactivity period. Ignore subsequent successful accesses that day without additional attendance or raw-event persistence.
- P20. Returning daily presence replaces the older multiple-visits-per-day requirement. Staff attendance behavior is a separate decision.
- P21. Avoid persisting ignored attempts in raw payload archives, queues with indefinite history, or routine logs. Temporary delivery/retry processing must have bounded retention.

## Membership packages

- P22. Owners can create packages with a chosen amount and duration in fixed days or calendar months, including multiple months. Snapshot price and duration on purchase.
- P23. Phase-one packages apply to a single branch. Multi-branch applicability remains an extension point for a later release.
- P24. Validate positive duration and valid monetary amounts; specify month-end handling and exact expiry boundaries before implementation. Examples include 30 days, 45 days, 3 months, and 6 months, each with its own price.

## Expiry reminders

- P25. Default the membership-ending reminder to one day before the purchased subscription period ends.
- P26. The owner can configure the lead time in days. Use tenant-level settings across branches as the initial interpretation, consistent with existing tenant reminder-channel settings.
- P27. Use purchased subscription dates rather than a mutable package definition. Define the scheduled send time and branch time-zone handling explicitly.
- P28. Use tenant-selected WhatsApp/email/both for enabled channels. Recheck renewal and eligibility before dispatch and prevent duplicate reminders.
- P29. The current first-release expiry-reminder rule supersedes earlier broad before/on/after milestone examples. Other reminder purposes require a separately agreed rule.
- P30. Confirm whether reminder lead time needs per-package overrides; none are assumed in the initial tenant-level setting.

## Validation

Verify phase-one rejection of multi-branch purchases/distribution; 399/500 versus 400/500 capacity; exactly 30 versus more than 30 inactive days; staff exclusion; concurrent attendance during eviction; prevention of automatic re-enrollment after eviction; branch daily uniqueness under parallel and delayed events; midnight boundaries; role/branch authorization; face/fingerprint thresholds independently; branch-wide eviction with one offline terminal; denied access followed by successful access on the same day; fixed-day and multi-month pricing; reminders after owner setting changes and renewal cancellation. Test retention boundaries after its anchor is agreed.

## Confirmed attendance and eviction rules — 2026-11-07 (Asia/Colombo)

This clarification supersedes earlier first-attempt attendance wording, affected-terminal-only eviction, and open denied-attempt/eviction-scope questions.

- Attendance is the first successful access per member, branch, and branch-local calendar day. Denied attempts never count as attendance or refresh last attendance. A denied attempt cannot prevent a later successful access that day from being recorded. Ignore later successful accesses without additional persisted attendance/raw-event rows. Concurrency and delayed-event handling retain the earliest successful occurrence.
- Check face and fingerprint occupancy independently against each terminal's supported capacity. At least 80% usage triggers cleanup for the corresponding credential type. Fingerprint capacity counts stored templates, which may include several fingers per person; it is not simply the number of people.
- Select members absent for more than 30 days using successful branch attendance. Exclude staff from this inactivity policy.
- Eviction is branch-wide. Remove selected members' corresponding face or fingerprint credentials from all branch terminals, recording confirmation per terminal. An offline terminal stays pending until it reconnects.
- Preserve an eviction/residency state per credential type so ordinary reconciliation does not reload removed data. Authorized restoration must recheck access eligibility and capacity.
- Central retention remains 60 days for evicted biometric data. Its starting date, never-attended inactivity baseline, cleanup target, and central-deletion treatment of remaining device copies still require agreement.
- Validation includes independent face/fingerprint thresholds, several fingerprints per person, branch-wide partial/offline results, denied attempts followed by successful entry, and inactivity unaffected by denied attempts.

## Retention correction — 2026-11-07 (Asia/Colombo)

This revision supersedes the earlier 60-day biometric retention rule and its unresolved date anchor.

### Member and biometric retention

- Keep registered-member profile/registration data for 12 calendar months from the latest membership end date. A later renewal updates that reference date. A current or future membership prevents inactive-member deletion.
- Retain access face images, derived face templates, and fingerprint templates for a configurable number of calendar months after the latest membership end date. Default biometric_retention_months to 3; require a positive value. This is a platform-managed setting initially; tenant-specific overrides are not assumed.
- Terminal capacity eviction remains separate. When face or fingerprint usage reaches 80%, eligible members absent more than 30 days may have that credential evicted branch-wide before central biometric retention ends. Retained central credentials can be restored by an authorized flow while eligibility and capacity permit.
- At biometric retention expiry, delete the member's access biometric data centrally and request removal of remaining copies across the branch terminals and integration-service staging/caches. Track per-terminal confirmation, with offline removal pending until reconnection.
- Keep a deletion generation/tombstone without biometric content so old retries, delayed enrollment callbacks, and reconciliation cannot restore data after deletion. Cancel obsolete biometric transfers and expire temporary download/upload access.
- After biometric data has been deleted, a returning member must capture the required biometrics physically at a terminal again. Reuse the existing member profile if it remains within its separate retention period.
- A member who returns while biometric data is still retained can use authorized restoration; rejoining does not imply biometrics are available until terminal synchronization is confirmed.
- Recheck the latest membership and retention configuration before deletion, protecting concurrent renewal or fresh enrollment. Schedule a due-deletion pass through the existing branch maintenance flow and record completion.
- At 12-month expiry, apply the member-profile retention process separately from biometric deletion. Preserve required financial, audit, and historical attendance records through a reviewed anonymization/reference-preservation strategy rather than cascading their deletion.
- Staff biometrics remain under a separate employment/access lifecycle; membership-based member retention does not define staff retention.

### Decisions remaining

- Confirm platform-wide versus tenant-configurable scope for biometric_retention_months. Platform scope is the initial interpretation.
- Define retention for registrations that never purchase a membership, since they have no membership end date.
- Define exact profile purge/anonymization fields and handling of backups, historical biometric copies, and offline devices. Logical deletion does not guarantee immediate removal from retained backups.
- Define calendar-month boundary/time-zone handling, configuration-change effect on existing deadlines, and the effective member-data deletion time after eligibility is reached.

### Acceptance criteria

Verify latest-end-date renewal resets, active/future membership protection, default 3-month biometric expiry, changed retention settings, independent 12-month profile retention, capacity eviction before retention expiry, branch-wide deletion and offline pending status, prevention of stale-data restoration, returning members requiring fresh terminal capture after deletion, and preservation of business history.

## Paid membership required before member enrollment — 2026-11-07 (Asia/Colombo)

This clarification supersedes earlier flows that enroll member biometrics during unpaid registration. The member access method can be selected during registration; biometric capture happens only after eligibility is established.

- Member biometric enrollment requires approved registration at the branch, full payment of any applicable registration fee, and a current fully paid membership covering that branch. Suspended members and inactive/blocked accounts cannot enroll.
- Enforce this rule on the backend before requesting remote capture, accepting/linking manually captured enrollment, restoring retained credentials, or distributing biometric data to terminals. Recheck eligibility before dispatching queued enrollment work.
- The staff enrollment UI explains why capture is unavailable and directs authorized staff to complete approval, payment, or membership activation first. Future-dated coverage does not permit enrollment until its start under this initial current-membership interpretation.
- A person manually enrolled outside the platform workflow is an unresolved device record. Do not accept it as a completed platform enrollment or grant access before eligibility is verified; report and resolve the unmanaged record through HWS.
- Supported device-side operator controls must prevent unauthorized manual enrollment where possible. Platform validation alone cannot prevent an independent device administrator from editing a locally managed terminal.
- Staff employee enrollment remains a distinct authorized staff-access flow. Staff do not need to buy a member subscription.
- Returning members whose biometric data was deleted must satisfy the same approval/payment/current-membership checks before fresh capture.
- Registrations without any purchased membership have no accepted platform biometric data. Define their profile-only retention separately because they have no membership end date.

Acceptance criteria

- Reject enrollment for unpaid registration fees, unpaid subscriptions, absent/expired/future membership, unapproved branch registration, suspension, or blocked tenant.
- Permit enrollment once all member eligibility conditions are satisfied, including tenants with no registration fee.
- Recheck queued work after payment void, suspension, or membership changes; obsolete work cannot bypass eligibility.
- Verify manual/unmatched enrollments cannot silently activate access, and authorized staff enrollment remains independent of member billing.

## Registration payment is sufficient for biometric enrollment — 2026-11-07 (Asia/Colombo)

This correction supersedes the preceding requirement for a current paid membership before member biometric enrollment.

- An approved member registration with its registration fee paid in full is sufficient for biometric capture, acceptance of manually captured enrollment, and branch credential synchronization. If the tenant has no registration fee, skip the fee check.
- A subscription purchase, subscription payment, or current subscription period is not required for biometric enrollment. Staff can select the access method during registration and capture biometrics once registration approval and any required fee payment are complete.
- Enrollment does not enable gym entry. Entry separately requires approved registration, any required registration fee paid, current fully paid subscription coverage at the branch, and no applicable suspension/account block.
- Synchronize enrollment without enabling access when subscription eligibility is absent. Recheck entry eligibility before sending an activation command. Reconciliation must preserve that separation and capacity-eviction state.
- Staff biometric enrollment uses the separate staff authorization flow.
- After biometric retention deletion, fresh capture follows this registration-payment enrollment rule; entry still requires subscription eligibility.
- Because members can now enroll before buying a subscription, define the biometric and profile retention baseline for registrations that never acquire membership before enabling automated retention for those records. Do not assume a nonexistent membership end date.

Acceptance criteria

- Paid approved registration permits capture and credential synchronization without a subscription, including unpaid, expired, or future subscription scenarios; entry stays disabled until its independent eligibility rules pass.
- Registration approval with no tenant fee also permits enrollment.
- An unpaid required registration fee blocks enrollment.
- Verify manual enrollment acceptance and queued work enforce the same registration rules and cannot implicitly enable entry.

## Conditional biometric enrollment eligibility — 2026-11-07 (Asia/Colombo)

This correction supersedes the earlier statement that approved free registration alone permits enrollment.

An approved branch registration is required in both cases.

| Tenant registration fee | Additional requirement for member biometric enrollment |
| --- | --- |
| Fee enabled | The member's registration fee is paid in full. A subscription is not required for enrollment. |
| No registration fee | The member has a current, fully paid membership covering that branch. |

Apply this rule to capture initiation, acceptance of manual enrollment, restoration after biometric deletion, and branch biometric distribution. Recheck queued work before dispatch. Platform and branch permissions and applicable suspension/account-block checks still apply.

Gym entry remains a separate decision and always requires current fully paid subscription coverage, approved registration, and the registration fee paid when applicable. Capturing or synchronizing biometrics does not implicitly enable entry. Staff enrollment follows separate staff authorization.

Acceptance criteria

- Fee-enabled tenant: paid registration permits enrollment without membership; unpaid registration fee blocks it.
- No-fee tenant: registration alone does not permit enrollment; current paid branch membership does.
- No-fee tenant: missing, unpaid, expired, or future membership cannot satisfy the current-membership enrollment condition.
- Test remote capture, manual acceptance, queued synchronization, and re-enrollment against the same conditional rule.
- Retention for fee-paid members who never buy membership still needs a defined baseline because no membership end date exists.

## Biometric management UI actions — 2026-11-07 (Asia/Colombo)

Authorized owner/admin and staff users need biometric action buttons on the person's branch enrollment view, subject to branch RBAC. Staff employee records use their separate enrollment eligibility.

- Register biometrics / Re-enroll: capture face and/or fingerprint physically at a selected compatible terminal, with manual device enrollment fallback. Re-enroll is available after central deletion; apply the conditional member enrollment payment rule.
- Remove from branch terminals: remove selected enrolled credential types from all branch terminals while keeping backend data within its retention period. Record removal state so reconciliation does not immediately restore it.
- Restore to branch terminals: available when the required backend biometric data remains. Recheck enrollment eligibility, access suspension, supported methods, and capacity; do not silently activate entry when subscription eligibility is absent.
- Delete biometric data: explicitly confirm removal of the selected biometric types centrally and across branch terminals, including managed integration copies. Explain that fresh physical enrollment will be needed afterward. Keep member profile and financial/attendance history under their separate policies.
- Remove access is a distinct action from credential deletion. Keeping credentials while suspending entry is supported.
- Show face/fingerprint availability separately, terminal-by-terminal pending/success/failure, and failure reasons. A command submission is not proof of deletion or restoration; offline terminals remain pending.
- Offer authorized retry for failed synchronization. Prevent duplicate submissions and conflicting restore/delete operations with command revisions. Disable unavailable actions with an explanatory reason.
- Confirm destructive actions with person, branch, selected credential types, and backend-versus-terminal deletion scope. Audit who initiated them and when without storing biometric content in audit logs.

Stakeholder acceptance

- Owners/admins can manage permitted member and staff credentials and see the effect of each button before confirming.
- Authorized employees can register, remove, restore, or re-enroll only where their role grants the specific action.
- Branch managers can review progress and retry authorized failures within assigned branches.
- Members can ask staff to restore retained credentials or arrange fresh terminal enrollment after deletion; no member portal is introduced.
- Platform administrators define separate permissions for these actions and verify unauthorized or cross-branch requests are rejected by the backend.
