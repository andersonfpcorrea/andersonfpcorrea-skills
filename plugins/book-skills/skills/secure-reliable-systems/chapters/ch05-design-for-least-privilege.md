# Chapter 5: Design for Least Privilege

## Core Idea
Least privilege — granting only the minimum access required for a task — is the primary design principle for limiting blast radius from both malicious actors and human error; it must be designed in from the beginning, not enforced culturally.

## Frameworks Introduced
- **Least Privilege**: Humans, automation, and machines all receive only the minimum access required for a task at a given time. Apply to data, APIs, infrastructure controls, and credentials.
- **Zero Trust Networking (BeyondCorp)**: Network location grants no implicit privilege. Access is based on verified user identity + device credentials. Plugging into a conference room network port ≠ more access than connecting from the internet.
- **Zero Touch Interfaces (ZTP/ZTN)**: Remove direct human access to production roles entirely. Humans have indirect access through audited tooling that makes controlled, predictable changes.
- **Access Classification Framework**: Classify data and APIs by risk (e.g., Public/Sensitive/Highly Sensitive) × access type (Read/Write/Infrastructure), then apply controls proportional to the risk tier.
- **Multi-Party Authorization (MPA)**: A second approver must authorize an action before execution; enforced by the system, not just culture.
- **Three-Factor Authorization (3FA)**: User identity + device credential + a third factor (e.g., hardware token or biometric) for the highest-risk operations.

## Reference Table: Example Access Classifications
| Classification | Read Access | Write Access | Infrastructure Access |
|---|---|---|---|
| Public (open to company) | Low risk | Low risk | High risk |
| Sensitive (limited to groups with business purpose) | Medium/High risk | Medium risk | High risk |
| Highly sensitive (no permanent access) | High risk | High risk | High risk |

*Infrastructure access = ability to bypass normal access controls (SSH, restart services, change ACLs, reduce logging).*

## Key Concepts
- **Small Functional APIs**: "Make each API endpoint do one thing well." Replace broad APIs (POSIX/interactive SSH) with narrow, purpose-built APIs that can be precisely permissioned and audited.
- **Breakglass**: Emergency bypass of authorization systems. Must be rare, logged, audited, and culturally stigmatized. Breaking glass is an emergency, not a workaround.
- **Auditing**: Granular action logs ("pushed config with hash 123DEAD...BEEF456") are only effective when using small functional APIs. Interactive sessions (bash history) can be trivially bypassed.
- **Business justifications**: Require operators to declare a reason for access before it is granted — creates an audit trail and adds friction against casual misuse.
- **Temporary access**: Grant time-limited credentials for specific tasks rather than permanent elevated access.
- **Proxies (safe proxies)**: A central point to enforce MPA, log all operations, and rate-limit dangerous actions without modifying target systems.

## Anti-patterns
- **Interactive SSH sessions as admin interface**: Exposes the entire POSIX API; bash history can be bypassed via vim `:!/bin/evilcmd` or ncurses; wide APIs + wide permissions = unauditable blast radius.
- **Ambient authority / implicit root access**: Engineers should not routinely be able to act as root — even if they generally are trusted.
- **Hope as a strategy**: "Our engineers have the best intentions" is not a security control. Design for the realistic failure modes.
- **Over-broad breakglass use**: Frequent breakglass makes auditing meaningless and normalizes bypassing controls. Culture + peer review must keep breakglass rare.

## Key Takeaways
1. Least privilege applies to humans, automation, services, and machines — design it into every access layer, not just user-facing systems.
2. Replace large APIs (interactive SSH, POSIX) with small functional APIs; precise permissions require precise interfaces.
3. Zero Trust: network location grants nothing; every access decision must verify user + device identity.
4. MPA for sensitive operations converts cultural norms into technically enforced controls.
5. Classify your data and APIs by risk tier before designing access controls — not all access deserves the same overhead.
6. Breakglass mechanisms must exist, but their use must be rare, logged, and peer-reviewed.

## Connects To
- **Ch 3**: Safe proxies as an implementation of MPA and least privilege
- **Ch 8**: Blast radius limiting (location/role separation)
- **Ch 14**: Supply chain — code review as MPA for deployments
- **Ch 21**: Culture of review — auditing culture reinforcing least privilege
