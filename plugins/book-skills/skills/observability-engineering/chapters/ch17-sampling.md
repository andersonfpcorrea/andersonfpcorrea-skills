# Chapter 17: Cheap and Accurate Enough: Sampling

## Core Idea
Sampling selects representative events to transmit rather than every event, preserving full cardinality on selected events while reducing storage/transmission cost. Unlike pre-aggregation, sampled events retain the ability to ask any question of the data — you just have fewer data points to ask it of.

## Frameworks Introduced
- **Sampling strategy taxonomy**:
  1. **Constant-probability (fixed-rate)**: Keep 1 in N events regardless of content. Simple; risks missing long-tail errors at high sample rates.
  2. **Traffic-volume-based (dynamic rate)**: Adjust rate based on recent traffic volume. Prevents backend saturation. Requires weighted aggregation.
  3. **Key-based (content-based)**: Set rate based on event content (HTTP status, customer tier). Ensures errors are sampled at higher rates than successes.
  4. **Per-key + historical (dynamic per key)**: Combine key identification with volume tracking per key. Fair allocation across all unique keys.
  5. **Target rate sampling**: Automate sample rate adjustment to hit a target events/second. Removes manual rate management.

- **Head-based vs. tail-based sampling**:
  - **Head-based**: Decision at trace start; propagated downstream via header. Use when sampling criteria are known at request initiation (customer ID, endpoint).
  - **Tail-based**: Decision after all spans complete; requires buffering. Use when criteria depend on outcome (error status, total latency).
  - **Buffered (collector-side)**: Enables deferring decision until full trace assembled — combines advantages of both. Computationally expensive; typically requires external collector.

## Code Examples
```go
// Fixed-rate with sample rate recorded
var sampleRate = flag.Int("sampleRate", 1000, "")
func handler(resp http.ResponseWriter, req *http.Request) {
    r := rand.Float64()
    if r < 1.0 / float64(*sampleRate) {
        RecordEvent(req, *sampleRate, start, err)  // pass sampleRate to backend
    }
}

// Target rate (auto-adjusting) sample rate
var targetEventsPerSec = flag.Int("targetEventsPerSec", 5, "")
var sampleRate float64 = 1.0
// In goroutine every minute:
newSampleRate = *requestsInPastMinute / (60 * *targetEventsPerSec)
sampleRate = max(1.0, newSampleRate)

// Consistent sampling (trace-aware): read Sampling-ID from upstream headers
if r, err := floatFromHexBytes(req.Header.Get("Sampling-ID")); err != nil {
    r = rand.Float64()  // root span: generate random
}
// Propagate r downstream via Sampling-ID header
```

## Key Concepts
- **Sample rate recording**: Always embed `sampleRate` in the sampled event so the backend can correctly weight aggregations.
- **Consistent sampling**: Use a centrally generated random value (trace ID or Sampling-ID) rather than independently sampling in each service. Ensures all spans of a sampled trace are captured.
- **Weighted aggregation**: When sample rates vary, you cannot average raw event counts. Multiply each event by its `sampleRate` to reconstruct true distribution.
- **Median example pitfall**: For samples `[{1, rate=5}, {3, rate=2}, {7, rate=9}]`, naive median = 3; correct median (weighted) = 7.
- **dynsampler-go**: Go library that handles complex sampling logic including per-key target rate allocation.

## Anti-patterns
- **Pre-aggregated metrics as a substitute for sampled events**: Aggregation collapses cardinality permanently; sampling preserves it on each retained event.
- **Not recording the sample rate**: Backend cannot correctly reconstruct distributions; counts will be systematically wrong.
- **Constant-probability sampling when errors are rare**: Low-volume error events will not be reliably sampled; use higher rates for error/outlier events.

## Key Takeaways
1. Sampled events preserve full cardinality; pre-aggregated metrics do not. This is the key difference.
2. Always record the `sampleRate` in each sampled event; the backend requires it for accurate reconstruction.
3. Consistent sampling (using upstream Sampling-ID) ensures complete traces are sampled or dropped together.
4. Multiple sample rates per service (normal vs. outlier) are standard — errors should be sampled at much higher rates than successes.
5. Use head-based sampling for known-at-start criteria; tail-based (via external collector) for outcome-based criteria like latency or error status.

## Connects To
- **Ch 16**: Sampling reduces the volume that the data store must handle.
- **Ch 18**: Telemetry pipelines can implement collector-side tail sampling.
- **Ch 6**: Consistent sampling is critical for maintaining complete distributed traces.
