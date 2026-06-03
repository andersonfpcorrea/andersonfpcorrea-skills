# Chapter 19: Load Balancing at the Frontend

## Core Idea
Global load balancing (between datacenters) uses DNS as the first distribution layer, but DNS limitations require multiple mitigations. VIP-based load balancing at the network layer handles the TCP termination problem.

## Frameworks Introduced
- **Two-level load balancing strategy**:
  1. **DNS-level**: Geographic distribution, latency optimization, capacity-aware datacenter selection.
  2. **VIP-level (Virtual IP)**: Within a datacenter; handles connection distribution to actual servers.

- **Traffic objective distinction**:
  - Latency-sensitive (search): route to nearest datacenter by RTT.
  - Throughput-sensitive (video upload): route to underutilized link regardless of distance.

## Key Concepts
- **DNS limitations for load balancing**:
  1. Recursive resolvers: actual client IP is hidden; the nameserver sees the resolver's IP, not the user's.
  2. Caching: one DNS reply may serve 1 user or 1 million users — impact estimation is difficult.
  3. Non-deterministic reply paths: same resolver may use different paths for different queries.
  - **Mitigation**: EDNS0 extension includes client subnet → enables user-level geographic optimization.
- **VIP (Virtual IP)**: A single IP address served by many physical machines. L4 load balancing distributes TCP connections across backends. Allows health checking and transparent failover.
- **Anycast DNS**: Authoritative nameservers at anycast addresses → DNS queries routed to nearest instance → replies optimized for that region.
- **Integration with global control systems**: DNS load balancer integrates with infrastructure state (traffic, capacity, health) to avoid directing users to a degraded datacenter.
- **DNS TTL tradeoff**: Short TTL → faster failover but more DNS load. Long TTL → less DNS load but slower traffic migration.

## Mental Models
- DNS provides coarse geographic routing (datacenter selection); VIP provides fine-grained within-datacenter distribution.
- DNS replies are probabilistic at scale — model resolver populations, not individual clients.
- "Even a supercomputer with a perfect network would still be limited by the speed of light" — physical constraints necessitate geographic distribution.

## Anti-patterns
- **Simple round-robin DNS**: No capacity awareness, no health checking, no geographic optimization.
- **Ignoring DNS caching**: Short TTL aggressive changes may still have 50-100× impact due to resolver caching.
- **Single-level load balancing**: DNS alone can't handle within-datacenter distribution; VIP alone can't handle geographic routing.

## Key Takeaways
1. Use two levels: DNS for geographic/datacenter selection, VIP for within-datacenter connection distribution.
2. DNS limitations (caching, recursive resolvers) require EDNS0 and resolver population modeling to be effective.
3. Traffic type determines routing objective: latency-optimized for interactive requests, throughput-optimized for bulk transfers.
4. DNS load balancer must be integrated with capacity and health state to avoid routing to degraded datacenters.
5. VIPs enable health checking and transparent failover — essential for any user-facing service.

## Connects To
- **Ch 2**: GSLB introduced as the three-level load balancing system.
- **Ch 20**: In-datacenter load balancing — the next level down.
- **Ch 21**: Handling overload — what happens when load balancing distributes too much traffic to one destination.
