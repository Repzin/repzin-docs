# Terminal enrollment, access, and synchronization requirements

Recorded on 2026-11-07, Asia/Colombo.

This document records the agreed product behavior. It is a requirements baseline for backend and HWS work. Device capabilities listed as unverified require confirmation on the actual device and firmware before release.

## Scope and responsibilities

- T01. The backend owns tenant/branch assignments, people, enrollment references, eligibility, and desired access permissions.
- T02. The integration service (HWS) handles terminal connections, vendor protocols, biometric transfer, command execution, and normalized results/events. Backend contracts are vendor independent.
- T03. The first release targets Hikvision DS-K1T320MFWX-B through SDK-free integration. General interfaces allow additional devices later.
- T04. Every entry decision happens locally using users, credentials, and permissions already stored on the terminal. Entry does not require a backend member lookup or approval request. HWS forwards events afterward.

## Terminal onboarding and branch scope

- T05. Authorized provisioning assigns each terminal to one tenant and branch and records its stable identity, supported/enabled methods, capacity, connectivity, and synchronization status. Device identity must be verified before accepting operational events.
- T06. Owners see configured active terminals and whether each is online. Active configuration and current connectivity are separate states.
- T07. All terminals in a branch form one logical control group. A single branch action fans out to its terminals, with results tracked individually. Tenant and branch permissions apply to every action.
- T08. New terminals receive the branch's required enrollment and access state, with progress visible until initial synchronization completes.

## Member and staff biometric enrollment

- T09. Both members and staff employees can enroll biometrics. Person type and identifier mappings distinguish them and prevent collisions between member and staff numbers.
- T10. Select the member's access method during member registration. Face, fingerprint, and enrollment of both must be supported where the terminal capabilities permit them.
- T11. Initial capture occurs physically at one branch terminal. Arbitrary portal photo upload is excluded from this registration flow.
- T12. Authorized staff select a person and terminal and request enrollment remotely when supported. Manual device enrollment is supported as a fallback.
- T13. Link the enrollment to the platform person using their registration identifier and a terminal-local user mapping. Unmatched manual enrollments require authorized resolution before access activation.
- T14. HWS retrieves the captured face data and/or fingerprint template, sends the available data to the backend, and reports success separately per credential. Preserve source terminal and credential version.
- T15. Synchronize the captured enrollment to the other compatible terminals in that branch. The person physically enrolls once. Backend-mediated transfer of terminal-captured data is permitted.
- T16. Track enrollment and synchronization separately for face and fingerprint. Incomplete enrollment remains visible. Never claim successful fingerprint synchronization based only on a successful face transfer.
- T17. Validate a person's selected method against each target terminal's supported/enabled methods. Show incompatible terminals and unresolved branch coverage rather than claiming all terminals are ready.

## Local access lifecycle

- T18. Biometric enrollment is separate from entry authorization. Capturing a face or fingerprint alone does not grant access.
- T19. Member access requires approved branch registration, any required one-time tenant registration fee paid in full, and a current fully paid subscription covering that branch, subject to explicit suspension and active person/tenant status. There is no grace period.
- T20. On payment/activation, send permitted start and expiry times to relevant terminals. Future subscriptions grant access from their start time. Terminals enforce expiry locally while offline.
- T21. Renewals update validity. Transfer biometric data again only if missing or changed.
- T22. Authorized staff can remove access on demand. Explicit suspension remains separate from payment eligibility, so routine reconciliation cannot inadvertently restore suspended access.
- T23. Staff access is authorized separately from member subscriptions. The exact staff grant/validity policy remains to be defined.
- T24. A disconnected terminal cannot apply a new activation, renewal, or revocation until communication resumes. The portal shows pending status until confirmation; existing locally applied expiry still governs entry.
- T25. Record desired state and confirmed state per person/terminal. Use durable work, command IDs, safe duplicate handling, acknowledgements, and revisions so an older enable command cannot override a newer removal.

## Realtime operations

