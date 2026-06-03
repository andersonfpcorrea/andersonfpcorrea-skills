---
name: ousterhout-software-design
description: "Knowledge base from \"A Philosophy of Software Design\" by John Ousterhout. Use when applying Ousterhout's frameworks for software complexity management, module design, information hiding, deep vs. shallow modules, strategic vs. tactical programming, error handling, documentation, and code obviousness — or when referencing any of the 15 design principles and 14 red flags."
allowed-tools:
  - Read
  - Grep
argument-hint: "[topic, principle name, red flag, or chapter number]"
---

# A Philosophy of Software Design
**Author**: John Ousterhout  |  **Pages**: ~188  |  **Chapters**: 21  |  **Generated**: 2026-06-02

## How to Use This Skill

- **Without arguments** — load the core frameworks and design principles below
- **With a topic** — ask about `information hiding`, `deep modules`, `tactical programming`, etc.; I find the relevant chapter
- **With chapter** — ask for `ch05` or `ch11`; I load that chapter file
- **Browse** — ask "what chapters do you have?" to see the full index

For details beyond the core frameworks below, I will Read the relevant `chapters/chNN-*.md`, `glossary.md`, `patterns.md`, or `cheatsheet.md` files.

---

## Core Frameworks & Mental Models

### 1. Complexity: The Enemy (Ch 2)

**Complexity = dependencies + obscurity.** It manifests as three symptoms:
- **Change amplification**: simple change requires modifications in many places
- **Cognitive load**: developer must learn too much to make one change
- **Unknown unknowns**: you don't know what you don't know — the worst one, because it causes invisible bugs

Complexity is **incremental** — no single decision makes a system complex. Hundreds of small dependencies and obscurities accumulate. This demands a "zero tolerance" mindset: every small compromise counts.

The formula: C = Σ(cp × tp) — overall complexity is each part's complexity, weighted by time spent on it. Isolating complexity in an untouched area is nearly as good as eliminating it.

### 2. Strategic vs. Tactical Programming (Ch 3)

| | Tactical | Strategic |
|---|---|---|
| Primary goal | Make current task work | Great long-term design |
| Short-term speed | Faster (10–20%) | Slightly slower |
| Long-term speed | Degrades | Gets faster |
| Investment rate | ~0% | 10–20% of dev time |

**Working code is not enough.** The "tactical tornado" — a fast programmer who ships everything quickly — leaves behind unmaintainable messes others must clean up.

**Investment mindset**: Spend 10–20% of development time improving design. Payback within months. Every modification should leave the system slightly better; if you're not improving it, you're degrading it.

### 3. Deep Modules (Ch 4)

The best modules provide powerful functionality behind simple interfaces. Think of a module as a rectangle: width = interface complexity (cost); height = functionality (benefit). Deep modules are tall and narrow.

**Canonical example**: Unix I/O — 5 system calls (`open`, `read`, `write`, `lseek`, `close`) hiding hundreds of thousands of lines covering disk layout, caching, permissions, scheduling, and device drivers. Garbage collectors are even better: zero interface.

**Classitis anti-pattern**: "more classes = better" produces systems of many shallow classes, each with its own interface, accumulating enormous system-level complexity. Method length is not a splitting criterion — depth is.

### 4. Information Hiding (Ch 5)

Each module should encapsulate design decisions (data structures, protocols, algorithms) so they are invisible to users. Information hidden = interface simplified = module deeper.

**Information leakage** — the opposite — occurs when a design decision is reflected in multiple modules. Both must change when that decision changes. The sneakiest form: two classes that both understand a file format, with neither exposing it in their interfaces. Still leakage.

**Temporal decomposition trap**: Structuring code around execution order (read phase, parse phase, write phase) almost always causes information leakage, because the same knowledge is needed at multiple execution times.

### 5. General-Purpose Modules are Deeper (Ch 6)

The sweet spot is **"somewhat general-purpose"**: implement functionality for current needs, design the interface for multiple uses.

Counter-intuitive result: general-purpose interfaces are simpler, not more complex. A single `delete(Position start, Position end)` is simpler than `backspace(Cursor)`, `delete(Cursor)`, and `deleteSelection(Selection)` — and better separates layers.

Test: "What is the simplest interface that covers all my current needs?" If a method is only called from one place, it may be too special-purpose.

### 6. Different Layer, Different Abstraction (Ch 7)

