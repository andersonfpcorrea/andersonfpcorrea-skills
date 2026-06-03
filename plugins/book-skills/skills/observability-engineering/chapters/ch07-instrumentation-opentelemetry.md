# Chapter 7: Instrumentation with OpenTelemetry

## Core Idea
OpenTelemetry (OTel) is the open source, vendor-neutral standard for application instrumentation. Instrument once with OTel and send telemetry to any backend. Automatic instrumentation provides a skeleton; custom instrumentation adds business-logic context that makes observability powerful.

## Key Concepts
- **OpenTelemetry (OTel)**: Merger of OpenTracing (2016) and OpenCensus (2017), released 2019 under CNCF. Supports Go, Python, Java, Ruby, Erlang, PHP, JavaScript, .NET, Rust, C++, Swift.
- **API**: Spec portion; lets developers add instrumentation without concern for underlying implementation.
- **SDK**: Concrete implementation; tracks state and batches data for transmission.
- **Tracer**: SDK component tracking the currently active span in a process.
- **Meter**: SDK component tracking available metrics and allowing updates.
- **Context propagation**: Deserializes inbound context (W3C TraceContext, B3), tracks current request context, serializes for downstream. Automatic in Java/.NET agent; explicit in Go.
- **Exporter**: Plug-in translating OTel in-memory objects to destination format (Jaeger, Honeycomb, Lightstep, stdout, etc.).
- **Collector**: Standalone binary (sidecar/proxy) receiving OTLP-format telemetry, processing, and fanning out to multiple backends.
- **OTLP (OpenTelemetry Protocol)**: The default wire protocol for OTel data transmission.
- **Automatic instrumentation**: OTel wrappers/interceptors for HTTP, gRPC, database calls auto-generate spans for common frameworks.
- **Custom instrumentation**: Developer-added spans and attributes for business-logic visibility.

## Code Examples
```go
// Automatic HTTP instrumentation (Go)
import "go.opentelemetry.io/contrib/instrumentation/net/http/otelhttp"
mux.Handle("/route", otelhttp.NewHandler(
    otelhttp.WithRouteTag("/route", http.HandlerFunc(h)), "handler_span_name"))

// Starting/finishing a custom span
var tr = otel.Tracer("module_name")
func funcName(ctx context.Context) {
    sp := tr.Start(ctx, "span_name")
    defer sp.End()
    // do work
}

// Adding wide fields to active span
import "go.opentelemetry.io/otel/attribute"
sp.SetAttributes(attribute.Int("http.code", resp.ResponseCode))
sp.SetAttributes(attribute.String("app.user", username))

// OTLP gRPC exporter (preferred default)
driver := otlpgrpc.NewClient(
    otlpgrpc.WithTLSCredentials(credentials.NewClientTLSFromCert(nil, "")),
    otlpgrpc.WithEndpoint("my.backend.com:443"),
)
otExporter, _ := otlp.New(ctx, driver)
tp := sdktrace.NewTracerProvider(
    sdktrace.WithSampler(sdktrace.AlwaysSample()),
    sdktrace.WithResource(resource.NewWithAttributes(
        semconv.SchemaURL, semconv.ServiceNameKey.String(serviceName))),
    sdktrace.WithBatcher(otExporter))
```

## Anti-patterns
- **Vendor-specific agents as primary instrumentation**: Creates lock-in; re-instrumentation required to switch backends.
- **Metrics for per-request business data**: Prefer adding fields to the active trace span (e.g., `value_in_cart`) over emitting a separate metric. Use metrics only for process-wide values that are not request-scoped.
- **Multiple custom exporters when OTLP is available**: Prefer the OTLP exporter to a Collector over vendor-specific exporters.

## Key Takeaways
1. OTel is the instrument-once standard: send to Honeycomb, Jaeger, Lightstep, or any other backend with configuration changes only.
2. Start with automatic instrumentation to get service topology quickly; add custom instrumentation for business logic context.
3. Every pull request should ask: "How will I know if this change is working as intended?" — custom instrumentation is the answer.
4. Prefer adding business data as attributes on trace spans over emitting separate metrics.
5. Use the OTLP gRPC exporter as the default; route via the OTel Collector for multi-backend fan-out.

## Connects To
- **Ch 6**: OTel implements the manual trace assembly patterns from Ch 6.
- **Ch 10**: Start with OTel instrumentation when beginning your observability journey to avoid vendor lock-in.
- **Ch 18**: The OTel Collector is the ingestion point for telemetry pipeline management.
