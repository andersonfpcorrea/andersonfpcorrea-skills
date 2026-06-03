# Chapter 6: General-Purpose Modules are Deeper

## Core Idea
The sweet spot is "somewhat general-purpose": implement functionality to reflect current needs, but design the interface to be general enough for multiple uses. General-purpose interfaces are simpler, deeper, and create better separation between classes.

## Key Concepts
- **Somewhat general-purpose**: Module functionality matches current needs, but its interface doesn't hard-code current use cases. The interface is usable for today's needs without being specific to them.
- **Special-purpose API anti-pattern**: Creating one method per user-interface operation (backspace, delete, deleteSelection) couples the text class to the UI, leaks abstractions, and creates many shallow methods.
- **False abstraction**: A method that claims to hide behavior but actually requires callers to know what it does anyway. `backspace()` purports to hide which characters are deleted, but UI developers read its code anyway — it provides no abstraction value.
- **General-purpose benefit**: Fewer, deeper methods; better separation between layers; reduced cognitive load; reusable for unanticipated purposes.

## Questions to Ask Yourself
1. "What is the simplest interface that covers all my current needs?" — fewer methods with broader applicability = more general-purpose.
2. "In how many situations will this method be used?" — designed for one specific use = red flag of over-specialization.
3. "Is this API easy to use for my current needs?" — if you need lots of extra code to use it, you've over-generalized.

## Code Examples

Special-purpose API (too many shallow, coupled methods):
```java
void backspace(Cursor cursor);       // only called from one place
void delete(Cursor cursor);          // UI-specific concept in text layer
void deleteSelection(Selection sel); // leaks UI abstraction into text
```

General-purpose API (fewer, deeper, reusable):
```java
void insert(Position position, String newText);
void delete(Position start, Position end);
Position changePosition(Position position, int numChars);

// backspace implemented in UI layer:
text.delete(text.changePosition(cursor, -1), cursor);
// delete key:
text.delete(cursor, text.changePosition(cursor, 1));
```

## Anti-patterns (Red Flags)
- **Special-General Mixture**: General-purpose mechanism contains code specialized for a particular use, creating information leakage.

## Key Takeaways
1. General-purpose interfaces are simpler and deeper — counterintuitive but empirically true.
2. "Somewhat general-purpose" is the sweet spot; avoid over-generalization too.
3. If a method is called from only one place, it may be too special-purpose.
4. Generality leads to better information hiding — UI concerns stay in UI, text concerns stay in text.
5. The number of methods is not a goal; depth of each method is.

## Connects To
- **Ch 5**: General-purpose design prevents information leakage across layers
- **Ch 7**: General-purpose vs. special-purpose is also relevant to layer design
- **Ch 9**: Separation of general-purpose and special-purpose code
