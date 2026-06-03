# Chapter 8: Pull Complexity Downwards

## Core Idea
When unavoidable complexity must live somewhere, it's better for the module developer to absorb it than to push it to every caller. More users than developers means one developer suffering beats many users suffering.

## Key Concepts
- **Pull complexity downward**: When a module encounters a hard problem, handle it internally rather than throwing exceptions or exporting configuration parameters to callers.
- **Simple interface > simple implementation**: It is more important for a module to have a simple interface than a simple implementation. (Design Principle 6)
- **Configuration parameters as complexity push-up**: Exporting parameters like "retry interval" or "cache size" forces every administrator to make decisions the module could make itself. Often used as an excuse to avoid solving the hard problem.
  - Better: compute sensible defaults automatically (e.g., TCP measuring round-trip time to set retry intervals), expose parameters only when truly necessary.
- **When to pull down**: (a) complexity closely related to existing functionality, (b) pulling down simplifies many callers, (c) pulling down simplifies the interface.

## Mental Models
- "Take a little extra suffering upon yourself to reduce the suffering of your users."
- Before exporting a configuration parameter, ask: "Will users (or higher-level modules) be able to determine a better value than we can?" Often the answer is no.
- The character-oriented text interface (vs. line-oriented) is the canonical example: absorb the complexity of splitting/merging lines inside the text class so every caller doesn't have to.

## Anti-patterns
- **Configuration parameter proliferation**: Hundreds of tunables that users can't possibly set correctly, because the module author punted on making the hard decisions.
- **Exception throwing as cop-out**: Throwing exceptions rather than determining a reasonable policy to handle the condition internally.

## Key Takeaways
1. Prefer a simpler interface over a simpler implementation — users pay for interface complexity at every call site.
2. Avoid configuration parameters unless higher-level modules genuinely know better than you.
3. When in doubt: make reasonable choices for your users, expose controls only for genuine knobs.
4. Pulling complexity down makes modules deeper: simpler interface + more hidden functionality.
5. Don't take it too far: don't pull unrelated complexity down or merge everything into one class.

## Connects To
- **Ch 6**: Somewhat general-purpose APIs naturally pull complexity downward
- **Ch 10**: Exception handling is a prime area where complexity can be pulled downward (masking)
- **Principle 6**: "It's more important for a module to have a simple interface than a simple implementation"
- **Principle 10**: "Pull complexity downward"
