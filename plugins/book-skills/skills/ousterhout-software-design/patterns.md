# Patterns & Techniques — A Philosophy of Software Design

## Deep Module Design

**When to use**: When designing any class, subsystem, or service.
**How**: Ask "what functionality does this module need to provide?" Then design the simplest possible interface that exposes that functionality. The interface should be much simpler than the implementation.
**Trade-offs**: Requires more implementation work to absorb complexity internally. Worth it because users outnumber implementers and interface cost is paid at every call site.
**Example**: Unix I/O — 5 system calls hide hundreds of thousands of lines.

---

## Information Hiding

**When to use**: For every design decision: file formats, protocols, algorithms, data structures.
**How**: Ask "what can I hide from the interface?" For each piece of hidden information, verify it appears nowhere in the interface. Test: can users of this module work without knowing this information?
**Trade-offs**: Requires careful API design. Some information genuinely must be exposed (e.g., performance-critical configuration). Balance: hide what users don't need; expose what they do.
**Anti-pattern**: Private variables with public getters/setters — the information is still exposed.

---

## Pull Complexity Downward

**When to use**: When a module encounters an unavoidable problem — exception handling, policy decisions, configuration.
**How**: Handle internally; make reasonable defaults; compute parameters automatically. Before exporting a configuration parameter, ask "will callers know better than I do?"
**Trade-offs**: More work for the implementer; much less work for every caller. Only appropriate when the complexity is related to the module's existing functionality.

---

## Define Errors Out of Existence

**When to use**: When designing any API that might throw exceptions for "incorrect" usage.
**How**: Redefine the operation semantics so the "error" case becomes correct behavior. Tcl unset: "ensure variable no longer exists" vs. "delete variable (error if missing)." Java substring: "return characters in range [begin, end)" with clamping vs. "throw if out of bounds."
**Trade-offs**: May occasionally hide bugs (e.g., calling delete on something that shouldn't exist). Counter-argument: fewer exceptions = simpler APIs = fewer other bugs.

---

## Exception Aggregation

**When to use**: When multiple methods can throw the same class of exception (e.g., missing parameters in a web server).
**How**: Let exceptions propagate upward to a single handler near the top of the call stack that can handle all of them uniformly. Each thrower includes the human-readable error message; the handler just formats the response.
**Trade-offs**: Reduces per-site handler code dramatically. Requires exception classes designed for propagation. Works best when exceptions at multiple levels can be handled the same way.

---

## Exception Masking

**When to use**: When lower-level exceptional conditions should not be visible to higher-level users.
**How**: Catch the exception internally and either retry (TCP retransmit), continue (NFS hanging until server recovers), or substitute a safe default. Return normally to the caller.
**Trade-offs**: Makes the module deeper and cleaner. Wrong if callers genuinely need to know about the condition. TCP is the canonical right use.

---

## Design-it-Twice

**When to use**: For every major design decision — interface design, implementation approach, system decomposition.
**How**: Generate 2-3 radically different alternatives. List pros/cons of each with focus on ease of use for higher-level software. Choose the best or combine the best aspects.
**Trade-offs**: Costs 1-2 hours for a class-level decision. Pays back in significantly better design. Also builds design skills that make future decisions faster.

---

## Write Comments First

**When to use**: When creating any new class or method.
**How**: Write the interface comment before writing any code. Iterate on it until the abstraction is clean. If the comment is hard to write or inevitably long, redesign.
**Trade-offs**: Adds ~10% to typing time. Produces better abstractions, better designs, better comments. Comments written first are stable; comments written last are rushed.

---

## Context Object Pattern

**When to use**: When a value or set of values is needed in multiple distant parts of a system (would otherwise be pass-through variables).
**How**: Create a Context object containing all global/application state. Store a reference to it in major objects. Pass it to constructors rather than threading through every method signature.
**Trade-offs**: Avoids pass-through variables. Has global-variable-like downsides (nonobvious dependencies). Best practice: make context fields immutable after initialization.

---

## Separate General-Purpose and Special-Purpose Code

**When to use**: When a module contains both a general-purpose mechanism and code specialized for one particular use of that mechanism.
**How**: Extract the general-purpose core into its own class/module. Place special-purpose code in the layers that understand the particular use. (History class with pluggable Action objects is the canonical example.)
**Trade-offs**: Requires an upfront design investment to identify the right abstraction boundary. The general-purpose core becomes reusable for unanticipated purposes.

---

## Design Around the Critical Path

**When to use**: When a specific operation is a proven performance bottleneck.
**How**: (1) Identify minimum code needed in the common case. (2) Design "ideal" code for this path, ignoring existing structure. (3) Find a clean design that keeps the ideal path intact while adding necessary abstraction. (4) Move special cases to a branch off the critical path.
**Trade-offs**: Produces code optimized for the common case. Special cases are handled separately with less regard for performance. RAMCloud Buffer: 2x speedup + 20% code reduction from one refactoring.

---

## designNotes File

**When to use**: When cross-module design decisions don't have a single obvious home.
**How**: Create a `designNotes` file divided into labeled sections (e.g., "Zombies" for zombie-server handling). In each affected piece of code, add: `// See "Zombies" in designNotes.`
**Trade-offs**: Single source of truth for cross-cutting decisions. Disadvantage: documentation is distant from affected code, harder to keep updated.
