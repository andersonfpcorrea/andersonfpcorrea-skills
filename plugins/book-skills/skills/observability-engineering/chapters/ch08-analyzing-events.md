# Chapter 8: Analyzing Events to Achieve Observability

## Core Idea
The **core analysis loop** is the formal workflow for debugging from first principles — methodically eliminating possibilities using data rather than intuition, enabling any engineer to diagnose any issue in any system without prior system familiarity.

## Frameworks Introduced
- **Core analysis loop** (4 stages):
  1. Start with the overall context (what did the alert or customer tell you?).
  2. Verify: is a notable change in performance actually happening? Use visualization to see behavior change in curves.
  3. Search for dimensions that might drive the change: (a) examine sample rows for outliers; (b) slice across dimensions looking for patterns — try `GROUP BY` on common fields like `status_code`; (c) filter for specific dimensions/values to expose outliers.
  4. Know enough? If yes, done. If not, filter view to isolate this area as your new starting point → return to step 3.

- **BubbleUp pattern** (automated core analysis loop): Select an anomalous region in a heatmap → compute all dimension values inside the region vs. outside (baseline) → diff and sort by percent difference → surface ranked list of dimensions most correlated with the anomaly.

## Key Concepts
- **Debugging from first principles**: Treating every problem as novel; forming hypotheses and validating with data rather than pattern-matching to past problems.
- **First principle**: A basic assumption not deduced from another assumption; the starting point of a scientific investigation.
- **Brute-force dimension search**: Cycling through all available dimensions to find those correlating with an anomaly — computers do this; humans direct it.
- **AIOps**: Algorithms for anomaly detection and alert noise reduction. The authors argue AIOps cannot reliably define anomaly baselines in rapidly changing production environments.
- **Human-machine combination**: Computers churn data to find patterns; humans apply cognitive context to determine whether a pattern is significant.

## Mental Models
- Think of the core analysis loop as a scientific method for production: hypothesis → data → validate or invalidate → next hypothesis.
- Observability tools automate the brute-force portion; humans provide direction and context.
- An AZ-wide infrastructure issue manifests as a dimension `availability_zone=us-east-1a` appearing in 98% of anomalous events vs. 17% of baseline — no prior knowledge of the AZ issue needed.

## Anti-patterns
- **Debugging from known conditions with observability data**: Collecting event data but then grepping it with known strings or piping to infinite dashboards — same reactive approach, different data format.
- **Relying on AIOps to define anomalies automatically**: Production baselines change constantly (new features, fixes, optimizations); AI-drawn boxes are too large or too small in innovative environments.
- **Manual brute-force dimension search at scale**: Cycling through dozens of dimensions by hand when the system has hundreds of them is impractical; automation is required.

## Key Takeaways
1. The core analysis loop requires only arbitrarily wide structured events — it cannot be done with pre-aggregated metrics or unstructured logs.
2. Debugging from first principles means: no guessing, no intuition-based jumping to conclusions, systematic data-following every time.
3. The question "what dimensions are different between the anomaly and the baseline?" can be automated; a human then judges which differences matter.
4. Human intelligence handles ever-changing baselines and context; machine intelligence handles processing billions of rows.
5. Any engineer who follows the core analysis loop can diagnose any system — system familiarity is not required.

## Connects To
- **Ch 5**: Arbitrarily wide structured events are the prerequisite for the core analysis loop.
- **Ch 12**: The core analysis loop is how you respond to SLO-based alerts.
- **Ch 16**: The data store must return query results fast enough to sustain iterative investigation.
