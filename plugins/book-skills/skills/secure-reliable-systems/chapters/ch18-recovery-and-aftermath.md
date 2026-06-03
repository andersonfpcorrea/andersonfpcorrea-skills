# Chapter 18: Recovery and Aftermath

## Core Idea
Recovery from security attacks differs from reliability recovery because an active adversary may still be present, can change behavior mid-attack, and can reengage during recovery — demanding a dynamic, choreographed response that balances evicting the attacker against learning from them.

## Frameworks Introduced
- **Recovery Logistics — Parallel Teams**: Investigation and recovery must run simultaneously as separate teams. Investigators (forensics, root cause) feed information to recovery engineers; recovery engineers (system builders, SREs) execute restoration. The IC coordinates dependencies.
- **Recovery Timeline Phases**: (1) Scope and plan, (2) Isolate assets (quarantine), (3) Rebuild systems, (4) Data sanitization, (5) Recovery data restore, (6) Credential/secret rotation, (7) Postmortem and documentation.
- **Dynamic Recovery Approach**: Pre-written playbooks provide a starting point but cannot cover an active adversary who adapts. Recovery must be flexible enough to respond to new information from the investigation team.

## Key Concepts
- **Quarantine (Isolating Assets)**: Remove compromised systems from the network before rebuilding — stops attacker from using them as a pivot and prevents recontamination during rebuild.
- **System rebuilds vs. patching**: For security compromises, rebuilding from known-clean images is preferable to patching live systems — patches leave attacker artifacts, backdoors, and modified files in place.
- **Data sanitization**: Before restoring data from backups, validate that the backup predates the compromise window and that the data itself was not tampered with. Restoring attacker-modified data reintroduces the compromise.
- **Credential and secret rotation**: Every credential that may have been exposed — passwords, API keys, certificates, HSM-protected keys — must be rotated as part of recovery. Include service accounts and automation credentials.
- **Observing the attacker**: Sometimes it's strategically valuable to leave the attacker in place temporarily to gather intelligence about their methods, tools, and objectives. This requires explicit authorization and carries significant risk.
- **Air-gapped documentation**: Recovery documentation, checklists, and communication must use systems outside the scope of the potential compromise. Start with notecards and independent service providers until you're sure no recovery team members' machines are compromised.
- **Postmortem**: After recovery, conduct a blameless postmortem to identify the root cause, timeline, and systemic improvements. This is also an opportunity to improve security posture significantly.
- **Recovery as security posture improvement**: The recovery period, when the system is being rebuilt, is the best opportunity to apply deferred security improvements — treat recovery as a chance to build a more secure system, not just restore the previous state.

## Reference Table: Recovery Checklist Categories
| Category | Key Steps |
|---|---|
| Scoping | Identify all potentially compromised systems; determine compromise timeline |
| Quarantine | Network-isolate compromised assets before rebuild |
| Rebuild | Rebuild from known-clean images; do not patch live compromised systems |
| Data | Validate backup integrity + timestamps; sanitize before restore |
| Credentials | Rotate all credentials in scope; include service accounts |
| Validation | Verify rebuilt systems match expected state; run security checks |
| Documentation | Out-of-band records throughout; postmortem after resolution |

## Anti-patterns
- **Patching live compromised systems**: Leaves attacker artifacts in place; doesn't verify system integrity. Rebuild from clean images.
- **Restoring backup without validating compromise window**: If the backup itself is within the attacker's window, restoring it reintroduces the compromise.
- **Using potentially compromised systems for recovery coordination**: If the attacker reads your Slack, your recovery plans are visible. Use out-of-band communication.
- **Treating recovery as "restore to previous state"**: Previous state had the vulnerability that was exploited. Use recovery as an opportunity to improve.

## Key Takeaways
1. Separate investigation and recovery teams — parallel execution, bidirectional information sharing, IC coordination.
2. Quarantine before rebuild — isolate compromised systems from the network first, then rebuild from clean images.
3. Rotate all potentially exposed credentials — passwords, API keys, certificates, service accounts. Be thorough.
4. Validate backup integrity and timestamp before restore — the backup may itself be within the compromise window.
5. Postmortem after every recovery — document timeline, root cause, and systemic improvements. Use recovery as a forcing function to improve security posture.

## Connects To
- **Ch 17**: Crisis management — IC manages recovery logistics
- **Ch 9**: Recovery design — systems built for recoverability are easier to recover
- **Ch 16**: Disaster planning — recovery checklists prestaged before incidents
- **Ch 15**: Investigation — forensic findings scope the recovery
