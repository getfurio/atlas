# Sentry (self-hosted): sources

Unofficial map, reconstructed from public sources. Not affiliated with Sentry.

- Upstream: https://github.com/getsentry/self-hosted
- Release: `26.9.0` (commit `667094ad0b11bb27e6380fc757754c66516b59b4`, published 2026-09-16)
- Read on: 2026-10-06

## Scope

The whole `docker-compose.yml`, in the `feature-complete` profile that `.env` sets by default.
Components that only run in that profile carry the tag `feature-complete`; the descriptions say
which processes also run with `errors-only`.

The 55 compose services are 26 components: the Sentry consumers, the post-process forwarders,
the Snuba consumers and the Snuba subscription executors are one component each, since the
processes of each group share image, configuration and dependencies and differ only in the
topic they read. Each description lists the processes.

Left out: `optional-modifications/` (not applied by the installer), the external symbol servers
Symbolicator may fetch from, and where the SMTP container delivers the mail.

## Files read

| What | File |
| --- | --- |
| Services, profiles, shared dependencies and environment | `docker-compose.yml` (`x-sentry-defaults`, `x-snuba-defaults`), `.env` |
| Entry point routes | `nginx.conf` |
| Sentry's stores and services | `sentry/sentry.conf.example.py` (`DATABASES`, Redis, Memcached, Kafka, nodestore), `sentry/config.example.yml` (mail, filestore, Symbolicator, profiles bucket) |
| Relay | `relay/config.example.yml` |
| Task broker topics | `taskbroker/config.yml` |
| Symbolicator | `symbolicator/config.example.yml` |
| Buckets | `install/bootstrap-s3-nodestore.sh` |

## Choices

- Kafka is one component with its topics in the description; the relations say which topics
  where it matters.
- Every Sentry process (web, consumers, forwarders, task scheduler and worker, cleanup) gets the
  dependencies of `x-sentry-defaults` and of the Sentry settings: PgBouncer, Redis, Memcached,
  Snuba API, SeaweedFS and the data volume. Symbolicator only for the web app and the ingest
  consumers, SMTP only for the processes that send mail.
- `taskscheduler → kafka` (publishes) and `launchpad-taskworker → kafka` (depends_on) come from
  the compose dependencies and the broker's topics, not from those services' code.
