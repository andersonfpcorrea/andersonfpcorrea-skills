# Chapter 11: Managing Load

## Core Idea
No single load management strategy works at all scales; combine traffic routing (anycast, global LB), autoscaling, and load shedding in layers to achieve reliable services under variable and unpredictable load.

## Frameworks Introduced
- **GCLB layered architecture** (Google Cloud Load Balancer):
  1. **Anycast**: routes traffic to the nearest PoP at the network layer
  2. **Maglev**: software load balancer; consistent hashing; survives individual LB restarts without dropping connections
  3. **Global Software Load Balancer (GSLB)**: routes to healthiest backend region; drains unhealthy zones
  4. **Google Front End (GFE)**: terminates TLS, applies policy; last hop before the backend

- **Autoscaling principles**:
  - Handle unhealthy machines: health checks must reflect actual serving capacity, not just process liveness
  - Be conservative: scale up aggressively, scale down slowly (avoid thrashing)
  - Set constraints: explicit min/max bounds prevent runaway scaling costs and runaway load
  - Include kill switches: manual override capability is mandatory for emergencies
  - Avoid overloading backends: autoscaling adds frontends, not backends; backends need their own capacity headroom

## Key Concepts
- **Load shedding**: explicitly dropping requests when backends are near capacity rather than queuing them; preserves SLO for requests that are served.
- **Traffic imbalance**: even with LB, backends can become unevenly loaded (cold caches, skewed hashing); monitor per-backend load, not just aggregate.
- **Anycast**: a single IP address advertised from multiple PoPs; BGP routing automatically directs clients to the nearest PoP.
- **Maglev consistent hashing**: assigns requests to backends deterministically; when a backend is removed, only 1/N of traffic is rehashed.

## Case Study: Pokémon GO on GCLB
- At launch, received 50x the predicted traffic.
- GCLB handled the traffic surge automatically; Niantic needed only to provision additional backend capacity.
- Key lesson: global LB provides geographic distribution and auto-failover without application changes.

## Case Study: Load Shedding Attacks
- Service misconfigured to queue indefinitely; under load, queue depth grew until response times exceeded 10 minutes.
- Fix: explicit admission control with a configured max queue depth; requests above threshold returned 503 immediately.
- Key lesson: unbounded queues convert overload into prolonged outages instead of brief degradation.

## Autoscaling Decision Table

| Factor | Guidance |
|--------|----------|
| Scale-up trigger | CPU > 60% or queue depth growing — be early |
| Scale-down trigger | CPU < 20% for 10+ minutes — be late |
| Health check | Must reflect serving readiness, not just process health |
| Min instances | ≥2 per zone for availability during rolling restarts |
| Max instances | Set to avoid budget surprises and backend overload |

## Anti-patterns
- **LB-level availability metrics without backend checks**: a backend that's running but returning 100% errors looks healthy to a process-based health check.
- **Autoscaling without cost constraints**: unconstrained scale-up can exhaust quota or budget.
- **Scale down too fast**: removes capacity before traffic drops; creates oscillation.

## Key Takeaways
1. Layer traffic management: anycast for routing, GSLB for region failover, LB for backend distribution, admission control for overload.
2. Autoscale conservatively: scale up fast, scale down slow.
3. Implement load shedding: bounded queues with fast rejection preserve SLO under overload.
4. Test your LB setup with synthetic overload before production; don't find its limits in a real incident.

## Connects To
- **Ch 12**: NALSD design methodology produces systems that reason about load from the start
- **Ch 17**: Overload recovery at the team/org level (vs. infrastructure level in this chapter)
