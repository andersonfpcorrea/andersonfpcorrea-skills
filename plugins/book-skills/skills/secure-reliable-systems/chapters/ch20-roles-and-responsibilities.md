# Chapter 20: Understanding Roles and Responsibilities

## Core Idea
Security and reliability are everyone's responsibility, not just specialists' — but specialists play critical roles in implementing complex infrastructure, providing expert judgment, setting policies, and building embedded programs that enable the whole org to execute well.

## Frameworks Introduced
- **Distributed Security Responsibility Model**: Security and reliability are integrated into every role — developer, SRE, QA, PM, tech lead, executive. Specialists support and enable, but cannot carry the full burden alone or it becomes a Sisyphean task.
- **Embedding Security Specialists**: Google's model: specialists embed with product teams, provide consultation at design/code/launch stages, and build shared infrastructure that removes security burden from individual teams. Two models: embedded team members vs. consulting team.
- **Special Teams Model**:
  - **Blue Team**: Defensive security; builds and maintains security infrastructure; monitors and responds to threats.
  - **Red Team**: Offensive security; simulates adversaries; finds gaps before real attackers do. Must report findings to Blue Team to close gaps.
  - **Purple Team**: Collaborative exercise where Red and Blue work together to test and improve defenses.

## Key Concepts
- **Security specialist roles**: Implement complex security infrastructure (crypto, AAA systems, secure frameworks); provide expert judgment on risk tradeoffs; devise organization-specific best practices and training; build and maintain security programs.
- **Reliability specialist (SRE) roles**: Develop centralized infrastructure and automation; create monitoring and alerting; drive production readiness reviews; manage error budgets; on-call for production incidents.
- **Vulnerability Reward Programs (Bug Bounties)**: Formalize the relationship with external researchers. Define scope, reward tiers, disclosure norms. Convert potential adversaries into allies. Effective bug bounty programs find real vulnerabilities that internal teams miss.
- **External researcher engagement norms**: Responsible disclosure requires researchers to report before publishing; organizations commit to fix within a defined timeframe (often 90 days per Project Zero norms); researchers get credit and reward.
- **"Don't roll your own crypto"**: The canonical example of work that requires specialists. Cryptography implementation errors are subtle, non-obvious, and catastrophic. Delegate to well-audited libraries and specialists.
- **Security review lifecycle**: (1) Security design review at project outset, (2) Ongoing security audits during development, (3) Pre-launch security review, (4) Continuous monitoring post-launch.
- **Certifications and academia**: Security certifications (CISSP, CISM) provide baseline knowledge but are not substitutes for practical experience. Academia produces security researchers; practical operational knowledge comes from experience.

## Anti-patterns
- **Security team as sole security function**: If only the security team is responsible for security, every security problem requires their involvement — they become a bottleneck and teams wait for "security approval" rather than building security in.
- **Siloed security team**: Security team separate from development teams misses product context; can't make sound risk judgments without understanding the system.
- **Red Team findings not actioned**: Red team exercises are valuable only if Blue Team closes the gaps discovered. A red team that finds the same vulnerabilities repeatedly means findings aren't being acted on.

## Key Takeaways
1. Security is everyone's responsibility — developers, SREs, PMs, and executives all play roles. Specialists support and enable rather than owning security in isolation.
2. Embed security specialists with product teams; consultation at design, code, and launch stages is more effective than post-hoc review.
3. Blue Team + Red Team + external researchers = three sources of adversarial perspective. Use all three.
4. Bug bounty programs convert potential adversaries into allies; define scope and disclosure norms explicitly.
5. Specialists implement the crypto, AAA systems, and secure frameworks that are too complex for every team to build independently.

## Connects To
- **Ch 21**: Culture — how to embed security responsibility across the org
- **Ch 19**: Chrome security team as a case study
- **Ch 16**: IR team structure for disaster response
- **Ch 2**: Attacker profiles — red team simulates these profiles
