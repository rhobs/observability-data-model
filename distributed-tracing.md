# Distributed tracing and OpenTelemetry

Protocols and semantic conventions documentation for Red Hat OpenShift distributed tracing (RHOSDT) and Red Hat build of OpenTelemetry products.

## Ingestion protocols

The following distributed tracing  protocols can be ingested:
* OpenTelemetry protocol (OTLP) - gRPC/protobuf, HTTP/protobuf, HTTP/json
* Jaeger - gRPC, thrift_http, thrift_compact, thrift_binary
* Zipkin - HTTP/v1-thrift, HTTP/v1-json, HTTP/v2-json, HTTP/v2-protobuf
* OpenCensus - gRPC

The Red Hat build of OpenTelemetry also supports protocols for metrics and logs via various [receivers](https://github.com/os-observability/redhat-opentelemetry-collector/blob/main/manifest.yaml).

## Semantic conventions

The supported visualization console (Jaeger UI) does not require any standard set of semantic attributes to be present in the ingested data.
However, the product team recommends customers using instrumentation that is compatible with [OpenTelemetry semantic attributes](https://github.com/open-telemetry/semantic-conventions/tree/main/docs).

### Span metrics connector / Service performance monitor

The Jaeger monitor tab requires [spanmetricsconnector](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/connector/spanmetricsconnector)
which uses OpenTelemetry semantic conventions ([HTTP](https://opentelemetry.io/docs/specs/semconv/http/http-spans/)) to derive request count, duration and error metrics (RED).

### Resource attributes

We recommend customers to use [k8sattributesprocessor](https://github.com/open-telemetry/opentelemetry-collector-contrib/tree/main/processor/k8sattributesprocessor) which
attaches [OpenTelemetry Kubernetes resource attributes](https://github.com/open-telemetry/semantic-conventions/blob/main/docs/resource/k8s.md) to all data going through the collector.

### Red Hat semantic attribute extension

None.
