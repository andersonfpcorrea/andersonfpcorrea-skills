# Chapter 5: Information Hiding (and Leakage)

## Core Idea
Information hiding is the primary technique for creating deep modules. Each module encapsulates design decisions so they're invisible to users. The opposite — information leakage — is one of the most important red flags in software design.

## Key Concepts
- **Information hiding**: Each module encapsulates knowledge about implementation details (data structures, algorithms, protocols, assumptions). That knowledge is embedded in implementation, not visible in the interface.
- **Information leakage**: A design decision is reflected in multiple modules. Both modules depend on it; both must change if it changes. Back-door leakage (two classes knowing a file format without exposing it in their interfaces) is more pernicious than interface leakage.
- **Temporal decomposition**: Structuring code around when operations execute rather than what knowledge they require. Classic example: separate "read file" and "parse file" classes — both need to understand the file format (information leakage).
- **Private ≠ information hiding**: Private variables still leak if exposed via getters/setters. True hiding = the information is irrelevant and invisible to users.
- **Partial hiding**: Information needed by only a few users, accessed through separate methods not visible in the common case, still reduces dependencies compared to fully-exposed information.

## Mental Models
- When you find information leakage between classes, ask: "How can I reorganize so this knowledge only affects one class?" Merge the leaking classes, or extract a new class that encapsulates just that information.
- Temporal decomposition is a trap: order of operations is on your mind, so it shapes your code structure. But most design decisions appear at multiple execution times, so temporal structure usually causes leakage. Design around knowledge, not order.
- Information hiding increases module depth: hiding more information → simpler interface → deeper module.

## Anti-patterns (Red Flags)
- **Information Leakage**: Same knowledge in multiple modules. If the file format changes, both classes must change.
- **Temporal Decomposition**: Code structure mirrors execution order, not knowledge boundaries. Both "read" and "parse" know the format → merge them.
- **Overexposure**: API forces callers to know about rarely used features to use commonly used ones. (e.g., Java's BufferedInputStream requiring explicit construction)

## Code Examples

Shallow parameter API (leaks internal representation):
```java
public Map<String, String> getParams() {
    return this.params;  // exposes internal Map — changes to representation break callers
}
```

Deeper, better API (hides representation):
```java
public String getParameter(String name) { ... }
public int getIntParameter(String name) { ... }
```

## Key Takeaways
1. Ask "what can be hidden?" when designing a new module — more hidden = simpler interface = deeper module.
2. Information leakage is one of the most important red flags; develop high sensitivity to it.
3. Don't be tricked by temporal decomposition — design around knowledge, not execution order.
4. Defaults illustrate partial hiding: callers needn't be aware of the defaulted item in the common case.
5. Classes should "do the right thing" without being explicitly asked (e.g., default buffering in I/O).

## Connects To
- **Ch 4**: Information hiding is the primary mechanism for creating deep modules
- **Ch 6**: General-purpose APIs hide more information than special-purpose ones
- **Ch 9**: When to bring code together vs. separate — information sharing drives this
