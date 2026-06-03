# Chapter 7: Management Practices for Software

## Core Idea
Three Lean management practices — WIP limits, visual displays, and monitoring-driven decisions — together predict delivery performance and culture; Change Advisory Boards actively harm performance and should be replaced with peer review.

## Frameworks Introduced
- **Lean Management Three-Component Model**:
  1. **WIP Limits**: Limit work in progress to expose obstacles and drive process improvement. WIP limits alone have a weak effect; combined with visual displays and monitoring feedback, the effect is strong.
  2. **Visual Displays**: Dashboards/kanban boards showing key quality/productivity metrics, defect rates, and current work status — visible to both engineers and leaders, aligned with operational goals.
  3. **Monitoring → Business Decisions**: Use application and infrastructure monitoring data to make business decisions daily (not just to page on-call staff).

- **Lightweight Change Approval Process**: Peer review (pair programming or intrateam code review) produces superior IT performance to external CAB approval. Recommendation: peer review + deployment pipeline for all change types.

## Key Concepts
- **Lean management**: Management philosophy derived from Toyota Production System; focuses on eliminating waste, limiting WIP, making work visible, and continuous improvement.
- **Change Advisory Board (CAB)**: External body reviewing production changes. The research finding: negatively correlated with lead time, deployment frequency, and restore time; no correlation with change failure rate. "Risk management theater."
- **Segregation of duties compliance without CAB**: Two mechanisms satisfy regulated-industry requirements: (1) peer review of changes before/after commit to version control; (2) all production changes applied only through automated deployment pipeline — providing a complete audit trail.

## Anti-patterns
- **CAB as quality gate**: External approval slows delivery without improving stability. Complex systems cannot be effectively reviewed by parties unfamiliar with their internals.
- **WIP limits without feedback loops**: Limits that don't expose bottlenecks and drive improvement are performative.
- **Monitoring only for alerting**: Infrastructure/application monitoring used purely for paging on-call staff misses its value as a business decision tool.

## Key Takeaways
1. WIP limits, visual displays, and monitoring together drive delivery performance — none of the three alone is sufficient.
2. CABs are negatively correlated with every delivery performance metric and should be replaced with peer review.
3. Lean management practices reduce burnout and drive generative culture — not just delivery performance.
4. Even in regulated industries, segregation of duties can be achieved through pipeline automation and peer review rather than CABs.

## Connects To
- **Ch 2**: CABs are called out in Ch 2 as negatively correlated with both tempo and stability.
- **Ch 3**: Lean management practices drive Westrum generative culture.
- **Ch 9**: Lean management reduces burnout.
- **Appendix A**: Lean management capabilities 15–19.
