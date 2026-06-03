# Chapter 12: Writing Code

## Core Idea
The best way to prevent security vulnerabilities and reliability issues at scale is to make them structurally impossible via hardened frameworks and safe-by-default libraries — shifting the burden from individual developer vigilance to platform-level enforcement.

## Frameworks Introduced
- **Security/Reliability via Frameworks**: Implement auth, logging, rate limiting, retry logic, and common security controls in shared frameworks. Each service uses the framework; domain experts fix issues in one place, removing them from all services simultaneously.
- **RPC Backend Interceptor Pattern**: A pipeline of pre/post interceptors around each RPC handler: Logging → Authentication → Authorization → Throttling → [RPC Logic] → (reverse order post). Interceptors share state via a context object. Each interceptor runs pre/post stages; an error in any interceptor prevents further execution but still runs all post stages in reverse.
- **Simplicity as Security**: Fewer lines of code, fewer abstractions, fewer nesting levels → fewer places for bugs to hide and fewer surprise interactions.

## Key Concepts
- **TrustedSqlString**: A type that can only be constructed from developer-controlled strings (not raw user input). The compiler enforces the SQL injection invariant at the type level — no runtime check needed, no developer vigilance required.
- **SafeHtml**: A type representing HTML content that is safe to render — either static developer strings or content processed through a sanitizer. Prevents XSS by making unsafe HTML unrepresentable in the type system.
- **Type-enforced security invariants**: Use strong types to make entire classes of vulnerabilities impossible. The invariant "only sanitized values reach security-sensitive APIs" becomes a compile-time guarantee, not a code review hope.
- **YAGNI smells / technical debt**: Unused code paths, overly generic abstractions, and accumulated debt create hidden attack surface. Regularly refactor and eliminate.
- **Avoiding multilevel nesting**: Deeply nested conditionals hide error-handling paths where security checks are often accidentally omitted.
- **Sanitize your code**: Static analysis tools that flag dangerous patterns; type systems that prevent dangerous constructs; linters configured to match security policies.
- **Framework rollout strategy**: Adopt new security frameworks incrementally — start with new code, migrate old code over time. Use automated migration tooling for large codebases.

## Code Examples
```python
# Anti-pattern: raw string interpolation
query = "SELECT * FROM users WHERE name = '" + user_input + "'"

# Pattern: TrustedSqlString ensures only developer-controlled strings
# reach the SQL API — user input is bound via parameterized API
db.query(TrustedSqlString("SELECT * FROM users WHERE name = %s"), user_input)
```

```java
// Anti-pattern: SafeHtml violation
element.setInnerHTML(userInput);  // XSS vulnerability

// Pattern: SafeHtml only accepts sanitized content
SafeHtml safe = SafeHtmlUtils.fromTrustedString(sanitize(userInput));
element.setInnerHTML(safe.asString());
```

## Anti-patterns
- **Relying solely on code review for security**: Reviewers can't reliably detect all injection paths, especially in large codebases; frameworks remove the problem systematically.
- **Rolling your own cryptography**: Delegate to well-audited frameworks (Tink); cryptography implementation errors are catastrophic and subtle.
- **Multilevel nesting**: Every additional nesting level increases the chance of missing error handling, access checks, or cleanup paths.
- **Unused code paths (YAGNI)**: Dead code contains bugs that can be activated by future changes or discovered by attackers.

## Key Takeaways
1. Hardened frameworks beat developer vigilance at scale — one fix removes a bug class from all services simultaneously.
2. Type-enforced invariants (TrustedSqlString, SafeHtml) eliminate whole vulnerability classes at compile time rather than relying on runtime checks or review.
3. RPC interceptor pattern centralizes cross-cutting concerns (auth, logging, throttling) and makes them uniformly applied to every handler.
4. Simplicity is a security property — every line of code is potential attack surface; eliminate YAGNI, multilevel nesting, and accumulated debt.
5. Never implement your own cryptography — use Tink or an equivalent well-audited library.

## Connects To
- **Ch 6**: Understandability — frameworks that enforce invariants by design
- **Ch 13**: Testing code — unit tests for security, fuzzing
- **Ch 4**: Design tradeoffs — frameworks as early investment in sustained velocity
- **Ch 21**: Culture of security by default — frameworks enable it
