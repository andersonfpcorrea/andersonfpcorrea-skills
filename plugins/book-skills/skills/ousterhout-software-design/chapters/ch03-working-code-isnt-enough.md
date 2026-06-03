# Chapter 3: Working Code Isn't Enough (Strategic vs. Tactical Programming)

## Core Idea
The difference between good and bad software design starts with mindset. Tactical programming (get it working fast) accumulates complexity; strategic programming (invest in clean design) produces better systems and is faster in the long run.

## Frameworks Introduced
- **Tactical vs. Strategic Programming**:
  - Tactical: primary goal is to make something work quickly. Each task adds a few small complexities. Fast short-term, catastrophically slow long-term.
  - Strategic: primary goal is a great design that also works. Invest time to improve design continuously.
  - When to use strategic: always, unless you're writing throwaway code.

- **Investment Mindset**: Spend 10–20% of total development time on design investments. This slows you down slightly initially, then pays back faster than you spent within months.

## Key Concepts
- **Tactical tornado**: The heroic programmer who ships fast but leaves behind unmaintainable wreckage for others to clean up. Management often celebrates them; peers dread them.
- **Investment mindset**: Treat design improvements as investments, not costs. Proactive (spending extra time upfront on clean design) and reactive (fixing design problems when discovered).
- **Tactical debt**: Each tactical shortcut contributes complexity; complexity accumulates rapidly when everyone programs tactically.

## Mental Models
- "Working code isn't enough" — working code with terrible design is a liability, not an asset.
- The strategic approach is slower at first (10–20% cost), then faster permanently. The tactical approach is faster at first, then slower permanently. The crossover happens within months.
- Facebook's early "Move fast and break things" vs. Google/VMware's quality-focused culture: both can succeed, but quality culture attracts better engineers and produces more maintainable systems.

## Anti-patterns
- **Punting on complexity**: "I'll clean it up later." Later never comes, and the mess grows.
- **Feature-over-design scheduling**: Always prioritizing new features over cleanup ensures permanent degradation.
- **Big upfront design**: Strategic doesn't mean waterfall. Design emerges incrementally; invest continuously in small improvements.

## Key Takeaways
1. Working code is necessary but not sufficient. The primary goal is great design that also happens to work.
2. Invest 10–20% of development time in design improvements — proactive and reactive.
3. Every time you modify code, ask: "Am I making the design better, or worse?"
4. Tactical tornadoes are costly to organizations even when praised as heroes.
5. Strategic programming becomes faster than tactical within months, not years.

## Connects To
- **Ch 2**: Explains why tactical programming creates the complexity symptoms described there
- **Ch 16**: How to stay strategic when modifying existing code
- **Ch 15**: Write comments first as a design investment, not a documentation afterthought
