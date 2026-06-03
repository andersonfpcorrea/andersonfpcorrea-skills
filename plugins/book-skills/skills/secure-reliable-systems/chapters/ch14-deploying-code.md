# Chapter 14: Deploying Code

## Core Idea
The deployment pipeline is an attack surface — every step (source, build, test, deploy) can be subverted — and must be secured by verifying artifacts (not just people), treating config as code, requiring code review as MPA, and enforcing provenance-based deployment policies.

## Frameworks Introduced
- **Software Supply Chain Threat Model**: The full chain from code write → VCS → CI build → test → CD deploy is a sequence of steps each of which can be compromised. Every step must produce auditable proof that it executed correctly; downstream steps must verify that proof.
- **Binary Provenance**: A cryptographically verifiable record of where a binary came from: which source revision, which build system, which build configuration. Stored as metadata attached to the artifact; verified by the deployment system before allowing deployment.
- **Provenance-Based Deployment Policies**: Deployment systems reject artifacts that don't have valid provenance meeting defined policies (e.g., "only binaries built by the CI system from reviewed source may be deployed to production").
- **Verifiable Builds (Hermetic Builds)**: Builds that produce byte-for-byte identical output from the same inputs, in any environment. Enables third-party verification that a published binary matches the reviewed source.

## Key Concepts
- **Code review as MPA**: Mandatory code review is a form of multi-party authorization for the supply chain — no individual can submit, build, and deploy code alone.
- **Treat configuration as code**: All config changes must go through the same review, CI, and deployment pipeline as code changes. Config mistakes are as dangerous as code bugs.
- **Verify artifacts, not just people**: Authenticating the deploying engineer doesn't prevent deploying a compromised artifact. Verify the artifact's provenance through the build chain, not just the identity of who's deploying.
- **Deployment choke points**: Single, well-monitored gateways through which all deployments must pass. Only allow deployments from the choke point; block direct deployments to production.
- **Post-deployment verification**: Automatically check that deployed artifacts match expected provenance after deployment completes. Detect compromise even if the deployment gate was bypassed.
- **Deployment breakglass**: Emergency deployment pathway that bypasses normal controls. Same rules as access breakglass: must be rare, logged, audited.
- **Threat taxonomy for supply chain**:
  - Benign insiders (accidental mistakes)
  - Malicious insiders (gaining more than their role allows)
  - External attackers (compromise of insider's machine/account)

## Reference Table: Supply Chain Threats → Mitigations
| Threat | Mitigation |
|---|---|
| Unreviewed code change | Mandatory code review (MPA) |
| Build from locally modified source | CI builds from VCS only; no local builds to production |
| Compromised signing key | HSM-stored keys; key rotation; access audit |
| Backdoored compiler/build tool | Verifiable/hermetic builds; build tool provenance |
| Old version with known vulnerability | Provenance policy requires recent build; version tracking |
| Config change without review | Config-as-code; same pipeline as code changes |

## Anti-patterns
- **Authenticating the engineer, not the artifact**: An engineer's valid credentials don't verify the binary they're deploying is the one that was reviewed. Verify artifact provenance independently.
- **Skipping code review for "small" changes**: Adversaries exploit the assumption that small changes don't need review. Mandatory means mandatory.
- **Configuration not treated as code**: Configs are deployed to production and can introduce vulnerabilities or outages; they need the same controls.

## Key Takeaways
1. The software supply chain is an attack surface at every step — VCS, CI, CD, configuration — secure each with verification, not just access control.
2. Binary provenance + provenance-based deployment policies prevent deploying code that didn't go through the proper review and build process.
3. Mandatory code review is MPA for the deployment pipeline — no individual should be able to introduce and deploy code unilaterally.
4. Treat config as code with the same review, CI, and deployment pipeline.
5. Deployment choke points + post-deployment verification create defense in depth for the deployment pipeline.

## Connects To
- **Ch 5**: MPA — code review as supply chain MPA
- **Ch 7**: Frequent builds/releases as security practice
- **Ch 13**: Automated testing as supply chain validation gate
- **Ch 9**: Recovery — fast rollout systems are also fast recovery systems