- T26. Promptly dispatch enrollment initiation/results, branch enrollment synchronization, payment activation, renewal validity changes, and on-demand access removal.
- T27. Process terminal work through a durable queue with bounded concurrency, per-terminal serialization, retry backoff, and visible failures. Prioritize access removal. Portal requests can return pending while HWS completes execution.
- T28. Initial provisioning can involve many people. Start it when the terminal is added, throttle transfers, and show progress.

## Daily batch operations

- T29. Platform administrators set and enable/disable a daily batch synchronization time per branch, interpreted in that branch's time zone.
- T30. The daily job compares desired enrollments and permissions with reported terminal state, repairs differences, retries unresolved work, and identifies capacity and connectivity issues.
- T31. Show last/next run, progress, outcome, and per-terminal failures. Prevent overlapping runs for the same branch and avoid conflicts with realtime work.
- T32. Daily reconciliation supplements prompt actions. Local expiry never waits for the batch.
- T33. At 80% face capacity, evict member faces for members absent more than 30 days, preserving central data for 60 days. Refer to the phase-one policy for retention anchor, eviction scope, and restoration decisions. Account for staff capacity while excluding staff from this member eviction rule.

## Attendance events

- T34. HWS sends tenant/branch-bound terminal identity, local person identifier, stable event identifier, occurrence time, and event outcome/authentication method where available.
- T35. Retain only the first member access attempt per branch-local day. Ignore later attempts without event persistence; handle concurrent and delayed events so the earliest occurrence remains. Store outcome where available; denied-attempt treatment for attendance and inactivity remains to be confirmed. Staff attendance semantics remain open.
- T36. Later receipt of an event does not become a backend authorization decision for an entry that already occurred locally.

## Decisions and capability verification

- Confirm SDK-free remote enrollment initiation and export/import of face data and fingerprint templates on the exact firmware. Cross-device template compatibility must be tested.
- Confirm recognition behavior during face/fingerprint transfer, validity changes, bulk synchronization, and local enrollment. Concurrent uninterrupted recognition is currently unverified.
- Define whether a member selecting both means either biometric is accepted or both are required, subject to firmware support.
- Define whether access removal targets one branch, all eligible branches, or selectable scope.
- Define staff access grants, remaining retention/eviction details, missed scheduled runs, and denied-attempt/staff attendance semantics.
- Confirm how an unregistered person enrolled manually is safely linked to the intended platform person.

## Acceptance validation

Test terminal-captured face and fingerprint enrollment for both member and staff; one-terminal capture followed by branch distribution; incompatible devices; new-terminal initial sync; paid activation and future start; offline local expiry; renewal; revocation while online/offline; stale command rejection; duplicate events; reconnect recovery; configured branch batch time and overlapping runs; capacity exhaustion; recognition during each type of update.

## References

- [Hikvision DS-K1T320MFWX-B datasheet](https://www.hikvision.com/content/dam/hikvision/products/S000000001/S000000080/S000000103/S000000107/OFR000141/M000120620/Data_Sheet/DS-K1T320MFWX-B_Value-Series-Face-Access-Terminal_Datasheet_20240319.pdf)
- [DS-K1T320 series manual V2.1](https://assets.hikvision.com/prd/public/all/doc/m000059157/UD28901B-C_Baseline_DS-K1T320-Series-Face-Recognition-Terminal_User-Manual_V2.1_20231110.PDF)
- [Existing HWS interface discussion](../repzilla-hws-interface/interface.md). Its endpoint list remains preliminary; the requirements above define the agreed operational flow.

## Phase-one scope clarification — 2026-11-07 (Asia/Colombo)

The [phase-one policy](phase-one-membership-access-and-retention.md) supersedes conflicting earlier examples. Enrollment synchronization and purchased member access cover one branch in this phase. At 80% terminal face capacity, remove member faces for members absent more than 30 days; preserve central data for 60 days, with its start point still to confirm. Do not re-enroll evicted members automatically during reconciliation.

Platform accounts require tenant and branch RBAC. Retain the first member access attempt per branch-local day and ignore later attempts without persistence. Owners can create priced fixed-day and multi-month packages; multi-branch packages are deferred. Default expiry reminders to one day before subscription end, with owner-configurable lead time. Denied-attempt handling, eviction scope, fingerprint retention, and the 60-day anchor are explicitly open in the linked policy.

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
