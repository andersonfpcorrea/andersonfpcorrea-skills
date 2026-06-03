# Chapter 16: Modifying Existing Code

## Core Idea
When modifying existing code, maintain the strategic mindset. Every change is an opportunity to improve or degrade the design. The default "minimal change" approach is tactical and accumulates complexity.

## Key Concepts
- **Stay strategic in modifications**: The mindset "what's the smallest change I can make?" is tactical. Instead ask: "Given this change, what's the best design the system could have?" Then refactor toward that.
- **If not improving, you're degrading**: "If you're not making the design better, you are probably making it worse." Every modification should leave the code slightly cleaner.
- **Keep comments near code**: The best way to ensure comments stay updated is to place them as close as possible to the code they describe. Farther away = less likely to be updated.
  - Interface comments: in the code file, next to the method body (not in a separate header file).
  - Implementation comments: pushed down to the narrowest scope that includes all the code they describe.
- **Comments belong in code, not commit log**: If information will be useful to future developers, it goes in the code. The commit log is rarely the place developers will look. (Example: a subtle bug fix that reverts if undone — document in the code why the code is there.)
- **Avoid comment duplication**: Document each design decision exactly once. Use references ("See the comment in xyz for...") rather than copies. Copies get out of sync; references become self-evidently stale.
- **Higher-level comments are easier to maintain**: Abstract comments don't reflect code details, so they're stable across minor changes. Comments that do need to be detailed (precision comments) are more fragile.
- **Pre-commit diff scan**: Before committing, scan all changed files to ensure each change is reflected in documentation. Also catches leftover debug code and TODO items.

## Mental Models
- Every change should make the system have "the structure it would have had if you had designed it from the start with that change in mind."
- The "best possible design given current constraints" is the standard. Sometimes that's a quick fix; sometimes it's a refactor. But always the best you can do within your constraints.
- Investment timing: small continuous investments (fix-as-you-go) are more effective than large periodic cleanups. The larger the problem, the more intimidating — which causes it to be deferred further.

## Key Takeaways
1. "Smallest possible change" mindset is tactical. "Best possible design" mindset is strategic.
2. Every modification is an opportunity to improve design — look for improvements beyond the minimum needed.
3. Place comments as close as possible to the code they describe.
4. Important design information belongs in the code, not the commit message.
5. Avoid duplicated documentation — one place, referenced from others.
6. Higher-level comments are easier to maintain because they don't mirror code details.

## Connects To
- **Ch 3**: Strategic vs. tactical programming — this chapter applies it to maintenance
- **Ch 15**: Comments-first approach extends naturally to maintaining comments during modification
- **Ch 13**: Where and how to put cross-module documentation (designNotes)
