# Chapter 13: Comments Should Describe Things That Aren't Obvious from the Code

## Core Idea
Comments must provide information at a different level of abstraction than the code — either lower (precision: exact meaning, units, constraints) or higher (intuition: overall purpose, reasoning). Comments at the same level as code just repeat the code and add no value.

## Frameworks Introduced
- **Four comment categories**:
  1. **Interface comments**: Before class/method declaration. Describes what/how-to-use, not how-it-works. Most important.
  2. **Data structure member comments**: Next to field declarations. Explains semantics, not just name.
  3. **Implementation comments**: Inside method bodies. Explains what/why, not how.
  4. **Cross-module comments**: Describes dependencies crossing module boundaries. Rare but critical.

- **Two levels for useful comments**:
  - Lower-level (precision): Units, boundary conditions (inclusive/exclusive?), null semantics, ownership, invariants.
  - Higher-level (intuition): Overall intent, rationale, context ("how we got here"), the simple mental model for a complex operation.

## Key Concepts
- **Don't repeat the code**: If the comment could be written by someone who only looked at the code, it adds no value. Classic anti-pattern: `// Add a horizontal scrollbar` above `hScrollBar = new JScrollBar(JScrollBar.HORIZONTAL)`.
- **Don't use the same words as the name**: Comments like `/* Obtain a normalized resource name from REQ. */ getNormalizedResourceNames(...)` are worthless.
- **Interface vs. implementation comments**: Keep separate. If interface comment must describe implementation, the module is shallow.
- **Implementation comments: what + why, not how**: Describe what the code is doing and why, not step by step how. Higher-level descriptions for blocks of code; "how we got here" for complex entry conditions.
- **Variable comments: focus on what the variable represents**, not how it is manipulated. Use nouns, not verbs.
- **Cross-module**: When design decisions cross module boundaries, document them in one obvious place and reference from affected locations. Use a `designNotes` file for scattered decisions.

## Code Examples

Bad (repeats code, same-level):
```python
ptr_copy = get_copy(obj)  # Get pointer copy
if is_unlocked(ptr_copy): # Is obj free?
    return obj            # return current obj
```

Good interface comment for method:
```cpp
/**
 * Copy a range of bytes from a buffer to an external location.
 * \param offset  Index within the buffer of the first byte to copy.
 * \param length  Number of bytes to copy.
 * \param dest    Must have room for at least length bytes.
 * \return Actual bytes copied; may be less than length if range extends
 *         past end of buffer. Returns 0 if no overlap.
 */
uint32_t Buffer::copy(uint32_t offset, uint32_t length, void* dest)
```

Good higher-level implementation comment:
```
// Try to append the current key hash onto an existing RPC to the
// desired server that hasn't been sent yet.
```
(vs. bad: "If there is a LOADING readRpc using the same session as PKHash pointed to by assignPos, and the last PKHash in that readRPC is smaller than current assigning PKHash, then...")

## Anti-patterns (Red Flags)
- **Comment Repeats Code**: Information in the comment is already obvious from adjacent code.
- **Implementation Documentation Contaminates Interface**: Interface comment describes implementation details users don't need.

## Key Takeaways
1. Comments must be at a different level of detail than the code — lower (precision) or higher (intuition).
2. Never use comments to repeat the code or just rephrase the method/variable name.
3. Interface comments must describe all info needed to use the class/method, including edge cases.
4. Implementation comments: describe what + why, not the step-by-step how.
5. Variable comments: focus on what the variable represents (noun), not how it's used (verb).
6. Keep cross-module documentation in one place; reference it elsewhere.

## Connects To
- **Ch 12**: Why comments are necessary (establishes the foundation)
- **Ch 15**: Write comments first as a design tool
- **Ch 14**: Good names reduce comment need but don't replace comments
