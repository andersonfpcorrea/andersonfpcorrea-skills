# Chapter 3: Case Study — Safe Proxies

## Core Idea
Safe proxies are a practical pattern for adding logging, multi-party authorization, and rate limiting to existing systems without modifying them — enabling least-privilege production access and supporting Zero Touch Production.

## Frameworks Introduced
- **Safe Proxy Model**: Clients route through a proxy instead of connecting directly to target systems. The proxy enforces ACLs, logs all RPCs, applies rate limits, and can require MPA before forwarding requests.
- **Zero Touch Production (ZTP)**: Every production change must be made by automation, pre-validated by software, or triggered through an audited breakglass mechanism. Safe proxies are a key ZTP instrument; Google estimates ~13% of evaluated outages could have been prevented or mitigated with ZTP.
- **Tool Proxy**: Google's specific implementation — a binary exposing a generic RPC method that executes CLI commands via fork/exec, with policy-controlled access.

## Key Concepts
- **Breakglass mechanism**: Named after fire alarm pulls; bypasses normal authorization for emergency access. Should be rare, logged, and audited — culture must enforce this.
- **MPA (Multi-Party Authorization)**: A second person approves an action before execution. Proxy enforces MPA programmatically, not just culturally.
- **Rate limiting via proxy**: Dangerous operations (e.g., machine restarts) take effect gradually; limits blast radius of mistakes.
- **ACL policy**: Configuration specifying which roles can call which RPCs; proxy enforces it — target systems accept calls only from the proxy.

## Code Examples
```
config = {
  proxy_role = 'admin-proxy'
  tools = {
    borg = {
      mpm = 'client@live'
      binary_in_mpm = 'borg'
      any_command = true
      allow = ['group:admin']
      require_mpa_approval_from = ['group:admin-leads']
      unit_tests = [{
         expected = 'ALLOW'
         command = 'file.borgcfg up'
      }]
    }
  }
}
```
**What it demonstrates**: Google Tool Proxy Borg policy — allows `group:admin` to run `borg` commands, but only after `group:admin-leads` approves. Every call is logged.

## Anti-patterns
- **Proxy as single point of failure**: Mitigate by running multiple instances, ensuring all dependencies have documented SLAs and emergency contacts.
- **Overly broad policy**: Templates/automation should generate secure-by-default configs; ad hoc policies are a source of errors.
- **Proxy grants high privileges**: The proxy itself must not execute under a privileged role — it forwards identity and actions on behalf of the caller.

## Key Takeaways
1. Safe proxies add security/reliability controls to existing systems without requiring changes to those systems — cost-effective for brownfield environments.
2. The proxy's enforced logging + MPA converts security from a culture-only practice to a technically enforced invariant.
3. Zero Touch Production reduces human error in production; safe proxies are one mechanism to approach ZTP.
4. Breakglass must exist but should be rare, audited, and culturally stigmatized — not a shortcut to bypass policy.
5. Rate limiting at the proxy level bounds the blast radius of both mistakes and compromised credentials.

## Connects To
- **Ch 5**: Least privilege, MPA, and breakglass in depth
- **Ch 21**: Culture of review — breakglass culture
- **Ch 4**: Proxy design tradeoffs vs. direct connection
