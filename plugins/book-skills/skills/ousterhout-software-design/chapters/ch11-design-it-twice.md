# Chapter 11: Design it Twice

## Core Idea
Your first design idea is almost never the best. Deliberately exploring multiple alternatives — even for small modules — produces significantly better designs and develops your design skills over time.

## Frameworks Introduced
- **Design-it-twice process**:
  1. Define the interface or structure you're designing.
  2. Generate 2-3 alternatives that are radically different from each other.
  3. List pros and cons of each (focus on ease of use for higher-level software, simplicity, generality, implementation efficiency).
  4. Choose the best, or combine the best aspects of multiple alternatives.
  5. If no alternative is attractive, let the problems identified drive new designs.

## Key Concepts
- **Applies at multiple levels**: Interface design → implementation design → system architecture → UI features.
- **Radically different alternatives**: Trying only minor variations teaches little. Design alternatives that challenge different assumptions.
- **Time cost is low**: For a class, an hour or two. Small relative to implementation time, large relative to design quality improvement.
- **Smart-person trap**: Highly intelligent developers often skip the second design because their first ideas have always been good enough. This works in simple domains but fails in complex software design. "No one is good enough to get it right with their first try."
- **Skill builder**: The process of comparing alternatives teaches you what factors make designs better or worse. Over time this internalizes.

## Mental Models
- "Design it twice" is not about insecurity — it's about recognizing that software design is genuinely hard, and hard problems require exploration.
- When all alternatives are unattractive, use the problems with each to motivate a new approach. Red flags from alternatives point toward better designs.
- This principle compounds: experienced designers who practiced design-it-twice can identify good designs more quickly.

## Key Takeaways
1. Always consider at least two design alternatives before settling on one.
2. Make the alternatives radically different — not just minor variations.
3. The extra time is small relative to implementation and the quality improvement is large.
4. The process builds design skill that pays forward indefinitely.
5. If your first idea is always your best, you're probably working on problems that are too easy.

## Connects To
- **Ch 3**: Investment mindset — designing it twice is one of the best investments
- **Ch 6**: Comparison of general-purpose vs. special-purpose interfaces is an example of this
- **Ch 15**: Write comments first — forces you to evaluate the design early
- **Principle 12**: "Design it twice"
