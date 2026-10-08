# Harbor: sources

Unofficial map, reconstructed from public sources. Not affiliated with the Harbor project or the
CNCF.

- Upstream: https://github.com/goharbor/harbor
- Release: `v2.15.3` (commit `fb476e10c9f981bd994011563bef6c5292ec272c`, published 2026-10-06)
- Read on: 2026-10-08

## Scope

What the installer (`make/install.sh` with `prepare`) runs from `harbor.yml`: the compose file is
generated from `make/photon/prepare/templates/docker_compose/docker-compose.yml.jinja`. The
default install has no scanner and no metrics; the Trivy adapter (`--with-trivy`) and the
exporter (`metric.enabled`) are on the map with the tag `optional`. Postgres and Redis are the
bundled ones; `harbor.yml` can point to external ones instead.

Left out: the Helm chart (`goharbor/harbor-helm`), internal TLS between the containers, the
tracing options (Jaeger, OTel), proxy-cache and replication targets outside the stack.

## Files read

| What | File |
| --- | --- |
| Services, dependencies, volumes | `make/photon/prepare/templates/docker_compose/docker-compose.yml.jinja` |
| Installer options and defaults | `make/install.sh`, `make/harbor.yml.tmpl` |
| Entry point routes | `make/photon/prepare/templates/nginx/nginx.http.conf.jinja` |
| Core's services | `make/photon/prepare/templates/core/env.jinja` |
| Job service | `make/photon/prepare/templates/jobservice/env.jinja`, `config.yml.jinja` |
| Registry | `make/photon/prepare/templates/registry/config.yml.jinja`, `registryctl/env.jinja` |
| Scanner and exporter | `make/photon/prepare/templates/trivy-adapter/env.jinja`, `exporter/env.jinja` |
| Storage drivers | `make/photon/prepare/utils/configs.py` |

## Choices

- The relations to the log container (every container's Docker syslog driver) are
  `critical: false`.
- The registry storage, the database files, Redis and the job logs are one component, the
  `/data` volume of the host, since that is what `harbor.yml` configures (`data_volume`); with
  an object storage driver the registry part would be a different component.
- The browser and the Docker client are not components; their path is in the diagram.
