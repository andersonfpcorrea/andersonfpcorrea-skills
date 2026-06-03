# Chapter 12: Why Write Comments? The Four Excuses

## Core Idea
Comments are essential — not optional. They capture information that was in the designer's mind but cannot be represented in code. Without comments, abstractions are incomplete, complexity is exposed, and future developers must rederive what you already knew.

## Frameworks Introduced
- **Four common excuses (and rebuttals)**:
  1. "Good code is self-documenting" — False. Code cannot capture informal interface semantics, design rationale, constraints on usage, or the abstraction itself. Comments are how abstractions exist.
  2. "I don't have time" — Writing comments adds ~10% to typing time (~5% of total dev time). Pays back quickly in maintainability.
  3. "Comments get out of date" — Keep comments near code (they're updated when code is updated). Code reviews catch stale comments.
  4. "All comments I've seen are worthless" — True that most comments are bad. That's a reason to write better comments, not to write none.

## Key Concepts
- **Comments complete abstractions**: Without a comment, a method's only abstraction is its declaration (name + parameter types). The declaration is missing too much to be a useful abstraction.
- **Comments and complexity**: Good documentation reduces cognitive load (tells developers what they need to know) and reduces unknown unknowns (clarifies structure so relevant code is discoverable).
- **Comments as design indicator**: If your method requires a long, complicated comment to describe it, the method is probably too complex. Simple comment → simple interface. This becomes more explicit in Ch 15.
- **Information captured in comments**: Low-level details (units, boundary conditions, invariants, null semantics, resource ownership) and high-level concepts (abstractions, design rationale, conditions under which a method should be called).

## Mental Models
- "The overall idea behind comments is to capture information that was in the mind of the designer but couldn't be represented in the code."
- Comments reduce both cognitive load and unknown unknowns — two of the three symptoms of complexity.
- Every abstraction requires documentation. Undocumented abstractions are false abstractions — they appear to simplify but actually require you to read the code anyway.

## Key Takeaways
1. Comments are not optional — they're the only way to capture the informal parts of an interface.
2. Without comments, you have no abstraction: all of a method's complexity is exposed to every caller.
3. Writing good comments takes ~10% of typing time, ~5% of total dev time — negligible cost, large benefit.
4. The goal: capture information that was in the designer's mind but can't be expressed in code.
5. If all comments you've seen are worthless, the solution is to learn to write good ones (Chs 13-16).

## Connects To
- **Ch 13**: What to put in comments and how to write them well
- **Ch 15**: Write comments first — as part of the design process
- **Ch 4**: Comments complete the abstractions that deep modules provide
