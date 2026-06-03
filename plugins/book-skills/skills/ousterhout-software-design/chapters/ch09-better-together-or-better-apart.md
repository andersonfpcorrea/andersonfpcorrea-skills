# Chapter 9: Better Together or Better Apart?

## Core Idea
The decision to split or join modules must be based on complexity, not arbitrary rules about size or line count. Bring code together when it shares information, simplifies interfaces, or eliminates duplication. Keep it apart when it's truly independent.

## Frameworks Introduced
- **Four costs of subdivision**: (1) More components to track; (2) More management code; (3) Separation hides relationships between components; (4) Duplication.
- **Four signs that code is related**: (1) Shares information; (2) Used together bidirectionally; (3) Overlaps conceptually; (4) Can't understand one without the other.

## Key Concepts
- **Bring together if information is shared**: Two classes that both understand the same file format or protocol are better merged — reduces information leakage.
- **Bring together if it simplifies the interface**: Combining two modules can eliminate intermediate interfaces and enable automation of steps that users would otherwise have to orchestrate.
- **Bring together to eliminate duplication**: Factor repeated code into a method only if the snippet is long and the replacement has a simple signature.
- **Separate general-purpose and special-purpose code**: General-purpose mechanisms should not contain specialized code for a particular use. Put special-purpose code in the upper layers.
- **Method splitting rules**:
  - Good split: Extract a cleanly separable subtask (child doesn't need parent context, parent doesn't need child internals) — the child is likely general-purpose.
  - Sometimes good: Split into two caller-visible methods if the original had overly complex interface trying to do two unrelated things.
  - Bad split (red flag): Results in conjoined methods (can't understand one without the other) or shallow methods.
  - Long methods are fine if they have simple signatures and are easy to read — they can be deep.

## Code Examples

History class (well-separated general-purpose mechanism):
```java
public class History {
    public interface Action {
        public void redo();
        public void undo();
    }
    void addAction(Action action) {...}
    void addFence() {...}  // groups actions for multi-step undo
    void undo() {...}
    void redo() {...}
}
// Special-purpose actions (UndoableInsert, UndoableSelection) live OUTSIDE History
```

## Anti-patterns (Red Flags)
- **Repetition**: Same nontrivial code pattern appears repeatedly — wrong abstraction.
- **Special-General Mixture**: General-purpose mechanism also contains specialized code for one use case.
- **Conjoined Methods**: Can't understand implementation of one without understanding the other. Red flag that the split was wrong.

## Mental Models
- Use-together bidirectionality test: disk block cache always uses hash tables, but hash tables are used in many other places → keep separate. HTTP request reading and parsing both need the same format knowledge → merge.
- "Pick the structure that results in the best information hiding, fewest dependencies, and deepest interfaces."
- Don't split methods because of length; split when it produces genuinely independent, clean abstractions.

## Key Takeaways
1. Subdivision creates complexity too — don't split by default; split when it reduces complexity.
2. Bring together code that shares information or that simplifies the resulting interface.
3. Separate general-purpose mechanisms from special-purpose extensions — put special-purpose code in upper layers.
4. Method length is not a splitting criterion — depth and independence are.
5. Conjoined methods signal a bad split; merge them.

## Connects To
- **Ch 5**: Information sharing is the primary driver for merging
- **Ch 6**: Separation of general-purpose and special-purpose code
- **Ch 7**: Pass-through methods as a symptom of wrong splitting
