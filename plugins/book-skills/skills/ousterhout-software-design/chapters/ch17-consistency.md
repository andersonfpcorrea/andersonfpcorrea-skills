# Chapter 17: Consistency

## Core Idea
Consistency is a powerful tool for reducing complexity. When similar things are done in similar ways, developers can reuse knowledge gained from one context to understand another — cognitive leverage at scale.

## Key Concepts
- **Consistency creates cognitive leverage**: Once you learn a pattern or naming convention in one place, it automatically applies everywhere else in the system. Inconsistency forces developers to learn each situation separately.
- **Consistency reduces mistakes**: Assumptions based on familiar patterns are safe when the system is consistent. When it isn't, those assumptions cause bugs.
- **Dimensions of consistency**:
  - **Names**: Use the same name for the same concept; never for different concepts.
  - **Coding style**: Indentation, brace placement, declaration order, naming conventions — enforced by linter.
  - **Interfaces**: Multiple implementations of the same interface — learn one, know them all.
  - **Design patterns**: Known solutions to common problems. When applicable, use them — code becomes more predictable.
  - **Invariants**: Always-true properties of variables or structures. Reduces special cases.

- **Ensuring consistency**:
  1. Document — create a clearly located style guide; refer new members to it.
  2. Enforce — automated checkers are better than human discipline. A pre-commit check that fails on violations instantly eliminates violations.
  3. When in Rome — when working in an existing codebase, look around and match what's there.
  4. Don't change existing conventions unless you have significant new information AND the improvement is large enough to justify updating all existing uses AND you complete the update fully.

## Mental Models
- "When in Rome, do as the Romans do." This prevents the worst form of inconsistency: a codebase with multiple competing conventions, each introduced by someone who thought theirs was better.
- Consistency only works if developers trust: "if it looks like an X, it really is an X." A single exception breaks that trust for the entire codebase.

## Anti-patterns
- Introducing new conventions without completing the migration to them — creates two competing conventions.
- Consistency for consistency's sake: forcing dissimilar things to use the same approach creates confusion (not everything should be a design pattern; not everything should be named the same way).

## Key Takeaways
1. Consistent systems are learnable systems — knowledge of one part transfers to another.
2. Document conventions where they'll be found; enforce them with tools, not willpower.
3. Resist the urge to "improve" conventions mid-project; consistency's value usually exceeds the improvement's value.
4. Invariants are a form of consistency that reduce special-case code.
5. When working in an existing codebase, match existing patterns — don't introduce your preferred style.

## Connects To
- **Ch 14**: Consistent naming is the primary application of this principle
- **Ch 18**: Consistency is one of the two most powerful tools for making code obvious
- **Ch 3**: Consistency maintenance is an investment mindset application
