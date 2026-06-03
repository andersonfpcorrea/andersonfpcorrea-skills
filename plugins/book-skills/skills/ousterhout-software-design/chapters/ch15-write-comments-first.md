# Chapter 15: Write the Comments First (Use Comments as Part of the Design Process)

## Core Idea
Write comments at the beginning of development — before the implementation code. This produces better comments, better designs, and a more enjoyable development process. Comments written afterward are universally poor.

## Frameworks Introduced
- **Comments-first workflow**:
  1. Write the class interface comment.
  2. Write interface comments and signatures for the most important public methods (leave bodies empty).
  3. Iterate over these comments until the structure feels right.
  4. Write declarations and comments for the most important instance variables.
  5. Fill in method bodies; add implementation comments as needed.
  6. As new methods/variables are discovered, write their comments before/alongside their code.

## Key Concepts
- **Why delayed comments are bad**:
  - They often never get written — always deprioritized, then there's too much code to document all at once.
  - Even if written, they're poor — you're mentally checked out, memories of design decisions have faded, you just repeat the code.
  - Writing afterward means comments describe what the code does, not the design thinking that shaped it.
- **Comments as design tool**: Writing a comment forces you to articulate the essence of what something does. This is design thinking. If you can't write a clean, simple comment, the abstraction may be wrong.
- **Comments as canary**: "If a method or variable requires a long comment, it is a red flag that you don't have a good abstraction." Hard to describe → hard to use → probably a poor design.
- **Interface comment ≠ implementation comment**: Writing the interface comment first keeps you focused on the abstraction and interface without being distracted by implementation concerns.
- **Cost argument**: Writing comments adds ~10% to typing time, ~5% of total dev time. Writing first may actually speed up coding (abstractions stabilize before you start implementing).

## Mental Models
- Comments written first are written when design thinking is freshest and most creative. Comments written last are written in cleanup mode.
- "The simpler the comments, the better I feel about my design." Comments quality is a proxy for design quality.
- If you're programming strategically (goal: great design), writing comments should be fun — it's how you evaluate and refine abstractions.

## Anti-patterns (Red Flags)
- **Hard to Describe**: If the comment for a method or variable must be long and complicated to be complete, that's a red flag indicating a design problem.

## Key Takeaways
1. Write interface comments before method bodies — you'll be in design mode, not coding mode.
2. If the comment is hard to write or inevitably long, reconsider the design.
3. Comments written first produce better abstractions because you evaluate them before committing to implementation.
4. The cost of writing comments first is negligible; the design benefits are real.
5. "Comments are how you record and test the quality of your design decisions."

## Connects To
- **Ch 12**: Why comments are necessary — this chapter explains when and how to write them
- **Ch 13**: What content good comments should have
- **Ch 3**: Investment mindset — writing comments first is the canonical design investment
- **Principle 13**: "Comments should describe things that are not obvious from the code"
