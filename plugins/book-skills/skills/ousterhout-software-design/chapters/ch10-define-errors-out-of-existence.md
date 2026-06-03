# Chapter 10: Define Errors Out of Existence

## Core Idea
Exception handling is one of the worst sources of complexity. The best strategy is to eliminate exceptions by redefining API semantics so error conditions don't exist. When that's not possible: mask at low level, aggregate at high level, or crash.

## Frameworks Introduced
- **Four techniques for reducing exception handlers**:
  1. **Define errors out of existence**: Redefine the API so the "error" case becomes normal behavior.
  2. **Mask exceptions**: Handle the exception internally at a low level so higher layers never see it. (TCP retransmits lost packets; callers see a reliable byte stream.)
  3. **Exception aggregation**: Handle many exceptions with one handler at a higher level; let exceptions propagate up to a single dispatch point.
  4. **Just crash**: For errors that are unrecoverable and rare (OOM, disk hard error, corrupt data structure), abort with a clear message rather than half-handling.

## Key Concepts
- **Complexity of exceptions**: Exceptions are part of the interface. Classes with many exceptions are shallower. Exception handling code is harder to write, rarely tested, and accounts for 90%+ of catastrophic distributed-system failures.
- **Define errors out of existence**: Change the semantics so the "error" case is handled naturally. Tcl's `unset`: instead of "delete a variable (error if missing)", redefine as "ensure this variable no longer exists." Now calling unset on a nonexistent variable is correct behavior, not an error.
- **Unix file deletion vs. Windows**: Unix marks the file for deletion and returns success immediately. Existing opens continue working; the file is freed when all handles close. Windows blocks deletion of open files — a constant source of errors. Unix defines two error cases out of existence.
- **Java substring**: Throws `IndexOutOfBoundsException` for out-of-range indices. Better API: "return characters (if any) with index ≥ beginIndex and < endIndex." Out-of-range indices have well-defined behavior — exception eliminated. Python and many other languages do this correctly.
- **Design special cases out of existence**: Represent "no selection" as an empty selection (start == end) rather than a null/boolean flag. The selection-management code needs no special cases; operations on empty selections produce correct empty results naturally.

## Mental Models
- Masking vs. aggregation complement each other: masking handles exceptions at the lowest appropriate level; aggregation handles them at the highest appropriate level. Both reduce total handler count.
- "Code that hasn't been executed doesn't work." Exception handling code is almost never tested — minimizing its volume directly reduces bugs.
- If you're having trouble figuring out what to do with an exception, your caller probably won't know either. That's a sign to handle it at a lower level or define it away.

## Anti-patterns
- **Exception as cop-out**: Throwing exceptions instead of finding a clean design for the difficult case.
- **Over-defensive exception throwing**: Detecting anything suspicious and throwing, creating unnecessary complexity for callers.
- **Per-site exception handlers**: Wrapping every getParameter call in its own try-catch when a single top-level handler would do.

## Key Takeaways
1. The best approach: redefine the API so the error case doesn't exist.
2. Mask exceptions at a low level when callers don't need to know about them.
3. Aggregate: let exceptions propagate to a single high-level handler rather than handling at every call site.
4. Crash on truly unrecoverable errors rather than writing elaborate handler code that never works.
5. Design special cases out of existence — represent them as degenerate normal cases.
6. Exceptions thrown by a class are part of its interface; fewer exceptions = simpler, deeper class.

## Connects To
- **Ch 4**: Fewer exceptions = simpler interface = deeper module
- **Ch 8**: Exception masking is an instance of pulling complexity downward
- **Principle 11**: "Define errors (and special cases) out of existence"
