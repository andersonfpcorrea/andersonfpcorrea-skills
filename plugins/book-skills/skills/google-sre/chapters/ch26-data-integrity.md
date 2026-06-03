# Chapter 26: Data Integrity: What You Read Is What You Wrote

## Core Idea
Data integrity is stricter than availability: 99.99% availability allows 1 hour downtime/year, but 99.99% byte integrity for a 2GB file = 200KB garbled = catastrophic. The secret to superior data integrity is proactive detection + rapid repair, not merely redundancy.

## Frameworks Introduced
- **Data integrity definition**: Services in the cloud remain accessible to users with their data in the shape expected. Distinct from uptime — data corruption without downtime is still a data integrity failure.
- **Proactive detection + rapid repair formula**:
  - Detect corruption before users are affected
  - Remove/quarantine the corrupt artifact
  - Fix and restore within ~30 minutes
  - Result: Object is ~99.99% available for its accessible lifetime even with one corruption/year
- **Defense in depth for data integrity** (layered):
  1. Soft deletion with configurable retention (recover from accidental deletion)
  2. Backups with point-in-time recovery
  3. Replication across zones/datacenters
  4. Regular integrity checks (checksums, read-back verification)
  5. Audit logging for all data modifications

## Key Concepts
- **"Too long" threshold**: 24 hours is Google's starting point for data unavailability. The 2011 Gmail incident (4 days to recover some mail) revealed this implicitly.
- **User perception**: Users don't distinguish between data loss, data corruption, and extended unavailability. All three erode trust equally.
- **Five optimization axes**: Services optimize some combination of Uptime, Latency, Scale, Velocity, Privacy. Data integrity imposes orthogonal constraints on all five.
- **ACID vs. BASE for data integrity**:
  - ACID: strong consistency; corruption is atomically prevented.
  - BASE: eventual consistency; application must handle inconsistency windows.
- **Soft deletion**: Don't hard-delete data immediately. Keep in a recoverable state for N days. Enables recovery from accidental deletion, bugs, and admin errors.
- **Backups vs. replication**: Replication protects against hardware failure; it does NOT protect against logical corruption (a bug that corrupts data replicates the corruption). Both are required.
- **Privacy-as-data-integrity**: Deleted data must actually be deleted. "Data integrity" includes the negative case — ensuring data is gone when it should be.
- **24-hour recovery SLO for critical data**: Establish this explicitly. Discovering the threshold after a data loss incident is too late.

## Reference Tables
| Strategy | Protects Against | Does NOT Protect Against |
|---|---|---|
| Replication | Hardware failure, machine loss | Logical corruption, accidental deletion |
| Backup + PITR | Logical corruption, accidental deletion | Extended unavailability during restore |
| Soft deletion | Accidental deletion, bugs | Data never fully deleted (privacy risk) |
| Checksums | Silent bit corruption | Application-level logical corruption |
| Audit logs | Unauthorized changes | Detection is retrospective, not preventive |

## Mental Models
- "The secret to superior data integrity is proactive detection and rapid repair and recovery." Redundancy alone doesn't prevent the corruption from persisting.
- An object detected-and-repaired within 30 minutes is 99.99% available for the year — detection speed is the key variable.
- User confusion from "data appears lost" (even if it isn't) = user trust erosion = same as actual data loss.

## Anti-patterns
- **Relying on replication as backup**: Replication propagates logical corruption. You need independent backups.
- **Implicit data integrity requirements**: "The worst time to disagree with users about requirements is after the demise of their data." Define SLOs before incidents.
- **No soft deletion**: Hard-deleting immediately prevents recovery from all classes of accidental/buggy deletion.
- **No checksums**: Silent bit corruption on disk or in transit is invisible without checksums.

## Key Takeaways
1. Data integrity SLOs are stricter than availability SLOs — 99.99% byte integrity for a 2GB file means catastrophic corruption.
2. Proactive detection + rapid repair (< 30 min) enables 99.99% accessible lifetime even with one corruption/year.
3. Backups ≠ replication. Replication propagates corruption; backups provide point-in-time recovery.
4. Soft deletion with configurable retention is the first line of defense against accidental data loss.
5. Set explicit data recovery SLOs before an incident — 24 hours is a reasonable starting point for critical user data.

## Connects To
- **Ch 3**: Embracing risk — error budgets apply to data integrity SLOs too.
- **Ch 23**: Distributed consensus — consistent writes are the foundation of data integrity.
- **Ch 25**: Data pipelines — pipeline failures can cause data loss or duplication.
