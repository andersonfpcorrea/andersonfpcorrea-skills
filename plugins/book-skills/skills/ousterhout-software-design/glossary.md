# Glossary — A Philosophy of Software Design

**Abstraction** — A simplified view of an entity that omits unimportant details. Can fail in two ways: including irrelevant details (adds cognitive load) or omitting important details (false abstraction). (Ch 4)

**Change amplification** — Symptom of complexity: a simple change requires modifications in many places. (Ch 2)

**Classitis** — Syndrome from the mistaken belief that "more classes = better." Results in many shallow classes with accumulated interface complexity. (Ch 4)

**Cognitive load** — Symptom of complexity: how much a developer must know to complete a task. High cognitive load = more time, more bugs. Not correlated with lines of code. (Ch 2)

**Complexity** — Anything related to the structure of a software system that makes it hard to understand and modify. C = Σ(cp × tp). (Ch 2)

**Complexity formula** — C = Σ(cp × tp): overall complexity is each part's complexity weighted by time spent on it. (Ch 2)

**Configuration parameters** — Exported tunables that push decision-making to users/admins. Often an excuse to avoid solving the problem. Prefer computing defaults automatically. (Ch 8)

**Context object** — Object that stores all global/application state to avoid pass-through variables. Enables multiple instances in one process. Has its own complexity; prefer immutable fields. (Ch 7)

**Conjoined methods** — Two methods so interdependent that you can't understand one without understanding the other. Red flag indicating a bad split. (Ch 9)

**Deep module** — A module with powerful functionality and a simple interface. Interface is much simpler than the implementation. (Ch 4)

**Dependencies** — One cause of complexity. A piece of code cannot be understood or modified in isolation; it relates to other code that must also be changed. (Ch 2)

**Design it twice** — Deliberately exploring multiple design alternatives before settling on one. Produces better results and builds design skill. (Ch 11)

**Dispatcher** — A method that uses its arguments to select one of several other methods to invoke. Legitimate form of same-signature methods (unlike pass-through methods). (Ch 7)

**False abstraction** — An interface that appears to simplify but actually requires callers to understand its internals anyway. (Ch 6)

**Information hiding** — The primary technique for creating deep modules. Each module encapsulates design decisions invisible to users. (Ch 5)

**Information leakage** — A design decision reflected in multiple modules, creating a dependency between them. One of the most important red flags. (Ch 5)

**Interface (formal)** — The parts of a module's interface specified explicitly in code and checkable by the compiler: signatures, types, exceptions. (Ch 4)

**Interface (informal)** — The parts of a module's interface that can only be described in comments: high-level behavior, constraints, side effects, semantics. (Ch 4)

**Investment mindset** — Treating design improvements as investments with future payoff, not costs to minimize. Spend 10–20% of dev time on investments. (Ch 3)

**Module depth** — A measure of value: benefit (functionality) relative to cost (interface complexity). Deep = high benefit / low cost. (Ch 4)

**Obscurity** — One cause of complexity. Important information is not obvious: vague names, undocumented assumptions, inconsistencies. (Ch 2)

**Pass-through method** — A method that does almost nothing except invoke another method with the same API. Adds interface complexity without adding functionality. Red flag. (Ch 7)

**Pass-through variable** — A variable passed through a long chain of methods that only the bottommost method uses. Forces all intermediate methods to know about it. (Ch 7)

**Pull complexity downward** — Design principle: when complexity must live somewhere, absorb it in the module rather than pushing it to callers. (Ch 8)

**Red flag** — A symptom that suggests a design problem. Used in code review to identify areas requiring redesign. (Ch 1)

**Shallow module** — A module whose interface is complex relative to its functionality. Doesn't provide much help managing complexity. (Ch 4)

**Somewhat general-purpose** — The sweet spot for module design: functionality reflects current needs, but interface is general enough for multiple uses. (Ch 6)

**Special-general mixture** — A general-purpose mechanism that contains code specialized for a particular use. Creates information leakage between mechanism and use case. (Ch 9)

**Strategic programming** — Development approach where the primary goal is a great long-term design, not just making the current task work quickly. (Ch 3)

**Tactical programming** — Development approach focused on making things work quickly. Accumulates complexity; faster in the short term, slower in the long term. (Ch 3)

**Tactical tornado** — A developer who ships code extremely fast with no regard for design quality, leaving cleanup for others. (Ch 3)

**Temporal decomposition** — Structuring code around the order operations execute, not around the knowledge they require. Almost always causes information leakage. (Ch 5)

**Unknown unknowns** — The worst symptom of complexity: you don't know what you need to know, or even that there's something missing. Leads to bugs invisible until runtime. (Ch 2)

**Zero tolerance (for complexity)** — The mindset required to control incremental complexity accumulation: refuse to add "just a small kludge." (Ch 2)
