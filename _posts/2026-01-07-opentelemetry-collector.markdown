---
layout: post
title:  "Understanding the OpenTelemetry Collector"
description: An introduction to how the OpenTelemetry Collector works, providing observability through metrics, traces, and logs.
date:   2026-01-07 10:00:00 +0500
categories: OpenTelemetry Observability Monitoring
---

An introduction to how the OpenTelemetry Collector works, providing observability through metrics, traces, and logs.

### Overview

The OpenTelemetry Collector is a vendor-agnostic telemetry pipeline that sits between your application and observability backends. Think of it as a **mail sorting facility**: applications drop off telemetry data (mail), the collector sorts and processes it, then delivers it to the appropriate backend services.

```mermaid
flowchart LR
    APP[Application<br/>SDK] -->|OTLP| COLLECTOR[OTel Collector<br/>Mail Sorting Facility]
    COLLECTOR -->|Processed Data| BACKEND[Observability<br/>Backend]
    
    style COLLECTOR fill:#4A90E2,stroke:#333,stroke-width:2px
```

### Core Concepts

#### 1. Receivers (Mailboxes 📬)

Receivers are entry points for telemetry data into the collector. Think of them as **mailboxes** where applications drop off their data. They listen on specific ports and protocols.

**Example setup:**
```yaml
receivers:
  otlp:
    protocols:
      grpc:
        endpoint: localhost:4317  # Mailbox for gRPC connections
      http:
        endpoint: localhost:4318  # Mailbox for HTTP connections
```

The OTLP (OpenTelemetry Protocol) receiver is the main mailbox where instrumented applications drop off their telemetry using the OpenTelemetry SDK.

#### 2. Processors (Mail Sorters ⚙️)

Processors transform, filter, or enrich telemetry data as it flows through the pipeline. Think of them as **mail sorters** who organize, bundle, and prepare data for delivery. They execute in order.

**Batch Processor**

Groups telemetry data into batches before exporting, reducing network overhead and improving throughput. Think of this as **bundling letters together** instead of sending them one at a time.

```yaml
processors:
  batch:
    send_batch_size: 1000    # Bundle when 1000 letters collected
    timeout: 60s             # Or bundle after 60 seconds, whichever comes first
```

**Key parameters:**
- `send_batch_size`: Maximum letters per bundle
- `send_batch_max_size`: Hard limit (prevents the sorting room from overflowing)
- `timeout`: Maximum wait time before sending the bundle

**Memory Limiter**

Prevents the collector from running out of memory by dropping data when limits are approached. Think of this as a **capacity manager** who stops accepting new mail when the facility is too full.

```yaml
processors:
  memory_limiter:
    check_interval: 1s
    limit_percentage: 80      # Use max 80% of available storage space
    spike_limit_percentage: 30 # Allow 30% temporary overflow
```

**Resource Detection**

Automatically detects and adds resource attributes (cloud provider, region, instance info). Like **adding a return address** to each piece of mail automatically.

```yaml
processors:
  resourcedetection:
    detectors: [gcp, ec2, azure]  # Detect cloud platform resources
    timeout: 10s
```

**Transform Processor**

Modifies telemetry data using a declarative transformation language. Think of this as **rewriting addresses** to match the destination's requirements.

```yaml
processors:
  transform/collision:
    metric_statements:
      - context: datapoint
        statements:
          # Rewrite addresses that would conflict at destination
          - set(attributes["exported_location"], attributes["location"])
          - delete_key(attributes, "location")
```

#### 3. Exporters (Delivery Trucks 🚚)

Exporters send processed telemetry data to backend systems. These are the **delivery trucks** that take bundled mail to its final destination.

**Example exporters:**
```yaml
exporters:
  prometheus:
    # Delivery truck to Prometheus (metrics)
    
  otlp:
    endpoint: observability-backend:4317
    # Delivery truck to another OTLP endpoint
    
  logging:
    # Delivery truck to console/logs (for debugging)
```

#### 4. Connectors (Mail Inspectors 🔄)

Connectors create new telemetry from existing telemetry. Think of them as **inspectors who count and categorize mail**, then create summary reports. They act as both exporters and receivers.

**Span Metrics Connector**

Generates metrics from trace spans, providing RED (Rate, Errors, Duration) metrics automatically. Every 60 seconds, it creates a **summary report** of all the mail (spans) that passed through.

```yaml
connectors:
  spanmetrics:
    aggregation_temporality: CUMULATIVE
    metrics_flush_interval: 60s  # Create summary report every 60 seconds
    dimensions:
      - name: rpc.method
      - name: rpc.service
```

**Example reports generated:**
- `traces_span_metrics_calls_total`: "We processed 1,247 SELECT database calls this minute"
- `traces_span_metrics_duration_milliseconds`: "95% of connect calls took less than 50ms"

#### 5. Pipelines (Mail Routes 🛤️)

Pipelines connect receivers → processors → exporters to define data flow paths. Think of these as the **routes that different types of mail take** through the facility.

```yaml
service:
  pipelines:
    # Route 1: Application metrics from SDK
    metrics/otlp:
      receivers: [otlp]           # Mailbox
      processors: [               # Sorting steps
        transform/collision,      # Rewrite addresses
        resourcedetection,        # Add return address
        memory_limiter,          # Check capacity
        batch/metrics            # Bundle together
      ]
      exporters: [prometheus]  # Delivery truck
    
    # Route 2: Metrics derived from traces (inspector reports)
    metrics/spanmetrics:
      receivers: [spanmetrics]    # Inspector's outbox
      processors: [resourcedetection, memory_limiter, batch/spanmetrics]
      exporters: [prometheus]
    
    # Route 3: Distributed traces
    traces:
      receivers: [otlp]
      processors: [resourcedetection, memory_limiter, batch]
      exporters: [otlp, spanmetrics]  # Dual delivery: storage + inspector
```

### Data Flow Example

#### Metrics Pipeline

```mermaid
sequenceDiagram
    participant App as Application
    participant SDK as OTel SDK
    participant Receiver as OTLP Receiver
    participant Processor as Batch Processor
    participant Exporter as Exporter
    participant Backend as Observability<br/>Backend
    
    App->>SDK: Record metric
    SDK->>SDK: Accumulate (60s)
    SDK->>Receiver: Export batch via OTLP
    Receiver->>Processor: Forward metrics
    Processor->>Processor: Batch (60s)
    Processor->>Exporter: Send batch
    Exporter->>Backend: Write metrics
```

#### Trace to Metrics Pipeline

```mermaid
sequenceDiagram
    participant App as Application
    participant Traces as Traces Pipeline
    participant SpanMetrics as Span Metrics<br/>Connector
    participant Metrics as Metrics Pipeline
    participant Backend as Observability<br/>Backend
    
    App->>Traces: Send trace span
    Traces->>SpanMetrics: Forward span
    Traces->>Backend: Export trace
    SpanMetrics->>SpanMetrics: Aggregate (60s)
    SpanMetrics->>Metrics: Generate metrics
    Metrics->>Backend: Export metrics
```
