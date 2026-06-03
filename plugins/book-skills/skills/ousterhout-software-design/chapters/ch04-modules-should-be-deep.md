# Chapter 4: Modules Should Be Deep

## Core Idea
The best modules provide powerful functionality behind simple interfaces. "Deep" means the interface is much simpler than the implementation. Deep modules are the primary tool for managing complexity.

## Frameworks Introduced
- **Depth Metaphor**: Visualize a module as a rectangle. Width = interface complexity (the cost). Height = functionality (the benefit). Deep modules are tall and narrow; shallow modules are wide and short.
  - Deep module: Unix I/O (5 syscalls hiding hundreds of thousands of lines of implementation)
  - Shallow module: Java's `addNullValueForAttribute(String attr)` — the method just calls `data.put(attr, null)`.

## Key Concepts
- **Module**: Any unit with an interface and an implementation — classes, methods, subsystems, services.
- **Interface (formal + informal)**: Formal = signatures checked by compiler. Informal = high-level behavior, constraints on usage, side effects — documented only in comments.
- **Abstraction**: A simplified view that omits unimportant details. Can go wrong in two ways: including irrelevant details (increases cognitive load) or omitting important details (creates false abstraction / obscurity).
- **Deep module**: Simple interface, large functionality. A module where the interface is much simpler than the implementation.
- **Shallow module**: Interface nearly as complex as implementation. Provides little help managing complexity.
- **Classitis**: Mistaken belief that "more classes = better." Produces systems with many shallow classes, each with its own interface, creating enormous system-level complexity.

## Code Examples

Unix I/O — the canonical deep interface:
```c
int open(const char* path, int flags, mode_t permissions);
ssize_t read(int fd, void* buffer, size_t count);
ssize_t write(int fd, const void* buffer, size_t count);
off_t lseek(int fd, off_t offset, int referencePosition);
int close(int fd);
```
Five calls hide hundreds of thousands of lines covering disk layout, permissions, caching, scheduling, buffering, device drivers, and more.

Java I/O anti-pattern (shallow + classitis):
```java
FileInputStream fileStream = new FileInputStream(fileName);
BufferedInputStream bufferedStream = new BufferedInputStream(fileStream);
ObjectInputStream objectStream = new ObjectInputStream(bufferedStream);
```
Three objects to open one file. Buffering must be requested explicitly — if forgotten, I/O is silently slow.

## Anti-patterns (Red Flags)
- **Shallow Module**: Interface isn't much simpler than the implementation. The cost (interface complexity) nearly equals the benefit.
- **Classitis**: Driven by "small class" dogma, produces many shallow classes with accumulated interface complexity.

## Key Takeaways
1. A module's interface is its COST; functionality is its BENEFIT. Maximize benefit/cost ratio.
2. Simple interface > simple implementation. Users outnumber implementers.
3. Garbage collectors and Unix I/O are the best examples of deep design: near-zero interface, huge functionality.
4. "Methods should be short" is wrong guidance. Length doesn't matter; depth does.
5. Design interfaces so the common case is as simple as possible.

## Connects To
- **Ch 5**: Information hiding — the primary technique for achieving deep modules
- **Ch 6**: General-purpose modules are deeper than special-purpose ones
- **Ch 8**: Pull complexity downward to make the module deeper
- **Principle 4**: "Modules should be deep" (Design Principles list)
