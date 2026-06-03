# Chapter 6: Stitching Events into Traces

## Core Idea
A distributed trace is simply a series of interconnected structured events (spans), stitched together with shared trace IDs propagated across service hops. Understanding how traces are assembled manually demystifies distributed tracing and reveals its general applicability beyond service-to-service calls.

## Frameworks Introduced
- **Trace assembly model**: Each span needs five required fields: trace ID, span ID, parent ID, timestamp, duration. Additional service name and span name fields improve usability.
- **Context propagation**: The trace ID and parent span ID are passed downstream via HTTP headers (e.g., B3 standard: `X-B3-TraceId`, `X-B3-ParentSpanId`) so child spans can record their relationship to the parent.

## Key Concepts
- **Trace**: A representation of a complete request's journey through a distributed system. Identifies bottlenecks and shows component relationships.
- **Span**: One unit of work within a trace. Can be root span (no parent) or nested (has parent ID). Forms parent-child relationships.
- **Root span**: The top-level span; identified by the absence of a parent ID.
- **Waterfall visualization**: Spans displayed as bars showing start time and duration relative to the trace start. Quickly surfaces bottlenecks and cascade failures.
- **Trace ID**: Globally unique identifier created by the root span and propagated to all child spans to group them.
- **Context propagation standards**: W3C TraceContext, B3 — HTTP header standards for passing trace context between services.
- **Head-based vs. tail-based sampling**: Decision made at trace start (head) vs. after completion (tail). Trade-offs covered in Ch 17.

## Code Examples
```go
// Manual trace instrumentation (Go) — illustrates the core mechanics
func rootHandler(r *http.Request, w http.ResponseWriter) {
    traceData := make(map[string]interface{})
    traceData["trace_id"] = uuid.String()
    traceData["span_id"] = uuid.String()
    startTime := time.Now()
    traceData["timestamp"] = startTime.Unix()
    traceData["name"] = "/oauth2/login"
    traceData["service_name"] = "authentication_svc"
    // ... work ...
    traceData["duration_ms"] = time.Now().Sub(startTime)
    sendSpan(traceData)
}

// Propagating context to child services
func callAuthService(req *http.Request, traceData map[string]interface{}) {
    aReq.Header.Set("X-B3-TraceId", traceData["trace.trace_id"])
    aReq.Header.Set("X-B3-ParentSpanId", traceData["trace.span_id"])
}
```

## Anti-patterns
- **Vendor-specific instrumentation libraries**: Re-instrumentation required to switch vendors. Use OTel (Ch 7) instead.
- **Treating traces as service-call-only**: Tracing is equally useful for batch jobs (one span per S3 upload), Lambda pipelines, monolithic hot-path profiling.

## Key Takeaways
1. A trace is just structured events with five extra fields (trace ID, span ID, parent ID, timestamp, duration) and context propagation headers.
2. Any set of correlated events can be stitched into a trace — this is not limited to microservice RPC calls.
3. The five required fields are non-negotiable; additional tags are optional but valuable.
4. Context propagation (passing trace/span IDs downstream via headers) is the mechanism that enables multi-service trace assembly.
5. Production tracing libraries (OTel) automate all this boilerplate — but understanding it enables non-standard use cases.

## Connects To
- **Ch 5**: Traces are structured events with relationship metadata added.
- **Ch 7**: OTel provides the production-grade implementation of what this chapter builds manually.
- **Ch 17**: Sampling decisions for traces require understanding head-based vs. tail-based approaches.
