# Chapter 10: Mitigating Denial-of-Service Attacks

## Core Idea
DoS defense is an economics problem: the attacker is trying to make demand exceed your capacity at minimal cost; your goal is to force the attacker to expend more resources than you do to absorb the attack, by shedding attack traffic as early in the stack as possible.

## Frameworks Introduced
- **Attacker Economics**: DoS is a capacity-exhaustion attack. Defenders win by: (a) over-provisioning capacity (expensive), (b) absorbing attacks at shared infrastructure (economy of scale), or (c) mitigating before traffic reaches expensive processing layers.
- **Layered Defenses for DoS**:
  1. **Edge routers**: Throttle high-bandwidth attacks; drop suspicious traffic before it enters the backbone.
  2. **Network load balancers**: Throttle packet-flooding attacks, protect application load balancers.
  3. **Application load balancers**: Rate-limit application-specific attacks before traffic reaches service frontends.
  4. **Service frontends**: Application-layer rate limiting per user/session/IP.
  5. **Service backends**: Protect databases and internal services from frontend floods.
- **Defendable Architecture**: Use stateless ACLs (not stateful firewalls) at the network edge — stateful firewalls are themselves vulnerable to state exhaustion attacks. Cache at the edge to reduce load on backends and reduce attack surface.
- **DoS vs. DDoS distinction**: DoS can be sourced from a single host; DDoS requires distributed infrastructure (botnet or amplification). DoS defenses often work at the application layer; DDoS defenses need network-layer filtering.

## Key Concepts
- **Amplification attack**: Attacker spoofs victim's IP, sends small requests to many servers (DNS, NTP, memcache), which send large responses to the victim. Defend by filtering UDP from known-amplifiable protocols at the network edge via ACLs.
- **Botnet**: Attacker coordinates many compromised machines to flood a target. Defense: absorb at shared infrastructure (Project Shield example); no single org can provision enough capacity to absorb large botnets individually.
- **Graceful degradation under DoS**: When overloaded by an attack, the system sheds features in priority order (same as resilience degradation) rather than failing completely.
- **Self-inflicted DoS**: Can be caused by bad client retry behavior (no exponential backoff), misconfiguration, synchronized device behavior (Google Home clock sync example), or viral traffic spikes. Mitigate in client design + server load shedding.
- **Client retry behavior**: Clients without exponential backoff amplify incidents: a momentary backend hiccup triggers a wave of retries that prevents recovery. Require exponential backoff with jitter in all clients.
- **Project Shield**: Google's DoS mitigation service as a shared protection layer — an attack that would overwhelm any individual site is absorbed by the aggregate capacity of all protected sites.
- **Threat model approach to DoS prioritization**: Rank attack vectors by "how many machines does the attacker need to cause user-visible disruption?" Prioritize defending the weakest link in the dependency chain first.

## Anti-patterns
- **Stateful firewall as first line of defense**: State exhaustion attack fills the firewall's connection tracking table; switch to stateless ACLs at the network edge.
- **No caching at the edge**: Caching proxies near the network edge absorb a large fraction of requests before they reach expensive backend services.
- **Clients without exponential backoff**: Retry storms from clients without backoff are operationally indistinguishable from a DoS attack — a transient outage becomes sustained.
- **Focusing on recent attacks**: Recency bias leads to defending against last week's attack vector. Use threat modeling to defend against the weakest link, not the most recent attack.

## Key Takeaways
1. Defense at the earliest (cheapest) layer is most cost-effective — drop attack traffic before it reaches expensive processing.
2. Stateless ACLs at the edge + caching proxies remove the majority of DoS attack volume before it reaches your infrastructure.
3. Layered defenses: each layer is capacity-planned only for traffic that breaches the outer layers.
4. Self-inflicted DoS from client retry storms is as damaging as external attacks; require exponential backoff with jitter in all clients.
5. Prioritize defenses by threat model (weakest link in the dependency chain), not by recent attack recency.

## Connects To
- **Ch 8**: Defense in depth applied to DoS specifically
- **Ch 1**: DoS as the intersection of reliability and security
- **Ch 2**: Attacker motivations — DoS extortion as financial attack
