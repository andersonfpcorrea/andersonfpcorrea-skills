# Chapter 20: Designing for Performance

## Core Idea
Clean design and high performance are compatible. Simpler code is usually faster. The process: be aware of fundamentally expensive operations, measure before optimizing, then design around the critical path.

## Frameworks Introduced
- **Three-phase performance approach**:
  1. During normal development: be aware of fundamentally expensive operations; choose "naturally efficient" designs that are also clean. Don't micro-optimize; don't ignore performance.
  2. When the system is too slow: measure first. Find the specific critical paths where the system spends time.
  3. Optimize critical paths: redesign around minimal code for the common case. Use "design around critical path" technique.

- **Design around the critical path**:
  1. Ask: "What is the smallest amount of code that must execute for the most common case?"
  2. Disregard existing structure. Design the ideal code for this minimal critical path from scratch.
  3. Find a new design that comes as close as possible to the ideal while maintaining clean structure.
  4. Move special cases off the critical path — check them once at the start; branch to separate handling.

## Key Concepts
- **Fundamentally expensive operations** (order of magnitude): Network round-trip (10–50µs datacenter), disk I/O (5–10ms), dynamic allocation (significant overhead), cache misses (hundreds of instruction times).
- **Death by a thousand cuts**: Ignoring performance leads to 5–10x slowdowns spread throughout, with no single fix that helps much.
- **Measure before modifying**: Programmers' intuitions about performance are unreliable. Measurements identify the actual bottleneck and provide a baseline to confirm improvement.
- **Micro-benchmarks**: Small programs measuring cost of one operation in isolation. A framework for them takes days to build but enables rapid measurement of new questions.
- **Simple code is fast code**: Deep classes reduce layer crossings. Defined-away special cases reduce conditional checks. Eliminating shallow layers removes method call overhead.

## RAMCloud Buffer Example
Original Buffer::alloc: 3 method calls deep, 6 conditions checked on critical path, shallow layers with identical signatures (red flag). Time: 8.8ns for 1-byte append.

After design-around-critical-path redesign: single method, single test ruling out all special cases, new `extraAppendBytes` instance variable simplifying the common case. Time: 4.75ns — 2x speedup. Code size reduced 20%. Design is cleaner and more readable.

## Mental Models
- "Simpler code tends to run faster than complex code." Optimizing for simplicity usually optimizes for performance too.
- The ideal is: identify what must absolutely execute for the common case, then build everything else around that skeleton.
- "There's no point in retaining complexity unless it provides a significant speedup." If a change doesn't improve measurably, revert it.

## Key Takeaways
1. Awareness of expensive operations guides design choices without micro-optimization.
2. Never guess where the bottleneck is — measure, then optimize the measured hotspot.
3. Design around the critical path: minimize code executed in the common case.
4. Move special cases off the critical path to a separate branch.
5. Clean, deep designs are usually fast; shallow, complex designs are usually slow.
6. RAMCloud Buffer: 2x speedup + 20% code reduction + cleaner design — all from one refactoring.

## Connects To
- **Ch 4**: Deep modules reduce layer crossings — also a performance benefit
- **Ch 10**: Defined-away special cases eliminate conditional overhead on critical paths
- **Ch 9**: Shallow methods create layer crossings that slow critical paths
