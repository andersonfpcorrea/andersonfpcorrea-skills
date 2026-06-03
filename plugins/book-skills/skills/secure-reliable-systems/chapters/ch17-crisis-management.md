# Chapter 17: Crisis Management

## Core Idea
Effective crisis management requires a structured command framework (IMAG), disciplined triage to determine whether an incident is a crisis, rigorous operational security to avoid tipping off adversaries, and clear communication discipline — all practiced before they're needed.

## Frameworks Introduced
- **IMAG (Incident Management at Google)**: Google's incident management framework, modeled on the US government Incident Command System (ICS). Establishes a standard, consistent way to handle all incident types — system outages, natural disasters, security breaches. Provides: clear chain of command, defined roles, standardized communication, documentation requirements.
- **Triage Framework**: First determine whether the escalation is (a) a false positive, (b) an easily correctable problem, or (c) a complex, damaging problem requiring organized response. Use preplanned criteria for this decision.
- **Incident Parallelization**: Large incidents must run multiple work streams in parallel (investigation, containment, communications, recovery planning) with explicit coordination between streams. The IC manages dependencies between parallel streams.
- **Handover Protocol**: Shift handovers must transfer full incident context to incoming responders with zero knowledge loss. Use structured handover documents; incoming IC reviews and confirms understanding before outgoing IC disengages.

## Key Concepts
- **Incident Commander (IC)**: Single point of authority during the incident. Manages the incident — not technical work. Delegates technical investigation and remediation; maintains situational awareness; makes escalation and communication decisions.
- **Operational Security (OPSEC)**: During a security incident, restrict information about the investigation to need-to-know. Use out-of-band communication channels (not systems potentially compromised). Do not document investigation status in systems the attacker may have access to.
- **Trading OPSEC for the greater good**: Sometimes (e.g., industry-wide vulnerability, imminent user harm) OPSEC must be broken to warn others. This decision requires explicit authorization from leadership.
- **Compromises vs. Bugs**: An active attacker in your systems changes the response calculus — bugs need patches; compromises need containment, investigation, and attacker eviction, often simultaneously.
- **Ransomware triage example**: Organizations with mature layered defenses (cryptographic execution control) handle ransomware as a routine playbook event. Organizations without layered defenses face crisis-level response.
- **Morale management**: Long incidents drain responders. IC must actively manage morale — rotate people off, celebrate small wins, communicate progress.
- **Communication pitfalls**: (1) Hedging ("might be a problem") is counterproductive — state what is known and unknown clearly. (2) Misunderstandings compound under stress — over-communicate, confirm understanding explicitly. (3) Right people, right detail — executives need status updates, not forensic details.

## Reference Table: IMAG Incident Flow
| Step | Activity |
|---|---|
| Triage | Assess severity; is it a crisis? |
| Declaration | Formally declare incident; assign IC |
| OPSEC + comms | Establish secure communication channel; restrict info |
| Begin response | Assign roles; start parallel work streams |
| Handover | Structured shift handover with context transfer |
| Hand back | Return to normal operations when exit criteria met |
| Closure | Postmortem; lessons learned; procedure updates |

## Anti-patterns
- **Panic as first response**: "Don't panic" is the first IMAG principle. Panic → rushed decisions → mistakes. Take a breath, establish the IC, and start structured triage.
- **Everyone is IC**: Without a single IC, no one has authority; decisions are made by committee under stress. Designate one IC per incident.
- **Communicating investigation status on potentially compromised systems**: The attacker reads your incident Slack channel. Use out-of-band tools for sensitive coordination.
- **Treating a security incident like a reliability incident**: Different information sharing model, different response structure, different recovery considerations.

## Key Takeaways
1. IMAG: single IC, defined roles, structured communication, documented handovers — same for every incident, reliability or security.
2. Triage first: is this a false positive, a playbook event, or a crisis? The response structure depends on the answer.
3. OPSEC during security incidents: communicate on out-of-band channels; restrict information to need-to-know; assume the attacker may be watching.
4. Parallelization: large incidents run multiple streams simultaneously with the IC managing dependencies between them.
5. Handovers must be structured, explicit, and verified — the incoming IC cannot function without full context.

## Connects To
- **Ch 16**: Disaster planning — building the IMAG capability before incidents
- **Ch 18**: Recovery — parallel to crisis management, executed by a different team
- **Ch 15**: Investigation — forensic work during crisis
- **Ch 1**: Security vs. reliability information strategies
