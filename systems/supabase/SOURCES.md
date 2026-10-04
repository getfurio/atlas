# Supabase (self-hosted): sources

Unofficial map, reconstructed from public sources. Not affiliated with Supabase.

- Upstream: https://github.com/supabase/supabase, folder `docker/`
- Commit: `7353782724d837be316f0f6f87471ed176ab4bfd` (master, 2026-10-03). The self-hosting
  stack is not released by tag: the guide tells you to clone the repo.
- Read on: 2026-10-04

## Scope

The default stack of `docker/docker-compose.yml`: 11 services, plus the two host folders they
share and the SMTP server you configure.

Left out: the optional overrides that `run.sh config add` layers on top (`logs` with Logflare
and Vector, `s3`, `rustfs`, `kong`, `nginx`, `caddy`, `pgbouncer`, `pg15`), and the hosted
platform, which is not what this folder describes.

## Files read

| What | File |
| --- | --- |
| Services, connection strings, environment, volumes | `docker/docker-compose.yml`, `docker/.env.example` |
| What is included, overrides | `docker/README.md`, `docker/run.sh` |
| Gateway routes and clusters | `docker/volumes/api/envoy/lds.template.yaml`, `docker/volumes/api/envoy/cds.yaml` |
| Pooler tenant | `docker/volumes/pooler/pooler.exs` |
| Database init scripts | `docker/volumes/db/` |
| Functions entry point | `docker/volumes/functions/main/index.ts` |

## Choices

- One folder, although each service has its own repo upstream: the wiring mapped here is
  defined in `supabase/supabase`, and the code of the services was not read. Each component
  links to its repo.
- Edge Functions receive `SUPABASE_URL` and `SUPABASE_DB_URL`, but what a function calls is up
  to its code: no relation to the gateway or the database is declared for them.
- Studio receives the Postgres host and password; only its calls to postgres-meta
  (`STUDIO_PG_META_URL`) and to the gateway (`SUPABASE_URL`) are declared.
- `tech` is set only where the compose file or the README states it.
