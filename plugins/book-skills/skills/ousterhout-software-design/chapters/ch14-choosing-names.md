# Chapter 14: Choosing Names

## Core Idea
Names are a form of abstraction — they create a mental image of what something is. Good names are precise and consistent. Bad names cause cognitive load, ambiguity, and bugs (as dramatically illustrated by a 6-month debugging saga caused by one overloaded variable name).

## Key Concepts
- **Names create images**: When you choose a name, ask: "If someone sees this name in isolation — no declaration, no documentation, no context — how closely will they guess what it refers to?"
- **Precision**: The most common problem is names that are too generic or vague. `count` (count of what?), `x`/`y` (pixels? characters?), `blinkStatus` (status of what? what does true mean?). Use names like `numIndexlets`, `charIndex`, `cursorVisible`.
- **Consistency**: Use the same name for the same concept everywhere; never use it for anything else; ensure the purpose is narrow enough that all variables with that name behave the same. Consistent naming creates cognitive leverage — once you know a name in one context, you can reuse that knowledge.
- **Red flag: if it's hard to name something**, that's a signal the underlying entity may not have a clean design. The difficulty of naming exposes a design weakness.
- **Boolean variables**: Should always be predicates (something that is true or false about the thing). `cursorVisible`, not `blinkStatus`.
- **Distance rule (Gerrand)**: "The greater the distance between a name's declaration and its uses, the longer the name should be." Loop variable `i` is fine for a 3-line loop; not for a 50-line method.

## The 6-Month Bug Story
In the Sprite distributed OS, the variable `block` was used for both physical disk block numbers and logical file block numbers. At one point a logical block was used in a context requiring a physical block, overwriting an unrelated disk block with zeroes. Multiple developers read the faulty code without noticing — the name made them assume the type was correct. `fileBlock` and `diskBlock` would have prevented the bug entirely.

## Anti-patterns (Red Flags)
- **Vague Name**: Name broad enough to refer to many different things. Developer likely to misuse.
- **Hard to Pick Name**: If it's difficult to find a simple, clear, intuitive name, the underlying object may not have a clean design.

## Mental Models
- Names are abstractions: the best names focus on what's most important about an entity while omitting less important details.
- Consistent naming is like a reusable class: once you've learned one instance, you know them all.
- Go style guide counter-example: short names like `i`, `n`, `d` work only if used with extraordinary consistency. Ambiguous short names like `ch` (character or channel?) create the same kind of confusion as the `block` bug.

## Key Takeaways
1. Choose precise, unambiguous names — don't settle for "reasonably close."
2. Consistent naming reduces cognitive load; one name = one concept, always.
3. If a name is hard to choose, the design may need improvement.
4. Boolean variable names should be predicates: `cursorVisible`, not `blinkStatus`.
5. Longer names for things with larger scope; shorter names acceptable when context is immediate.

## Connects To
- **Ch 18**: Good names are one of the two most important techniques for making code obvious
- **Ch 13**: Names reduce the need for comments but don't replace them
- **Ch 17**: Consistency in naming is a key dimension of system-wide consistency
