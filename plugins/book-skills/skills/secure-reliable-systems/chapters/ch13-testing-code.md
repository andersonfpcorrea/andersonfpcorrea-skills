# Chapter 13: Testing Code

## Core Idea
Testing is a multi-layered discipline for gaining confidence in security and reliability properties — unit tests catch narrow bugs, integration tests verify component interactions, fuzz tests discover unexpected inputs, and static analysis enforces invariants across the codebase.

## Frameworks Introduced
- **Testing Pyramid for Security**: Unit tests (fast, focused) → Integration tests (component interactions) → Dynamic analysis / fuzzing (unexpected input exploration) → Static analysis (whole-codebase invariants). Each layer complements the others; none is sufficient alone.
- **Fuzz Testing (Fuzzing)**: Automatically generate large numbers of semi-random inputs and feed them to the program. Coverage-guided fuzzers track which code paths have been exercised and generate inputs that explore new paths. Discovers crashes, memory corruption, and behavior on edge cases no human would think to test.
- **Static Program Analysis**: Analyze source code or binaries without executing them. Ranges from simple pattern matching (linters) to abstract interpretation to formal verification. Finds bugs across the entire codebase including code never exercised in testing.

## Key Concepts
- **Hermetic tests**: Tests that produce the same result in isolation, every time. Non-hermetic tests (which call external services, rely on real clocks, or share state) are "flaky" — they erode trust in the test suite and are ignored.
- **Mocks / stubs / fakes**: Controlled substitutes for external dependencies in unit tests. Allow testing error paths that are hard to trigger in real systems. Important: use consistent terminology in your org.
- **Coverage-guided fuzzing**: Fuzzer instruments the binary to track branch coverage; generates inputs that maximize new code path exploration. Much more effective than purely random mutation.
- **Fuzz driver**: A small program that accepts arbitrary bytes from the fuzzer and feeds them to a target function. Should be as simple as possible — the fuzzer provides the variation.
- **Continuous fuzzing**: Run the fuzzer perpetually in CI/CD, not just during security reviews. New code paths are constantly tested; bugs are caught close to introduction.
- **Abstract interpretation**: Static analysis technique that mathematically over-approximates all possible program behaviors to prove properties hold for all inputs (not just tested inputs). Used for null-pointer analysis, buffer overflow detection, etc.
- **Formal methods**: Constructing mathematical proofs that a system has specified properties. Highly confident but expensive (seL4 microkernel proof took ~20 person-years). Practical for isolated components (cryptographic libraries, microkernels).
- **Mutation testing**: Verify test quality by introducing deliberate bugs ("mutants") and checking whether tests detect them. If tests pass with a mutant, the tests are insufficient.

## Code Examples
```cpp
// Example fuzz driver (libFuzzer / AFL compatible)
extern "C" int LLVMFuzzerTestOneInput(const uint8_t *data, size_t size) {
  // Feed arbitrary bytes to the target function
  // Never crash; return non-zero only on expected errors
  ParseInput(data, size);
  return 0;
}
```

```cpp
// Example GoogleTest unit test with security focus
TEST(IsPrimeTest, Trivial) {
  EXPECT_FALSE(IsPrime(0));    // boundary: 0
  EXPECT_FALSE(IsPrime(1));    // boundary: 1
  EXPECT_TRUE(IsPrime(2));     // smallest prime
  EXPECT_TRUE(IsPrime(3));
}
```

## Anti-patterns
- **Testing only the happy path**: Security bugs live in error paths, edge cases, and unexpected input combinations — test those explicitly.
- **Flaky tests**: Tests that sometimes pass and sometimes fail are worse than no tests — engineers learn to ignore them.
- **Overabstraction in mocks**: Tests that assert on the order of function calls test the control flow, not the behavior. Test observable outcomes.
- **Running fuzzer only once**: Fuzzing must be continuous — new code introduces new paths; one-time fuzzing misses bugs introduced after the run.

## Key Takeaways
1. Fuzz test parsers, protocol handlers, and any code that processes external input — coverage-guided fuzzing discovers edge cases no human thinks to test.
2. Hermetic tests are mandatory for reliable CI — fix flaky tests immediately before engineers learn to ignore failures.
3. Static analysis enforces invariants (null safety, buffer bounds, SQL safety) across the entire codebase, including code never touched by test inputs.
4. Continuous fuzzing in CI is the industry best practice for security-critical code — run it always, not just at security review time.
5. Mutation testing validates test quality — if you can delete new code and all tests still pass, the tests aren't testing the right thing.

## Connects To
- **Ch 6**: Invariants — testing as one mechanism to gain confidence (not sufficient alone)
- **Ch 12**: Code frameworks — tests verify framework-enforced invariants
- **Ch 14**: Deployment — automated tests as supply chain validation gates
