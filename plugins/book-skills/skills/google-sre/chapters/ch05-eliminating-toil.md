# Chapter 5: Eliminating Toil

## Core Idea
Toil is not just work you dislike — it is a specific category of operational work that scales linearly with service growth. Unchecked toil expands to 100% of SRE capacity; the 50% cap is structural protection against this fate.

## Frameworks Introduced
- **Toil definition**: Work that is ALL of: manual, repetitive, automatable, tactical (interrupt-driven), has no enduring value, and scales O(n) with service growth.
  - Any one attribute increases the likelihood it's toil; work matching several is almost certainly toil.
- **The 50% Rule**: At least 50% of every SRE's time must be engineering work (project work producing lasting improvement). Ops work including toil is capped at 50%.

## Key Concepts
- **Toil vs. overhead**: Overhead = administrative work not tied to running a service (meetings, HR). Distinct from toil.
- **Engineering work categories** (not toil):
  - *Software engineering*: Writing/modifying code — automation, tools, reliability features.
  - *Systems engineering*: Configuring production systems producing lasting improvement from one-time effort.
- **Toil categories at Google** (reported by SREs, ranked by frequency):
  1. Interrupts (non-urgent service messages/email) — largest source
  2. On-call (urgent) response
  3. Releases and pushes
- **O(n) growth = toil signal**: If a task grows linearly with users/traffic/service size, it's toil. An ideally designed service grows 10× with zero additional operational work.
- **Average toil at Google**: ~33% of SRE time (below the 50% cap, but not zero — on-call floor requires it).
- **On-call toil floor**: 6-person rotation = 2/6 = 33% minimum on-call time; 8-person = 25%.

## Mental Models
- "If a human operator needs to touch your system during normal operations, you have a bug." (Carla Geisser)
- Toil is not a person's fault — it's a system design problem. Eliminate the underlying condition, don't just automate the human workaround.
- Every week, commit to eliminating a bit of toil through engineering.
- Novel work ≠ toil. The first or second time you do something isn't toil; the N-th repetition is.

## Anti-patterns
- **Accepting toil as permanent**: If a task recurs and could be automated, leaving it manual is a design debt that compounds.
- **"We need human judgment here"**: Be skeptical. Excessive alerts requiring "human judgment" usually indicate poor system design, not an inherent requirement for humans.
- **Toil creep**: Ops teams that don't push back on toil acquisition create precedent; devs shift operational responsibilities to SRE indefinitely.
- **Celebrating heroic toil**: Rewarding engineers for manual firefighting incentivizes the wrong behavior. Reward automation.

## Key Takeaways
1. Toil = manual + repetitive + automatable + tactical + no enduring value + O(n) growth. All six criteria together, but any few together are a strong signal.
2. The 50% cap is enforced structurally: when exceeded, overflow goes back to the dev team — this creates feedback pressure on devs to build self-managing systems.
3. Toil in small amounts is fine. Toil > 50% causes burnout, career stagnation, and team attrition.
4. Quarterly surveys show Google averages ~33% toil — the cap is real, not just aspirational.
5. Target: automated, not just automated. Systems should self-heal without human involvement.

## Connects To
- **Ch 1**: The 50% cap introduced as a tenet of SRE.
- **Ch 7**: Automation — the engineering answer to eliminating toil.
- **Ch 11**: Being On-Call — the floor on toil from on-call shifts.
- **Ch 29**: Dealing with Interrupts — the #1 source of toil.
