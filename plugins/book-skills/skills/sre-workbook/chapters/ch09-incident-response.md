# Chapter 9: Incident Response

## Core Idea
Structured incident response (based on ICS) resolves incidents faster and prevents chaos. The key is to declare early, establish clear roles, and keep a real-time written record.

## Frameworks Introduced

### Incident Command System (ICS) — Google's IMAG Adaptation
Three roles, one hierarchy:
1. **Incident Commander (IC)**: Commands and coordinates; assumes all roles not yet delegated; stays in control of the response process. The IC does not debug — they manage.
2. **Operations Lead (OL)**: Applies operational tools; diagnoses and mitigates. Reports to IC.
3. **Communications Lead (CL)**: Public face; periodic status updates to stakeholders; manages inbound inquiries. Reports to IC.

Roles can be collapsed (IC handles CL and OL in small incidents) or expanded (multiple OLs for different subsystems).

### The 3Cs of Incident Management
- **Coordinate**: structure the response effort
- **Communicate**: between responders, within org, to users/external
- **Control**: maintain authority over the response process (not the incident itself)

When an incident goes badly, the failure is almost always in one of the 3Cs.

### Incident Response Principles
1. Maintain a clear line of command.
2. Designate clearly defined roles.
3. Keep a working record of debugging and mitigation in real time.
4. Declare incidents early and often.

## Key Concepts
- **War room**: a shared real-time channel (IRC, Slack, video call) where all incident responders gather; enables centralized communication.
- **Working document**: real-time running notes of what's been tried, what's been ruled out, current hypotheses. Essential for handoffs and postmortems.
- **Declare early**: managed incidents resolve faster; waiting to declare until "it's definitely an incident" costs error budget. Default: if in doubt, declare.
- **Mitigate first, root-cause second**: stop user impact before understanding root cause, unless root cause is trivially obvious.

## Incident Life Cycle
```
Alert fires
    → Assess (is this an incident?)
    → Declare incident, assign IC
    → IC delegates OL, CL as needed
    → OL mitigates (revert, rollback, drain, scale)
    → CL communicates status
    → Impact resolved
    → IC closes incident, assigns postmortem owner
    → Postmortem (Ch 10)
```

## Case Study Lessons

### Case Study 1 (Google Home — "what not to do")
- Team did not declare an incident when problems first appeared.
- Miscommunication between client/server developers caused repeated incorrect mitigations.
- Weekend rollout with no developer on-call; required heroics.
- **Lesson**: Declare early; never roll out during periods when responders are unavailable.

### Case Study 2 (GKE — "what good looks like")
- Zara declared incident at 7:06am (25 min after first page); opened IRC channel immediately.
- Senior SRE Il-Seong started working doc and directed escalation.
- Clear IC (Zara), explicit escalation to infrastructure, cloud networking, compute teams.
- **Lesson**: Working doc + structured escalation = faster root cause even in complex, multi-team incidents.

## Incident Response Checklist (from "Putting Best Practices into Practice")
- [ ] On-call engineer has clear authority to declare an incident
- [ ] Define severity levels and what each requires (IC, CL, war room)
- [ ] All responders know the escalation tree before any incident
- [ ] War room protocol documented (which channel? who notifies?)
- [ ] Working document template exists and is used
- [ ] Drills scheduled quarterly (game days, disaster recovery tests)

## Anti-patterns
- **"Not an incident yet"**: waiting until impact is obvious; by then, budget is burned.
- **Heroics**: resolving incidents via individual effort rather than process; doesn't scale, creates burnout.
- **No working doc**: responders duplicate work; postmortems are inaccurate.
- **IC debugging**: IC loses situational awareness when they become a hands-on debugger.

## Key Takeaways
1. IC manages, OL debugs/mitigates, CL communicates — do not merge IC and OL for complex incidents.
2. Declare early; the cost of a false alarm is far less than an unmanaged incident.
3. Keep a real-time working document; it enables handoffs and powers postmortems.
4. Train for incidents before they happen; game days reveal process gaps that real incidents punish.

## Connects To
- **Ch 8**: On-call pager is the trigger for incident response
- **Ch 10**: Postmortem is the mandatory follow-on to any significant incident
- **Ch 5**: Error budget consumed during incident drives postmortem urgency
