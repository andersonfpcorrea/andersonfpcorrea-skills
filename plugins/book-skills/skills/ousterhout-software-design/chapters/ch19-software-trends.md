# Chapter 19: Software Trends

## Core Idea
Evaluate every software development trend from the standpoint of complexity: does it help minimize complexity in large systems? Many trends provide real value; some are over-applied or misunderstood in ways that increase complexity.

## Key Concepts

- **Object-oriented programming**:
  - Interface inheritance: reduces complexity by enabling a single interface for multiple implementations — increases depth.
  - Implementation inheritance: reduces change amplification (shared code) but creates information leakage between parent/subclass. Use with caution; prefer composition.
  - Mechanisms (private, encapsulation) can help, but don't guarantee good design. Shallow OO classes are still bad.

- **Agile development**:
  - Incremental and iterative — aligned with good design; experience reveals better abstractions.
  - Risk: encourages feature-focused thinking and deferring design decisions → tactical programming.
  - Correct interpretation: "increments of development should be abstractions, not features." Design each abstraction cleanly when you first need it.

- **Unit tests**:
  - Enable refactoring confidently → facilitate structural improvement → better design over time.
  - Without tests, developers avoid refactoring → complexity accumulates and design mistakes persist.

- **Test-driven development (TDD)**:
  - Ousterhout is skeptical. TDD focuses on features (making the next test pass), not abstractions. This is tactical programming.
  - Exception: write tests before fixing bugs — the test confirms the fix.
  - The problem with TDD: "there's no obvious time to do design."

- **Design patterns**:
  - Generally good — they arose because they solve common problems with clean solutions.
  - Greatest risk: over-application. Don't force problems into patterns where they don't fit. More patterns ≠ better.

- **Getters and setters**:
  - Shallow methods (typically one line). Add clutter without adding functionality.
  - They expose implementation (the existence and type of the variable) — violates information hiding.
  - "It's better to avoid getters and setters (or any exposure of implementation data) as much as possible."

## Mental Models
- Test suite = enabler of refactoring = enabler of strategic programming. If you can't refactor safely, you can't improve design.
- "Developing incrementally is generally a good idea, but the increments of development should be abstractions, not features."
- Challenge every proposal: "Does this reduce complexity in large systems?" The answer is rarely simple.

## Key Takeaways
1. Interface inheritance (depth) is good; implementation inheritance (coupling) should be used carefully.
2. Agile's incremental approach is right; its feature focus is the dangerous part.
3. Unit tests enable the refactoring that maintains good design over time.
4. TDD is tactical — it focuses on features, not abstractions.
5. Design patterns are good when they fit; bad when forced.
6. Getters and setters are mostly shallow and expose implementation — avoid where possible.

## Connects To
- **Ch 3**: TDD critique is an application of tactical vs. strategic programming
- **Ch 4**: Shallow getters/setters violate the deep module principle
- **Ch 5**: Implementation inheritance causes information leakage
- **Ch 6**: Agile's "YAGNI / implement minimum" conflicts with somewhat-general-purpose design
