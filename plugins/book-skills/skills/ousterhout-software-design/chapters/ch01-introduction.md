# Chapter 1: Introduction (It's All About Complexity)

## Core Idea
The greatest limitation in writing software is our ability to understand the systems we create. Complexity accumulates over time, and the primary goal of software design is to reduce it.

## Frameworks Introduced
- **Modular Design**: Divide systems into relatively independent modules so a developer can work on one without understanding all others.
  - When to use: Always, as the default approach to managing complexity.
  - How: Decompose into modules with clean interfaces that hide internal implementation.

## Key Concepts
- **Complexity**: Anything that makes a system hard to understand and modify. Not about size — a large system can be simple; a small system can be complex.
- **Incremental development**: Design happens continuously, not just at the start. Initial designs are rarely optimal.
- **Two approaches to complexity**: (1) Eliminate it — simpler, more obvious code; (2) Encapsulate it — modular design hides complexity.

## Mental Models
- Think of design as continuous, not a phase. Every increment is an opportunity to improve or degrade the design.
- The goal is not "working code" but "working code with the minimum possible complexity."

## Key Takeaways
1. Complexity accumulates gradually; fighting it requires deliberate effort throughout development.
2. The primary job of a software designer is to reduce complexity, not just add features.
3. Use this book alongside code reviews — seeing design problems in others' code builds your own design instincts.
4. Moderation applies: every principle has limits; beautiful designs balance competing ideas.

## Connects To
- **Ch 2**: Defines complexity precisely (symptoms and causes)
- **Ch 3**: Strategic vs. tactical programming — the mindset needed to fight complexity
