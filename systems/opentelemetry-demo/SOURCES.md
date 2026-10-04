# OpenTelemetry Demo: sources

Unofficial map, reconstructed from public sources. Not affiliated with the OpenTelemetry project.

- Upstream: https://github.com/open-telemetry/opentelemetry-demo
- Release: `3.1.0` (commit `dedc0178918e260823323b8d95005a8cb924b007`, published 2026-09-18)
- Read on: 2026-10-04

## Scope

The stack that `make start` runs: `compose.yaml` + `compose.full.yaml` +
`compose.observability.yaml` (see `DOCKER_COMPOSE_FILES` in the `Makefile`).

Left out: the optional stacks `compose.agent.yaml` (agent, mcp, chatbot),
`compose.profiling.yaml` (eBPF profiler, firepit), `compose.tests.yaml`, the React Native app,
and the Kubernetes deployment.

## Files read

| What | File |
| --- | --- |
| Components, dependencies, environment | `compose.yaml`, `compose.full.yaml`, `compose.observability.yaml`, `.env`, `Makefile` |
| Proxy routes | `src/frontend-proxy/envoy.tmpl.yaml` |
| Calls from the frontend | `src/frontend/gateways/rpc/*`, `src/frontend/gateways/http/Shipping.gateway.ts` |
| Calls and order of the checkout | `src/checkout/main.go`, `src/checkout/kafka/producer.go` |
| gRPC services | `pb/demo.proto` |
| Kafka consumers | `src/accounting/Consumer.cs`, `src/fraud-detection/src/main/kotlin/frauddetection/main.kt` |
| Database schemas and grants | `src/postgresql/init.sql`, `src/product-catalog/main.go` |
| Other calls | `src/recommendation/recommendation_server.py`, `src/shipping/src/shipping_service/quote.rs`, `src/load-generator/locustfile.py` |
| Collector pipelines | `src/otel-collector/otelcol-config.yml`, `otelcol-config-full.yml`, `otelcol-config-observability.yml` |
| Observability backends | `src/jaeger/config.yml`, `src/prometheus/prometheus-config.yaml`, `src/grafana/provisioning/datasources/` |
| OpAMP, flag UI, telemetry docs | `src/opamp-server/README.md`, `src/flagd-ui/README.md`, `src/telemetry-docs/README.md` |

## Choices

- Telemetry export (every service sends OTLP to the Collector) and the Collector's metric
  scrapes are relations with `critical: false`: a service keeps working without the Collector,
  and the Collector without a scrape target. The blast radius does not follow them. Needs
  Furio 0.4.0 or later.
- The relations to flagd come from `FLAGD_HOST` passed in the compose files, not from each
  service's code.
- The Kafka broker and its `orders` topic are one component (`orders`).