Each layer must provide a different abstraction. If adjacent layers have the same abstraction, the design is wrong.

**Pass-through methods** are the symptom: a method that does nothing but invoke another method with the same API. They add interface complexity without adding functionality. Solution: reassign responsibility, expose the lower layer directly, or merge the classes.

**Pass-through variables** (values threaded through many methods only needed by one) are solved by context objects — a single object containing all global state, stored in major objects, passed to constructors.

### 7. Pull Complexity Downward (Ch 8)

When unavoidable complexity must live somewhere, absorb it in the module rather than pushing it to every caller. More users than developers means one developer suffering beats many users suffering.

"It is more important for a module to have a simple interface than a simple implementation."

Configuration parameters are usually a cop-out — they push decision-making to users who often can't make the right choice. Prefer computing sensible defaults automatically.

### 8. Define Errors Out of Existence (Ch 10)

The best exception-handling strategy is to eliminate exceptions by redefining semantics:
- Tcl `unset`: "ensure variable no longer exists" (not "delete variable; error if missing")
- Java `substring`: return characters in range [begin, end) with clamping (not throw if out of bounds)
- Unix file deletion: mark for deletion, return success immediately — processes with open handles continue normally

When you can't define errors away: **mask** at a low level (TCP retransmit), **aggregate** at a high level (single top-level handler for all missing-parameter errors), or **crash** when truly unrecoverable.

### 9. The Comments Framework (Ch 12-15)

Comments are **essential**, not optional. They capture information the designer knew but code can't express — the informal parts of an interface, design rationale, constraints, semantics.

**Write comments first**: Write the interface comment before the method body. If the comment is hard to write or inevitably long, the abstraction is probably wrong — redesign.

**Two useful levels**: lower-level (precision — units, boundary conditions, null semantics, ownership) and higher-level (intuition — overall purpose, why the code exists). Comments at the same level as code just repeat it.

**Red flags**: Comment Repeats Code; Implementation Documentation Contaminates Interface.

### 10. Obviousness and Consistency (Ch 17-18)

**Consistency** creates cognitive leverage — learn a pattern once, apply it everywhere. Enforce with automated tools, not willpower. Never change conventions mid-project unless the improvement justifies updating all existing uses.

**Obviousness**: Readers should be able to read code quickly and correctly on first reading. Key tools: good names, consistency, judicious white space, strategic comments. Avoid generic containers (Pair<A,B>) — define specific types with meaningful names.

Principle: "Software should be designed for ease of reading, not ease of writing."

---

## The 15 Design Principles (from back of book)

1. Complexity is incremental: you have to sweat the small stuff.
2. Working code isn't enough.
3. Make continual small investments to improve system design.
4. Modules should be deep.
5. Interfaces should be designed to make the most common usage as simple as possible.
6. It's more important for a module to have a simple interface than a simple implementation.
7. General-purpose modules are deeper.
8. Separate general-purpose and special-purpose code.
9. Different layers should have different abstractions.
10. Pull complexity downward.
11. Define errors (and special cases) out of existence.
12. Design it twice.
13. Comments should describe things that are not obvious from the code.
14. Software should be designed for ease of reading, not ease of writing.
15. The increments of software development should be abstractions, not features.

---

## Chapter Index

