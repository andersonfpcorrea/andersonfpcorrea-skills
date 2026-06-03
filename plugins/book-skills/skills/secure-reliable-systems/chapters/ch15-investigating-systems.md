# Chapter 15: Investigating Systems

## Core Idea
Effective system investigation requires both good debugging skills (structured hypothesis testing) and properly designed logging infrastructure — because investigation is only possible if you designed for it, and security investigations differ fundamentally from reliability debugging.

## Frameworks Introduced
- **Structured Debugging Process**: (1) Know how the system is supposed to work. (2) Be systematic: collect data → form hypothesis → test hypothesis → revise. The Spanner temporary-files example demonstrates this: 6 nested "why" questions, each requiring a tool or experiment to answer.
- **Horses vs. Zebras**: "When you hear hoofbeats, think horses, not zebras." Start with common explanations before exotic ones. At scale, eliminate common problems over time until you're left with only rare ones — "only the zebras are left."
- **Security vs. Reliability Investigation**: Reliability debugging benefits from broad collaboration and all available information. Security investigations must be restricted to need-to-know — sharing investigation status tips off the adversary. The two require opposite information strategies.

## Key Concepts
- **Immutable logging**: Logs must be append-only and tamper-evident. An attacker who can modify logs can cover their tracks. Design logging as write-once, ideally to a separate, hardened system.
- **Privacy in logs**: Logs must not contain authentication credentials, PII, or other sensitive data that would make the logs themselves an attractive attack target.
- **Log retention budget**: More logs = more investigation capability + more cost. Balance by classifying which security events require long-term retention vs. short-term.
- **Security log categories to retain**: Authentication events, authorization decisions, admin actions, data access patterns, system configuration changes. These provide the minimum forensic foundation.
- **Debugging access security**: Even read-only debugging access creates risk; it must be controlled with the same least-privilege and audit principles as write access. Compromised debug credentials are a common attacker entry path.
- **Collaborative debugging**: Pair debugging is an effective teaching tool and catches bugs faster. Also: when stuck, explaining the problem to someone else often surfaces the solution (rubber duck effect).
- **"When stuck" strategies**: Simplify the problem space (reduce variables), verify your assumptions (the data you think you have may be wrong), seek a second perspective, step away and return with fresh eyes.

## Anti-patterns
- **Logs as the only source of truth**: Logs can be tampered with; corroborate log data with out-of-band sources (metrics, hardware state, network captures) during security investigations.
- **Treating security investigation as a reliability investigation**: Broad communication during a security investigation alerts the adversary. Operational security (OPSEC) requires need-to-know information control.
- **Inadequate log granularity**: Logs that record "user logged in" vs. "user accessed file X with read permission via RPC Y from IP Z at time T" have vastly different forensic value.
- **Logs on the compromised system**: If the attacker has access to the machine, they can modify local logs. Ship logs to a centralized, hardened logging system in real time.

## Key Takeaways
1. Design logging infrastructure before you need it — what you can investigate is limited by what you logged.
2. Logs must be immutable, must exclude credentials/PII, and must be shipped to a hardened external system — logs on a compromised host are useless.
3. Structured debugging (collect data → hypothesize → test) is a learnable skill; practice it systematically rather than relying on intuition.
4. Security investigations require OPSEC (need-to-know); reliability investigations require broad collaboration — choose the right mode based on whether an adversary is involved.
5. Read-only debugging access is still privileged access; control it with the same rigor as write access.

## Connects To
- **Ch 5**: Access control for debugging tools
- **Ch 1**: Security vs. reliability incident information strategy
- **Ch 17**: Crisis management — investigation during an active incident
- **Ch 18**: Recovery — using investigation findings to scope recovery
