# Chapter 9: Simplicity

## Core Idea
Simplicity is a reliability property, not just an aesthetic preference. Every new line of code is a liability. SRE should actively resist complexity and advocate for its elimination.

## Frameworks Introduced
- **Essential vs. Accidental Complexity** (from Fred Brooks):
  - **Essential complexity**: Inherent in the problem — cannot be removed.
  - **Accidental complexity**: Introduced by implementation choices — can and should be eliminated with engineering effort.
  - SRE's job: push back on accidental complexity introduced into systems they operate.

- **"Negative Lines of Code" metric**: Deleting code is a positive contribution. Every line added creates potential for new defects. A smaller codebase is easier to understand, easier to test, and has fewer defects.

- **Minimal API design**: "Perfection is finally attained not when there is no longer more to add, but when there is no longer anything to take away." (Antoine de Saint Exupery). Fewer methods = easier to understand + higher quality per method.

## Key Concepts
- **The virtue of boring**: "Unlike a detective story, the lack of excitement, suspense, and puzzles is actually a desirable property of source code." Surprises in production are SRE's nemesis.
- **Dead code**: Code gated by a permanently-disabled flag is a time bomb. Delete it. Source control enables reverting changes; commented-out code creates confusion.
- **Knight Capital example**: Dead code reactivated by a flag change caused a $440M trading loss in 45 minutes. Unused code paths are real risks.
- **Loose coupling**: Binary-to-binary and binary-to-configuration loose coupling enables: changes to one component without rebuilding the whole system, faster feedback on bugs, independent release cadences.
- **Versioned APIs**: Allow consumers to stay on old versions while migrating — don't force a full-system push every time an API changes.
- **Release simplicity**: "Simple releases are generally better than complicated releases." One change per release makes impact measurement trivial.
- **Stability vs. agility tradeoff**: Exploratory code can sacrifice stability (shelf life, no production path). Production code needs stability; reliable processes paradoxically *increase* developer agility (faster feedback when bugs surface).

## Mental Models
- "At the end of the day, our job is to keep agility and stability in balance in the system."
- Use modularity: each binary has a clear, well-scoped purpose. No "util" or "misc" binaries.
- Every 24/7 service should view every new line of code as a liability — does it serve a clear business goal?
- "SREs should constantly strive to eliminate complexity in systems they onboard."

## Anti-patterns
- **"What if we need this code later?"**: Source control makes this fear baseless. Delete the code.
- **Feature flags for dead code**: A permanently-disabled flag is a time bomb, not a safety net.
- **Monolithic APIs**: Large surface area APIs are harder to test, harder to reason about, and harder to secure.
- **Change batching**: Releasing 10 changes at once makes the root cause of any issue impossible to identify quickly.
- **Stability-agility false tradeoff**: Investing in reliability tooling makes developers faster, not slower.

## Key Takeaways
1. Accidental complexity can always be eliminated with engineering effort — this is SRE's responsibility to push for.
2. Deleting code is a positive contribution. Negative lines of code metric is real.
3. Dead code behind disabled flags is worse than no code — it's a hidden risk.
4. Minimal APIs have higher quality per method and are easier to test, secure, and document.
5. Simple releases: one change per release makes debugging trivially easy.

## Connects To
- **Ch 8**: Release engineering — release simplicity principle.
- **Ch 5**: Toil — accidental complexity generates operational toil.
- **Ch 17**: Testing — simpler systems are easier to test.