| # | Title | Key Frameworks |
|---|-------|----------------|
| [ch01](chapters/ch01-introduction.md) | Introduction | Modular design, complexity as the enemy |
| [ch02](chapters/ch02-nature-of-complexity.md) | The Nature of Complexity | Complexity formula, symptoms, causes (dependencies + obscurity) |
| [ch03](chapters/ch03-working-code-isnt-enough.md) | Working Code Isn't Enough | Strategic vs. tactical programming, investment mindset, tactical tornado |
| [ch04](chapters/ch04-modules-should-be-deep.md) | Modules Should Be Deep | Deep module, shallow module, classitis, Unix I/O example |
| [ch05](chapters/ch05-information-hiding.md) | Information Hiding | Information hiding, information leakage, temporal decomposition |
| [ch06](chapters/ch06-general-purpose-modules.md) | General-Purpose Modules are Deeper | Somewhat general-purpose, false abstraction, text editor example |
| [ch07](chapters/ch07-different-layer-different-abstraction.md) | Different Layer, Different Abstraction | Pass-through methods, decorators, pass-through variables, context object |
| [ch08](chapters/ch08-pull-complexity-downwards.md) | Pull Complexity Downwards | Simple interface > simple implementation, configuration parameters |
| [ch09](chapters/ch09-better-together-or-better-apart.md) | Better Together or Better Apart? | Split/join criteria, History class pattern, conjoined methods |
| [ch10](chapters/ch10-define-errors-out-of-existence.md) | Define Errors Out of Existence | Define away, exception masking, exception aggregation, just crash |
| [ch11](chapters/ch11-design-it-twice.md) | Design it Twice | Design-it-twice process, design skill building |
| [ch12](chapters/ch12-why-write-comments.md) | Why Write Comments? | Four excuses, comments complete abstractions |
| [ch13](chapters/ch13-comments-describe-nonobvious.md) | Comments Should Describe Non-Obvious Things | Four comment categories, precision vs. intuition levels |
| [ch14](chapters/ch14-choosing-names.md) | Choosing Names | Precision, consistency, hard-to-name as design smell |
| [ch15](chapters/ch15-write-comments-first.md) | Write the Comments First | Comments as design tool, comments-first workflow |
| [ch16](chapters/ch16-modifying-existing-code.md) | Modifying Existing Code | Stay strategic, comment placement, comment maintenance |
| [ch17](chapters/ch17-consistency.md) | Consistency | Cognitive leverage, consistency enforcement, When in Rome |
| [ch18](chapters/ch18-code-should-be-obvious.md) | Code Should be Obvious | Obvious code techniques, generic containers, event-driven |
| [ch19](chapters/ch19-software-trends.md) | Software Trends | OOP, agile, TDD critique, design patterns, getters/setters |
| [ch20](chapters/ch20-designing-for-performance.md) | Designing for Performance | Measure first, critical path design, RAMCloud Buffer example |
| [ch21](chapters/ch21-conclusion.md) | Conclusion | Synthesis, investment pays off, design is fun |

---

## Topic Index

- **Abstraction** → ch04, ch06, ch12, ch13
- **Agile development** → ch19
- **Change amplification** → ch02
- **Classitis** → ch04
- **Cognitive load** → ch02, ch04, ch05
- **Comments (writing good)** → ch12, ch13, ch15, ch16
- **Complexity (definition, causes, symptoms)** → ch02
- **Configuration parameters** → ch08
- **Conjoined methods (red flag)** → ch09
- **Context object** → ch07
- **Deep modules** → ch04, ch06, ch08
- **Define errors out of existence** → ch10
- **Dependencies** → ch02, ch04, ch05
- **Design it twice** → ch11
- **Design patterns** → ch17, ch19
- **Exception handling** → ch10
- **General-purpose modules** → ch06, ch09
- **Getters and setters** → ch19
- **Information hiding** → ch05, ch06, ch07
- **Information leakage (red flag)** → ch05, ch06, ch07, ch09
- **Investment mindset** → ch03, ch12, ch15, ch17
- **Modular design** → ch01, ch04
- **Naming** → ch14, ch17, ch18
- **Obscurity** → ch02, ch13, ch14, ch18
- **Pass-through methods (red flag)** → ch07
- **Pass-through variables** → ch07
- **Performance** → ch20
- **Pull complexity downward** → ch08, ch10
- **Red flags (all)** → cheatsheet.md
- **Shallow modules (red flag)** → ch04, ch07
- **Software trends evaluation** → ch19
- **Special-general mixture (red flag)** → ch06, ch09
- **Strategic vs. tactical programming** → ch03, ch16, ch19
- **Temporal decomposition (red flag)** → ch05
- **TDD critique** → ch19
- **Test-driven development** → ch19
- **Unit tests** → ch19
- **Unknown unknowns** → ch02
- **Write comments first** → ch15

## Supporting Files

- [glossary.md](glossary.md) — all key terms with definitions and chapter references
- [patterns.md](patterns.md) — all techniques with When/How/Trade-offs
- [cheatsheet.md](cheatsheet.md) — 15 design principles, 14 red flags, and quick-reference tables

---

## Scope & Limits

This skill covers the book content only (1st ed., April 2018, v1.01). Examples are primarily Java and C++, but all principles apply across languages. The book's frameworks (deep modules, strategic programming, information hiding, design-it-twice) are language-agnostic design philosophy.

For language-specific idioms or framework-specific patterns, combine with project-specific skills.
