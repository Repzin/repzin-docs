# User stories

Consolidated on 2026-11-08 (Asia/Colombo).

Each stakeholder file keeps its previous version at the top for comparison. The
**Consolidated additions and changes** section below it is the current version:
later additions are grouped by topic, contradictory intermediate rules are
replaced, and old story IDs are mapped to their merged story.

Start with [current shared rules](current-rules.md), then the relevant
stakeholder:

- [Owner / tenant admin](admin.md)
- [Employee / receptionist](employee.md)
- [Branch manager](branch-manager.md) — employee stories also apply within
  delegated permissions.
- [Member](member.md) — staff-assisted experience; member portal stories are
  future scope.
- [Platform administrator](super-admin.md)

The shared rules avoid repeating the same lengthy policy in five files. They are
part of each current story's acceptance criteria. They explicitly separate
confirmed requirements, future scope, and open decisions.

## How the consolidation was derived

- For the three original roles, the previous version is the initial committed
  file (`c5d0c1f`). This also restores the original employee stories that had
  become buried below later additions.
- Branch manager and member were added later; their earliest available
  stakeholder section is preserved at the top instead of inventing an earlier
  version.
- Later additions were compared by topic and explicit corrections, rather than
  timestamp alone: some files contain out-of-order dates. The final conditional
  enrollment rule, retention correction, and confirmed attendance/eviction
  decisions take precedence over their intermediate proposals.
- Supersession tables explain the changed rules, and ID maps preserve links from
  old discussions. This is a requirements edit; it does not change the schema,
  runtime implementation, or historical requirement documents.

Future agreed changes should edit the consolidated story and shared rule
together. Add a brief revision note instead of appending another competing full
policy block.
