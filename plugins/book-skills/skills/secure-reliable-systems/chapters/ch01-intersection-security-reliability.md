# Chapter 1: The Intersection of Security and Reliability

## Core Idea
Security and reliability are both emergent properties of system design that cannot be bolted on after the fact — they share deep structural commonalities (invisibility, assessment, simplicity, evolution, resilience) but diverge when a malicious adversary is present.

## Frameworks Introduced
- **CIA Triad (Confidentiality, Integrity, Availability)**: The three foundational properties, relevant to both reliability (accidental violations) and security (adversarial violations). The key distinction: reliability concerns itself with nonmalicious violations; security must assume an active adversary.
- **Fail-Safe vs. Fail-Secure**: Reliability designs often fail open (door unlocks on power loss = safe exit); security designs fail closed (door stays locked = prevents exploitation). These two goals directly conflict — redundancy that improves reliability also widens the attack surface.

## Key Concepts
- **Emergent property**: Neither security nor reliability can be achieved by a single module or flag; both arise from the interaction of many design decisions across the whole system.
- **Error budget**: A reliability technique for reasoning about acceptable failure rates (reliability), but security failures are harder to bound because adversary behavior cannot be assumed independent across components.
- **Defense in depth**: Multiple redundant defense layers so no single failure compromises the whole system.
- **Blast radius**: The extent of impact a single failure or compromise can have; should be minimized by compartmentalization.
- **Least privilege**: Users/processes receive only the minimal access needed for the task at hand — mitigates both malicious insider risk and accidental error damage.
- **Multi-party authorization (MPA)**: Requiring a second person to approve sensitive operations; protects against both insider threats and human error.
- **Zero Trust**: No credential or network location is implicitly trusted; access is granted based on verified user + device identity.
- **IMAG (Incident Management at Google)**: Google's formalized crisis response framework, modeled on the US government's Incident Command System.
- **DiRT (Disaster Recovery Testing)**: Regular simulation exercises to keep incident response skills sharp.

## Mental Models
- Treat security and reliability failures as two sides of the same coin: both represent the system not doing what it's supposed to do — the difference is whether there's a human adversary.
- "When designing for reliability, assume some things will go wrong at some point. When designing for security, assume an adversary could be trying to make things go wrong at any point."
- Redundancy that increases reliability always increases attack surface. Design each redundancy path with this tradeoff explicitly in mind.

## Anti-patterns
- **Bolting on security/reliability later**: Both are architectural properties — retrofitting is expensive and less effective than designing for them from day one.
- **Overloading logs with sensitive data**: Comprehensive logs improve reliability investigation but logs become attractive attack targets; balance coverage vs. exposure.
- **Sharing recovery info broadly during security incidents**: Reliability incidents benefit from wide collaboration; security incidents require need-to-know information control to avoid tipping off the adversary.

## Key Takeaways
1. Security and reliability share six structural commonalities: invisibility, assessment, simplicity, evolution, resilience, and response/recovery — address them together.
2. Fail-safe (reliability) and fail-secure (security) are often in direct tension; redundancy paths must be explicitly designed against both threat models.
3. Defense in depth and distinct failure domains address both adversarial exploitation and cascading reliability failures.
4. Least privilege and MPA reduce both malicious insider risk and accidental human error — dual-purpose designs.
5. Crisis response requires opposite information strategies: reliability = involve everyone; security = need-to-know only.

## Connects To
- **Ch 5**: Least privilege and MPA in depth
- **Ch 8**: Resilience and blast radius design
- **Ch 9**: Recovery design principles
- **Ch 16/17**: Disaster planning and crisis management (IMAG)
