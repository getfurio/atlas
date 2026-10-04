# Atlas

Architecture maps of open source systems you already know, written as
[Furio](https://github.com/getfurio/furio) manifests: every component, what it depends on, and
what stops working when one of them goes down.

Each map is **unofficial**: reconstructed from the public sources of a tagged release, and not
affiliated with or endorsed by the project it describes.

## Systems

| System | Release | Components | Manifest | Sources |
| --- | --- | --- | --- | --- |
| [OpenTelemetry Demo](https://github.com/open-telemetry/opentelemetry-demo) | 3.1.0 | 28 | [architecture.yaml](systems/opentelemetry-demo/opentelemetry-demo/.architecture/architecture.yaml) | [SOURCES.md](systems/opentelemetry-demo/SOURCES.md) |
| [Supabase](https://github.com/supabase/supabase/tree/master/docker), self-hosted | commit `7353782` | 14 | [architecture.yaml](systems/supabase/supabase/.architecture/architecture.yaml) | [SOURCES.md](systems/supabase/SOURCES.md) |

## The map

**[getfurio.github.io/atlas](https://getfurio.github.io/atlas/)**: every system, rebuilt on each
change to this repo.

Select a component and choose **Blast radius** to see what is affected if it goes down, or
**Depends on** to see what it needs. The view lives in the URL, so every answer is a link.

### OpenTelemetry Demo

| Question | View |
| --- | --- |
| What stops working if flagd, the feature flag service, goes down? | [Blast radius of flagd](https://getfurio.github.io/atlas/#/?sel=opentelemetry-demo/flagd&mode=impact) |
| What is affected if the PostgreSQL database goes down? | [Blast radius of astronomy-db](https://getfurio.github.io/atlas/#/?sel=opentelemetry-demo/astronomy-db&mode=impact) |
| What if the Kafka `orders` topic is unavailable? | [Blast radius of orders](https://getfurio.github.io/atlas/#/?sel=opentelemetry-demo/orders&mode=impact) |
| What if the cart store (Valkey) goes down? | [Blast radius of valkey-cart](https://getfurio.github.io/atlas/#/?sel=opentelemetry-demo/valkey-cart&mode=impact) |
| What does placing an order need? | [What checkout depends on](https://getfurio.github.io/atlas/#/?sel=opentelemetry-demo/checkout&mode=depends) |

Telemetry is on the map as non-critical relations, drawn dotted: every service exports to the
Collector and keeps working without it, so the blast radius does not follow them.

### Supabase, self-hosted

| Question | View |
| --- | --- |
| What stops working if Postgres goes down? | [Blast radius of db](https://getfurio.github.io/atlas/#/?sel=supabase/db&mode=impact) |
| What is affected if PostgREST goes down? | [Blast radius of rest](https://getfurio.github.io/atlas/#/?sel=supabase/rest&mode=impact) |
| What does Storage need to work? | [What storage depends on](https://getfurio.github.io/atlas/#/?sel=supabase/storage&mode=depends) |

### Build it yourself

With Node 22 or later:

```bash
npx @getfurio/cli build --workspace atlas --site --out _site systems/*/*/
```

```bash
npx serve _site
```

## How a system is mapped

```
systems/
  <system>/
    SOURCES.md                 the upstream release, the files read, the choices made
    <repo>/.architecture/
      architecture.yaml        project: <system>
      diagrams/*.mmd
```

One Furio project per system. When the system lives in several repos upstream, it has several
folders here, each declaring what that repo owns.

The rules, because a wrong map of a well-known project helps nobody:

- A manifest is pinned to an upstream release, a tag or a commit, written in `SOURCES.md`.
- Every component and every relation comes from a file of that release, and `SOURCES.md` names
  the file. Nothing is written from memory.
- What cannot be verified stays out.
- A dependency a component keeps working without (telemetry, metric scrapes) is a relation with
  `critical: false`.
- Descriptions and diagrams are written from scratch; no upstream text or drawing is copied.
- Project names describe what is mapped. No logos.

## Contributing

Corrections are welcome: open a pull request that changes the manifest and names the upstream
file that proves it. To add a system, open an issue first. Every pull request is checked by
`furio validate`; run it yourself with:

```bash
npx @getfurio/cli validate systems/*/*/
```

Maintainers of a mapped project: if the map is wrong, or you would rather keep the manifest in
your own repo, open an issue.

## License

[MIT](LICENSE). The names of the mapped projects belong to their owners.
