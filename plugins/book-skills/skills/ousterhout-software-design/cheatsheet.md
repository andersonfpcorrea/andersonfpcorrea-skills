# Cheatsheet — A Philosophy of Software Design

## The 15 Design Principles (verbatim from book)

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

## The Red Flags (verbatim from book)

| Red Flag | Diagnosis |
|---|---|
| **Shallow Module** | Interface isn't much simpler than implementation |
| **Information Leakage** | Same knowledge used in multiple modules |
| **Temporal Decomposition** | Code structure based on execution order, not information hiding |
| **Overexposure** | API forces awareness of rarely used features to use common ones |
| **Pass-Through Method** | Does almost nothing except pass arguments to another method |
| **Repetition** | Same nontrivial code repeated over and over |
| **Special-General Mixture** | Special-purpose code not cleanly separated from general-purpose |
| **Conjoined Methods** | Can't understand one without understanding the other |
| **Comment Repeats Code** | All information in comment is obvious from adjacent code |
| **Implementation Documentation Contaminates Interface** | Interface comment describes implementation details |
| **Vague Name** | Name so broad it doesn't convey useful information |
| **Hard to Pick Name** | Difficult to find a precise, intuitive name → design smell |
| **Hard to Describe** | Comment must be long to be complete → design smell |
| **Nonobvious Code** | Behavior/meaning can't be understood with a quick reading |

---

## Deep vs. Shallow: Quick Test

| Question | Deep | Shallow |
|---|---|---|
| Interface vs. implementation | Much simpler | About the same |
| Functionality hidden | A lot | A little |
| Exceptions thrown | Few | Many |
| Common case usage | Very simple | Requires boilerplate |
| Single-use methods | Rare | Frequent |

---

## Strategic vs. Tactical: Quick Comparison

| | Tactical | Strategic |
|---|---|---|
| Primary goal | Make current task work | Great long-term design |
| Complexity | Adds each task | Fights each task |
| Short-term speed | Faster (10–20%) | Slower (10–20%) |
| Long-term speed | Gets slower | Gets faster |
| Design trajectory | Degrades | Improves |
| Investment rate | ~0% | 10–20% of dev time |

---

## Complexity Root Causes → Solutions

| Cause | Symptoms | Solutions |
|---|---|---|
| **Dependencies** | Change amplification, cognitive load | Deep modules, information hiding, pull complexity down |
| **Obscurity** | Unknown unknowns, cognitive load | Good names, comments, consistency, obvious code |

---

## Exception Handling Decision Tree

```
Is the error condition genuinely necessary?
  No → Define it out of existence
  Yes → Can it be handled at a low level invisibly?
    Yes → Mask it (exception masking)
    No → Can many exceptions of this type be handled together?
      Yes → Aggregate (single top-level handler)
      No → Is it unrecoverable and rare?
        Yes → Just crash with a clear message
        No → Document it as part of the interface (last resort)
```

---

## Comment Quality Quick Check

Write a comment. Then ask:
1. Could someone write this comment just by looking at the code? → **Repeats code** (delete or improve)
2. Does it use the same words as the entity name? → **Worthless** (rewrite with different words)
3. Is it at the same level of detail as the code? → **Wrong level** (go higher or lower)
4. Does it describe implementation in an interface comment? → **Contamination** (separate concerns)
5. Is it too long to be complete? → **Design smell** (consider redesigning)
